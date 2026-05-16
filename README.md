# 🖥️ SolarWinds NPM — Enterprise Network Monitoring Lab

> A complete, hands-on implementation of **SolarWinds Network Performance Monitor (NPM)** in a simulated enterprise environment — covering installation, node onboarding, agent deployment, alerting, and reporting.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Lab Environment](#lab-environment)
- [Installation & Setup](#installation--setup)
- [Node Onboarding](#node-onboarding)
- [Agent Deployment](#agent-deployment)
- [Alert Configuration](#alert-configuration)
- [SWQL Queries](#swql-queries)
- [Reports & Dashboards](#reports--dashboards)
- [Screenshots](#screenshots)
- [Certifications](#certifications)

---

## Overview

This project documents a full SolarWinds NPM deployment in an enterprise lab environment. It demonstrates:

- ✅ SolarWinds Orion Platform installation and initial configuration
- ✅ SNMP-based network discovery and node onboarding
- ✅ SolarWinds Agent deployment for Windows & Linux servers
- ✅ Custom alert rules and escalation policies
- ✅ SWQL queries for advanced monitoring
- ✅ Custom dashboards and scheduled reports

**Modules Used:** NPM · SAM · IPAM (optional)  
**Platform:** SolarWinds Orion 2024.x  
**Environment:** VMware-based lab simulating a production enterprise network

---

## Lab Environment

```
┌─────────────────────────────────────────────────────┐
│                  Lab Topology                       │
│                                                     │
│   [Orion Server]  ──── [Core Switch (Cisco)]        │
│        │                      │                     │
│   [SQL Server]        ┌───────┴────────┐            │
│                       │               │             │
│                [Windows Servers]  [Linux Servers]   │
│                  (Agent-managed)   (SNMP-managed)   │
└─────────────────────────────────────────────────────┘
```

| Component         | Specs                          |
|------------------|-------------------------------|
| Orion Server     | Windows Server 2019, 16GB RAM  |
| SQL Server       | SQL Server 2019, 8GB RAM       |
| Monitored Nodes  | 10+ nodes (mixed OS)           |
| Network Devices  | Cisco IOS routers/switches     |

---

## Installation & Setup

See full guide: [`docs/01-installation-guide.md`](docs/01-installation-guide.md)

**High-level steps:**
1. Pre-requisites check (OS, .NET, SQL Server)
2. Run Orion installer → select NPM module
3. Complete Configuration Wizard (database, website, polling settings)
4. Apply license and verify services

---

## Node Onboarding

See full guide: [`docs/02-node-onboarding.md`](docs/02-node-onboarding.md)

**Methods used:**
- **Network Discovery** — subnet scan with SNMP community strings
- **Manual Add** — for specific devices with custom credentials
- **CSV Import** — bulk onboarding for large environments

**SNMP Configuration example (Cisco IOS):**
```
snmp-server community PUBLIC ro
snmp-server location Cairo-DC-Rack01
snmp-server contact noc@company.com
snmp-server enable traps
```

---

## Agent Deployment

See full guide: [`docs/03-agent-deployment.md`](docs/03-agent-deployment.md)

The SolarWinds Agent enables deeper monitoring without relying on SNMP — ideal for servers.

**Deployment methods covered:**
- Manual install via Orion web console
- Push deployment via Orion (Windows domain)
- Manual install on Linux (RPM/DEB package)

**Linux agent install example:**
```bash
# Download agent package from Orion server
wget http://<orion-server>/AgentManagement/LinuxPackage/...

# Install on Debian/Ubuntu
sudo dpkg -i solarwinds-agent.deb

# Start and enable the service
sudo systemctl start solarwinds-agent
sudo systemctl enable solarwinds-agent
```

---

## Alert Configuration

See full guide: [`docs/04-alert-configuration.md`](docs/04-alert-configuration.md)

**Alerts implemented in this lab:**

| Alert Name                    | Trigger Condition              | Action            |
|------------------------------|-------------------------------|-------------------|
| Node Down                    | Node status = Down             | Email + Log       |
| High CPU Utilization         | CPU > 85% for 5 min            | Email             |
| Interface Error Rate High    | Error rate > 1%                | Email + Syslog    |
| Disk Space Critical          | Disk usage > 90%               | Email + Ticket    |
| Network Response Time High   | Response time > 200ms          | Email             |

---

## SWQL Queries

See full collection: [`swql-queries/`](swql-queries/)

**Sample — Top 10 nodes by CPU:**
```sql
SELECT TOP 10
  N.Caption AS NodeName,
  N.CPULoad AS CPU_Percent,
  N.MemoryUsed AS MemoryUsed_Bytes
FROM Orion.Nodes N
WHERE N.CPULoad IS NOT NULL
ORDER BY N.CPULoad DESC
```

---

## Reports & Dashboards

- **Availability Report** — monthly uptime per node
- **Top Talkers Dashboard** — interfaces with highest bandwidth
- **Alert History Report** — alert frequency per device

---

## Screenshots

> Screenshots taken from the live lab environment.

| Screenshot | Description |
|---|---|
| `screenshots/01-orion-dashboard.png` | Main Orion web console |
| `screenshots/02-node-list.png` | Monitored nodes list |
| `screenshots/03-alert-manager.png` | Alert manager view |
| `screenshots/04-agent-status.png` | Agent deployment status |

---

## Certifications

| Certification | Issuer | Status |
|---|---|---|
| SolarWinds Certified Professional (SCP) | SolarWinds | ✅ Certified |

---

## Author

**Abdelrahman** — Network & Infrastructure Engineer  
Specializing in network monitoring, NOC operations, and IT infrastructure management.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue)](https://linkedin.com/in/your-profile)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black)](https://github.com/Abdelrahman51)
