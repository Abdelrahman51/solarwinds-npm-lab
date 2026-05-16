# 🤖 SolarWinds Agent Deployment Guide

The SolarWinds Agent provides deeper monitoring than SNMP/WMI, with encrypted communication, firewall-friendly polling, and richer application metrics.

---

## When to Use the Agent vs SNMP

| Use Case | Recommended Method |
|---|---|
| Network devices (switches, routers) | SNMP |
| Windows servers (domain-joined) | Agent or WMI |
| Linux/Unix servers | Agent |
| Servers behind strict firewalls | Agent (outbound only) |
| Application performance monitoring | Agent + SAM |

---

## Method 1 — Push Deployment (Windows, Domain)

1. Go to **Settings → Manage Agents**
2. Click **Deploy Agent**
3. Enter hostname or IP of target Windows server
4. Select **Push to Agent** method
5. Provide domain admin credentials
6. Click **Deploy**

> Requires: WinRM enabled on target, firewall allowing TCP 17790

---

## Method 2 — Manual Install (Windows)

1. In Orion web console → **Settings → Manage Agents → Download Agent**
2. Copy installer to target server
3. Run as Administrator:

```powershell
# Silent install with Orion server specified
.\SolarWinds-Agent-x64.exe /S /OrionServer=10.10.0.5 /Port=17778
```

4. Verify agent appears in **Manage Agents** as **Pending Approval**
5. Click **Approve** to activate

---

## Method 3 — Manual Install (Linux)

### Debian / Ubuntu
```bash
# Download agent package from Orion server
wget "https://10.10.0.5/AgentManagement/LinuxPackage/solarwinds-agent.deb" \
  --no-check-certificate -O solarwinds-agent.deb

# Install package
sudo dpkg -i solarwinds-agent.deb

# Configure agent to connect to Orion
sudo /opt/solarwinds/agent/bin/configure \
  --orion-server=10.10.0.5 \
  --orion-port=17778

# Start and enable service
sudo systemctl start solarwinds-agent
sudo systemctl enable solarwinds-agent

# Check status
sudo systemctl status solarwinds-agent
```

### RHEL / CentOS
```bash
# Download RPM package
wget "https://10.10.0.5/AgentManagement/LinuxPackage/solarwinds-agent.rpm" \
  --no-check-certificate -O solarwinds-agent.rpm

# Install
sudo rpm -ivh solarwinds-agent.rpm

# Configure and start
sudo /opt/solarwinds/agent/bin/configure --orion-server=10.10.0.5
sudo systemctl start solarwinds-agent
sudo systemctl enable solarwinds-agent
```

---

## Verifying Agent Status

### From Orion Console
1. Go to **Settings → Manage Agents**
2. Look for your server — status should show **Agent Active**
3. Click on the node → verify resources are populating

### From Target Server (Linux)
```bash
# Check service status
sudo systemctl status solarwinds-agent

# View agent logs
sudo tail -f /var/log/solarwinds/agent/agent.log

# Check agent connectivity
sudo /opt/solarwinds/agent/bin/agentctl status
```

---

## Agent Ports Reference

| Port | Protocol | Direction | Purpose |
|---|---|---|---|
| 17778 | TCP | Agent → Orion | Agent registration & data |
| 17790 | TCP | Orion → Agent | Push deployment (optional) |
| 443 | TCP | Agent → Orion | Fallback HTTPS communication |

---

## Troubleshooting

| Issue | Check |
|---|---|
| Agent shows "Disconnected" | Firewall blocking port 17778 |
| Agent not appearing in console | Check agent log for connection errors |
| Push deployment fails | Verify WinRM is enabled on target |
| Linux agent fails to start | Check SELinux or AppArmor policies |

```bash
# On Linux — test connectivity to Orion
curl -k https://10.10.0.5:17778

# Check SELinux
sudo ausearch -c solarwinds-agent | audit2allow
```
