# 📊 SWQL Queries Collection

SWQL (SolarWinds Query Language) is a SQL-like language for querying the Orion database. These queries can be used in dashboards, reports, and custom views.

---

## 1. Top 10 Nodes by CPU Load

```sql
SELECT TOP 10
  N.Caption AS NodeName,
  N.IPAddress,
  N.CPULoad AS CPU_Percent,
  N.MemoryUsed AS MemoryUsed_Bytes,
  N.Location
FROM Orion.Nodes N
WHERE N.CPULoad IS NOT NULL
ORDER BY N.CPULoad DESC
```

---

## 2. All Down Nodes

```sql
SELECT
  N.Caption AS NodeName,
  N.IPAddress,
  N.Status,
  N.Location,
  N.LastBoot,
  N.SysDescription
FROM Orion.Nodes N
WHERE N.Status = 2  -- 1=Up, 2=Down, 3=Warning, 4=Unknown
ORDER BY N.Caption
```

---

## 3. Interfaces with High Error Rate

```sql
SELECT
  N.Caption AS NodeName,
  I.Caption AS InterfaceName,
  I.InErrorPercent,
  I.OutErrorPercent,
  I.OperStatus
FROM Orion.NPM.Interfaces I
JOIN Orion.Nodes N ON I.NodeID = N.NodeID
WHERE I.InErrorPercent > 1 OR I.OutErrorPercent > 1
ORDER BY I.InErrorPercent DESC
```

---

## 4. Volumes Near Capacity (> 85%)

```sql
SELECT
  N.Caption AS NodeName,
  V.Caption AS VolumeName,
  V.VolumeType,
  V.DiskUsed AS UsedGB,
  V.DiskSize AS TotalGB,
  ROUND((V.DiskUsed / V.DiskSize) * 100, 2) AS UsedPercent
FROM Orion.Volumes V
JOIN Orion.Nodes N ON V.NodeID = N.NodeID
WHERE (V.DiskUsed / V.DiskSize) * 100 > 85
  AND V.VolumeType = 'Fixed Disk'
ORDER BY UsedPercent DESC
```

---

## 5. Active Alerts Summary

```sql
SELECT
  A.ObjectName AS AffectedObject,
  A.Name AS AlertName,
  A.Severity,
  A.TrigerredDateTime AS TriggeredAt,
  A.Acknowledged,
  A.AcknowledgedBy
FROM Orion.AlertActive A
ORDER BY A.TrigerredDateTime DESC
```

---

## 6. Node Availability (Last 30 Days)

```sql
SELECT
  N.Caption AS NodeName,
  N.IPAddress,
  AVG(AV.Availability) AS Availability_Percent
FROM Orion.Nodes N
JOIN Orion.ResponseTime.Daily AV ON N.NodeID = AV.NodeID
WHERE AV.DateTime > ADDDAY(-30, GETDATE())
GROUP BY N.Caption, N.IPAddress
ORDER BY Availability_Percent ASC
```

---

## 7. Interfaces by Bandwidth Utilization

```sql
SELECT TOP 20
  N.Caption AS NodeName,
  I.Caption AS InterfaceName,
  I.InBandwidth AS InBandwidth_bps,
  I.OutBandwidth AS OutBandwidth_bps,
  I.Inpps AS InPacketsPerSec,
  I.Outpps AS OutPacketsPerSec
FROM Orion.NPM.Interfaces I
JOIN Orion.Nodes N ON I.NodeID = N.NodeID
WHERE I.OperStatus = 1  -- Up only
ORDER BY I.InBandwidth DESC
```

---

## 8. Nodes by Vendor / OS

```sql
SELECT
  N.Vendor,
  N.MachineType,
  N.IOSVersion,
  COUNT(*) AS NodeCount
FROM Orion.Nodes N
WHERE N.Vendor IS NOT NULL
GROUP BY N.Vendor, N.MachineType, N.IOSVersion
ORDER BY NodeCount DESC
```

---

## 9. Nodes Not Polled in Last Hour

```sql
SELECT
  N.Caption AS NodeName,
  N.IPAddress,
  N.LastSync,
  N.Status
FROM Orion.Nodes N
WHERE N.LastSync < ADDMINUTE(-60, GETDATE())
  AND N.Status <> 2  -- Exclude already-down nodes
ORDER BY N.LastSync ASC
```

---

## 10. Agent Status Report

```sql
SELECT
  N.Caption AS NodeName,
  N.IPAddress,
  AG.AgentStatus,
  AG.AgentVersion,
  AG.ConnectionStatus,
  AG.LastContactTime
FROM Orion.Nodes N
JOIN Orion.AgentManagement.Agent AG ON N.NodeID = AG.NodeID
ORDER BY AG.LastContactTime DESC
```

---

## How to Use These Queries

1. Go to **Reports → All Reports → Create New Report**
2. Select **SWQL** as the data source
3. Paste the query
4. Preview and save

Or use in **Custom Views:**
1. Edit any Orion view
2. Add a **Custom Table** resource
3. Paste SWQL query
4. Configure columns and display
