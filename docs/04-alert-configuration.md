# 🔔 Alert Configuration Guide

---

## Alert Architecture in SolarWinds

```
Trigger Condition → Alert Rule → Alert Action(s)
                                    ├── Send Email
                                    ├── Send SMS
                                    ├── Create ITSM Ticket
                                    ├── Log to Syslog
                                    └── Run Script
```

---

## Creating an Alert — Step by Step

1. Go to **Alerts & Activity → Manage Alerts**
2. Click **Add New Alert**
3. Fill in:
   - **Name** — descriptive (e.g., "Node Down - Critical")
   - **Severity** — Critical / High / Medium / Low
   - **Object Type** — Node, Interface, Volume, Application, etc.
4. Set **Trigger Condition**
5. Set **Reset Condition** (when to auto-clear the alert)
6. Configure **Alert Actions** (email, log, etc.)
7. Set **Time of Day** restrictions if needed
8. Save and enable

---

## Alert 1 — Node Down

**Trigger Condition:**
- Object Type: Node
- Condition: `Node Status is equal to Down`
- Trigger after: 2 consecutive polling cycles

**Reset Condition:**
- `Node Status is not equal to Down`

**Email Template:**
```
Subject: 🔴 [CRITICAL] Node Down: ${NodeName}

Node ${NodeName} (${IP_Address}) is DOWN.
Location: ${Location}
Down since: ${TriggerTime}
Acknowledged by: ${Acknowledged}

View in Orion: ${NodeDetailsURL}
```

---

## Alert 2 — High CPU Utilization

**Trigger Condition:**
- Object Type: Node
- Condition: `CPU Load > 85`
- Duration: `Sustained for at least 5 minutes`

**Reset Condition:**
- `CPU Load < 70`

---

## Alert 3 — Interface Down

**Trigger Condition:**
- Object Type: Interface
- Condition: `Operational Status is equal to Down`
- Exclude: Admin-shutdown interfaces (Operational Status = Shutdown)

**Reset Condition:**
- `Operational Status is equal to Up`

---

## Alert 4 — High Disk Usage

**Trigger Condition:**
- Object Type: Volume
- Condition: `Disk Usage > 90%`

**Reset Condition:**
- `Disk Usage < 80%`

---

## Alert 5 — Interface Error Rate

**Trigger Condition:**
- Object Type: Interface
- Condition: `In/Out Error Percent > 1`
- Duration: Sustained for 3 minutes

---

## Suppression & Maintenance Windows

**Creating a Maintenance Window:**
1. Go to **Settings → Manage Maintenance Windows**
2. Click **Add Maintenance Window**
3. Select nodes/groups affected
4. Set start/end time
5. Choose whether to suppress alerts only or also polling

```
Use case: Suppress alerts during scheduled patching window
Example: Every Tuesday 02:00–04:00 AM, suppress all Windows server alerts
```

---

## Alert Escalation (Multi-tier)

```
Level 1 (0 min):  Email → NOC Team DL
Level 2 (15 min): Email → Team Lead + SMS
Level 3 (30 min): Email → Manager + Create ServiceNow Ticket
```

Configure using multiple **Alert Actions** with different **time offsets** in the same alert rule.

---

## Best Practices

- Use **Alert Suppression** to avoid noise during maintenance
- Group similar devices and apply alerts at **Group level**
- Use **Dynamic Queries** instead of static node lists
- Always set a **Reset Condition** to auto-clear resolved alerts
- Test alerts using the **"Test Alert"** feature before going live
- Review **Alert History** weekly to tune thresholds
