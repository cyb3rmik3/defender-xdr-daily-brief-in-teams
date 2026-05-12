# Defender XDR Daily Brief in Teams

An automated daily security briefing from Microsoft Defender XDR delivered to a Microsoft Teams channel via Azure Logic Apps. Runs scheduled Advanced Hunting queries (KQL) and presents key security signals in a single Adaptive Card.

## What It Is 🧩
An ARM-deployable Logic App that:
- Executes daily KQL queries via Microsoft Graph Security API
- Aggregates signals across Defender workloads
- Posts a Teams Adaptive Card with key metrics and deep links

## Core components:
- Azure Logic Apps
- Managed Identity (Graph API)
- Teams connector
- Defender XDR Advanced Hunting

## How It Helps 🛡️
- Quick visibility – Snapshot of last 24h security posture
- aster triage – Highlights high alerts, threats, and gaps
- Consistency – Enforces a daily SecOps routine
- Cross-domain view – Endpoint, Email, Identity, Cloud Apps

## Daily Signals 📊
- 🚨 Alerts & AIR remediation
- 📧 Phishing & malware activity
- 🖥️ Device health & AV status
- 🧨 Critical vulnerabilities
- ☁️ Newly discovered apps
- 🔑 Privileged identities

## Key Requirements ⚙️
- Defender XDR data available (MDE, MDO, MDI, etc.)
- Managed Identity with: *ThreatHunting.Read.All* (Microsoft Graph)
- Teams API connection authorized

## Deployment 🚀
- Deploy ARM template via Azure Portal
- Authorize Teams connection
- Assign Graph permissions to Managed Identity
- Trigger Logic App to validate output in Teams
(See deployment guide for full steps)

## Customization 🔄
- Add/modify KQL queries
- Adjust schedule or time window
- Extend Adaptive Card content
- Integrate with other workflows (e.g., ticketing, alerts)

## Why It Matters 💡
Surfaces Defender XDR insights directly in Teams, reducing investigation time and driving consistent security operations.
