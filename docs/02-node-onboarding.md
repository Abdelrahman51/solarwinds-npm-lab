# 🔌 Node Onboarding Guide

## Method 1 — Network Discovery (Recommended for bulk)

### Configure Discovery
1. Go to **Settings → Network Discovery**
2. Click **Add New Discovery**
3. Set discovery name and select **IP ranges** to scan
4. Add SNMP credentials (v2c community string or v3 credentials)
5. Add WMI/Windows credentials if monitoring Windows servers
6. Set discovery schedule or run immediately

### Discovery Filters
- Exclude printers, UPS, and non-critical devices
- Filter by SNMP OID if needed (e.g., only Cisco devices)

---

## Method 2 — Manual Node Add

1. Go to **Settings → Manage Nodes → Add Node**
2. Enter IP address or hostname
3. Select polling method:
   - **SNMP v2c** — for network devices
   - **SNMP v3** — for secure environments
   - **WMI** — for Windows servers (agent-less)
   - **Agent** — for servers with SolarWinds agent installed
4. Test credentials → click **Next**
5. Select resources to monitor (interfaces, CPU, memory, volumes)
6. Click **Add Node**

---

## Method 3 — CSV Import (Bulk onboarding)

### CSV Format
```csv
Hostname,IPAddress,Community,SNMPVersion,Location,DeviceType
core-sw-01,10.10.1.1,PUBLIC,2,Cairo-DC,Cisco_Switch
dist-sw-01,10.10.1.2,PUBLIC,2,Cairo-DC,Cisco_Switch
web-server-01,10.10.2.10,,,Cairo-DC,Windows_Server
```

### Import Steps
1. Go to **Settings → Manage Nodes → Import Nodes**
2. Upload your CSV file
3. Map CSV columns to Orion fields
4. Review and confirm import

---

## SNMP Configuration — Cisco IOS

```ios
! Basic SNMP v2c configuration
snmp-server community PUBLIC ro
snmp-server location Cairo-DC-Rack01
snmp-server contact noc@company.com

! Enable SNMP traps to Orion server
snmp-server host 10.10.0.5 version 2c PUBLIC
snmp-server enable traps snmp linkdown linkup coldstart
snmp-server enable traps interface
snmp-server enable traps bgp
```

## SNMP Configuration — Cisco IOS (v3 - More Secure)

```ios
! Create SNMP v3 user
snmp-server group ORION-GROUP v3 priv
snmp-server user ORION-USER ORION-GROUP v3 auth sha AuthPass123 priv aes 128 PrivPass123

! Send traps to Orion
snmp-server host 10.10.0.5 version 3 priv ORION-USER
snmp-server enable traps
```

---

## Post-Onboarding Checklist

- [ ] Node status shows **Up** (green)
- [ ] Interfaces discovered and monitored
- [ ] CPU and Memory polling working
- [ ] Response time graph populating
- [ ] Node placed in correct **Group/Container**
- [ ] Custom properties set (Location, Department, etc.)
