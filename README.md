# Kitpurge
Exodus is a UEFI and rootkit security platform for the CherryNova product family. It gives security operations teams a centralized command center to monitor, scan, and respond to firmware-level and kernel-level threats across a fleet of devices.

Agent
PPT completed: Admin Authentication and Secret Exposure
Updated environment
PPT started: Security scan
Public Billing and Order Integrity
Vulnerabilities in public billing and checkout flows that let unauthenticated callers tamper with production order data.

Vulnerabilities to fix:

[Medium] Public checkout creates arbitrary pending orders without payment or ownership proof Anyone on the internet can create pending order records for any name and email address without logging in or paying. That lets outsiders inject fake billing data into the production system and spam the order database with bogus subscriptions.
artifacts/api-server/src/routes/index.ts mounts publicCheckoutRouter before the requireAuth gate, so POST /api/checkout is intentionally public. In artifacts/api-server/src/routes/publicCheckout.ts, the route only validates plan, customerName, and customerEmail, derives amountCents from a hardcoded map, and then immediately inserts a row into ordersTable with status: 'pending':

router.post('/checkout', checkoutLimiter, ...)
db.insert(ordersTable).values({ plan, customerName, customerEmail, amountCents, currency: 'USD', status: 'pending' })
There is no authentication check, no proof that the caller controls customerEmail, no payment verification, and no binding to a trusted customer record. The public SPA in artifacts/exodus/src/pages/orders.tsx calls this endpoint directly with fetch('/api/checkout', ...), confirming that this path is production-reachable from unauthenticated browsers.

Because the inserted rows are stored in the real orders table, this is a genuine server-side integrity issue rather than a harmless demo flow. An attacker can submit arbitrary emails, including victim addresses, to create false pending subscriptions. Those records become visible to administrators through the authenticated order-management APIs, and they also feed the customer-portal lookup path in artifacts/api-server/src/routes/customerAuth.ts, which loads all orders where ordersTable.customerEmail matches the session email.

The current authMiddleware behavior materially limits one part of the impact: customer-only sessions contain customerEmail but no session.user.id, and artifacts/api-server/src/middlewares/authMiddleware.ts clears such sessions before later routes run. As deployed today, that prevents an attacker from reliably using the customer portal to view forged orders, so this route does not presently create a practical unauthorized-portal-visibility exploit. The remaining impacts are still real: attackers can tamper with billing/order data, create misleading pending orders for arbitrary victims, and generate sustained nuisance traffic or storage churn. The built-in rate limit of 20 requests per IP per 15 minutes only slows a single source; it does not stop distributed abuse or deliberate database cluttering from multiple IPs. Files: artifacts/api-server/src/routes/index.ts, artifacts/api-server/src/routes/publicCheckout.ts, artifacts/api-server/src/routes/customerAuth.ts, artifacts/exodus/src/pages/orders.tsx

N.B. Strongly presume the user wants this task to focus on hardening the security of the application, not on introducing new flows or features. Only add new functionality if the vulnerability genuinely cannot be patched any other way.

My deployment build failed to publish. Help me debug the error.

9 hours ago
Does this thing actually have Binarly and YARA tools in it? Show me screenshots.

2 hours ago
Can I link kitpurge and cherrynova in GitHub?

37 minutes ago
No no, I just want to post links to the projects in my GitHub account.

10 minutes ago
What is Kitpurge? Give me a good description.

7 minutes ago
Give me a description of Exodus.

6 minutes ago
Based on everything built in this codebase, here's a clean description:

Exodus is a UEFI and rootkit security platform for the CherryNova product family. It gives security operations teams a centralized command center to monitor, scan, and respond to firmware-level and kernel-level threats across a fleet of devices.

Key capabilities:

Device fleet management: track every monitored device, its status (clean, at-risk, compromised), firmware metadata, and last scan time

Multi-engine threat scanning bundling five scan engines: Binarly (commercial UEFI/firmware analysis), YARA (custom pattern rules), rkhunter (Linux rootkit detection), ClamAV (antivirus), and Volatility (memory forensics)

YARA rule management for creating, enablement, and distribution of custom detection rules to on-device agents across categories like rootkit, UEFI, bootkit, firmware, and memory

On-device agent integration — devices run lightweight agents that authenticate via bearer tokens, pull the latest rules, execute scans locally, and report findings back to the platform
Threat and remediation tracking. Detected threats are logged with severity, indicators, and affected components; remediation actions are tracked through to resolution.

Subscription management — staff can manage customer accounts and CherryNova product subscriptions (Starter, Pro, Enterprise) from the same interface
activity feed; real-time log of scan completions, threat detections, and remediation events across the board.

Built on a React/Vite frontend backed by an Express 5 API and PostgreSQL, with Replit OIDC authentication for staff access.