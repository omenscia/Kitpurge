https://kitpurge.replit.app

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

https://kitpurge.replit.app