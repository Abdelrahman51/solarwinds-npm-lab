# 📦 SolarWinds NPM — Installation Guide

## Prerequisites

Before installing SolarWinds Orion NPM, ensure the following:

### Server Requirements
| Component | Minimum | Recommended |
|---|---|---|
| OS | Windows Server 2016 | Windows Server 2019/2022 |
| CPU | 4 cores | 8+ cores |
| RAM | 8 GB | 16 GB |
| Disk | 50 GB | 150 GB (SSD) |
| .NET Framework | 4.8 | 4.8 |

### SQL Server Requirements
| Component | Requirement |
|---|---|
| SQL Version | SQL Server 2016+ |
| Authentication | Mixed Mode (SQL + Windows) |
| Collation | SQL_Latin1_General_CP1_CI_AS |

---

## Step 1 — Prepare the Server

```powershell
# Enable required Windows features
Install-WindowsFeature Web-Server, Web-Asp-Net45, Web-Net-Ext45 -IncludeManagementTools

# Open required firewall ports
New-NetFirewallRule -DisplayName "Orion Web" -Direction Inbound -Port 443,80,17778 -Protocol TCP -Action Allow
New-NetFirewallRule -DisplayName "Orion Polling" -Direction Inbound -Port 161 -Protocol UDP -Action Allow
```

---

## Step 2 — Install SQL Server

1. Mount SQL Server ISO and run `setup.exe`
2. Choose **New SQL Server standalone installation**
3. Select: Database Engine Services
4. Set authentication to **Mixed Mode**
5. Set SA password and add current user as admin

---

## Step 3 — Run SolarWinds Installer

1. Download **Orion Installer** from [customerportal.solarwinds.com](https://customerportal.solarwinds.com)
2. Run as Administrator
3. Select **NPM** (and any additional modules)
4. Follow the installation wizard

---

## Step 4 — Configuration Wizard

After installation, the **Orion Configuration Wizard** launches automatically:

1. **Database Setup** — point to your SQL Server instance
2. **Website Configuration** — set port (default: 443)
3. **Service Account** — create or use existing account
4. **Polling Engine** — set polling interval (default: 120s)

---

## Step 5 — Apply License

1. Log in to Orion web console: `https://<server-ip>`
2. Navigate to **Settings → License Manager**
3. Enter your license key or activate via customer portal

---

## Step 6 — Verify Services

```powershell
# Check all Orion services are running
Get-Service -Name "SolarWinds*" | Select-Object Name, Status

# Expected services:
# SolarWinds Job Engine v2    - Running
# SolarWinds Information Service - Running
# SolarWinds Orion Module Engine - Running
# SolarWinds Syslog Service   - Running
# SolarWinds Trap Service     - Running
```

---

## Post-Installation Checklist

- [ ] Orion web console accessible via HTTPS
- [ ] SQL Server connection verified
- [ ] All SolarWinds services running
- [ ] License applied and active
- [ ] Admin user password changed from default
- [ ] SNMP trap receiver configured
- [ ] Syslog receiver configured
