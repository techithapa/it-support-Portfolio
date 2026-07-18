# AD Replication Basics (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 13 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Is AD Replication?  
- How Replication Works  
- Replication Components  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Check Replication Health  
- Step 2 – Validate Site & Subnet Configuration  
- Step 3 – Inspect Replication Topology  
- Step 4 – Troubleshoot Replication Failures  
- Step 5 – Check SYSVOL & DFSR Replication  
- Step 6 – Force Replication  
- Step 7 – Advanced Replication Diagnostics  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory replication ensures that all domain controllers (DCs) share consistent directory data. Without healthy replication, authentication fails, GPOs break, DNS becomes inconsistent, and AD objects may appear missing or outdated.

This document provides a structured guide for understanding, validating, and troubleshooting AD replication.

---

# What Is AD Replication?

Active Directory replication is the process of synchronizing directory data between domain controllers. It ensures:

- User accounts  
- Computer accounts  
- Group memberships  
- GPOs  
- DNS records  
- SYSVOL contents  

are consistent across the domain.

---

# How Replication Works

AD replication uses:

- **Multi-master replication** (all DCs can write)  
- **Knowledge Consistency Checker (KCC)** to build topology  
- **Replication partners** for each DC  
- **Change notifications** for updates  
- **Incremental replication** (only changes replicate)  

---

# Replication Components

| Component | Description |
|-----------|-------------|
| **KCC** | Builds replication topology |
| **NTDS Settings** | Defines replication partners |
| **Sites & Subnets** | Controls replication boundaries |
| **Connection Objects** | Define replication paths |
| **DFSR/FRS** | Replicates SYSVOL |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Replication | Intra-site (fast), Inter-site (scheduled) |
| Tools | repadmin, dcdiag, PowerShell |

---

# Common Tasks

- Check replication health  
- Validate site/subnet configuration  
- Troubleshoot replication failures  
- Force replication  
- Repair SYSVOL replication  
- Inspect replication topology  
- Monitor replication latency  

---

# Tools Used

- **repadmin**  
- **dcdiag**  
- **PowerShell AD Module**  
- **Event Viewer**  
- **Active Directory Sites and Services**  

---

# Troubleshooting Workflow

```
         Replication Issue Detected
                 │
                 ▼
       Run Replication Health Checks
                 │
                 ▼
       Validate Sites & Subnets
                 │
                 ▼
     Inspect Replication Topology (KCC)
                 │
                 ▼
     Review Event Logs for Replication Errors
                 │
                 ▼
     Check SYSVOL/DFSR Replication
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Force Replication
                 │
                 ▼
             Verify Consistency
```

---

# Step 1 – Check Replication Health

### Replication summary

```powershell
repadmin /replsummary
```

### Check inbound/outbound replication

```powershell
repadmin /showrepl
```

### Check replication queue

```powershell
repadmin /queue
```

---

# Step 2 – Validate Site & Subnet Configuration

Open:

```
Active Directory Sites and Services
```

Check:

- Sites match physical locations  
- Subnets assigned correctly  
- DCs placed in correct sites  

### PowerShell

```powershell
Get-ADReplicationSite
Get-ADReplicationSubnet
```

---

# Step 3 – Inspect Replication Topology

### View connection objects

```powershell
repadmin /showconn
```

### Force KCC to rebuild topology

```powershell
repadmin /kcc
```

---

# Step 4 – Troubleshoot Replication Failures

### Common causes

- DNS misconfiguration  
- Firewall blocking replication ports  
- Time synchronization issues  
- Network connectivity problems  
- Stale connection objects  
- Missing SRV records  
- DC offline or unreachable  

### Check event logs

```
Event Viewer → Directory Service
Event Viewer → DFS Replication
Event Viewer → System
```

Look for:

- Event ID 1311 (replication failure)  
- Event ID 1864 (DC unreachable)  
- Event ID 2042 (USN rollback)  
- Event ID 4012 (DFSR backlog)  

---

# Step 5 – Check SYSVOL & DFSR Replication

### Check DFSR state

```powershell
dfsrdiag replicationstate
```

### Check SYSVOL availability

```powershell
dcdiag /test:sysvolcheck
```

### Check Netlogon

```powershell
dcdiag /test:netlogons
```

---

# Step 6 – Force Replication

### Using repadmin

```powershell
repadmin /syncall /AeD
```

### Using Active Directory Sites and Services

```
Right-click connection → Replicate Now
```

---

# Step 7 – Advanced Replication Diagnostics

## Check replication metadata

```powershell
repadmin /showobjmeta dc01 "CN=UserName,OU=Users,DC=domain,DC=com"
```

## Check lingering objects

```powershell
repadmin /removelingeringobjects
```

## Check USN rollback

```powershell
repadmin /showrepl dc01 /verbose
```

## Check replication partners

```powershell
Get-ADReplicationPartnerMetadata -Target dc01
```

---

# Verification

Confirm:

- No replication errors  
- All DCs synchronized  
- SYSVOL healthy  
- DNS records consistent  
- GPOs applying correctly  
- No lingering objects  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| Replication failure | DNS misconfiguration |
| DC unreachable | Network/firewall issue |
| SYSVOL not replicating | DFSR/FRS failure |
| GPO not applying | Replication delay |
| Kerberos failures | Time sync issues |
| Lingering objects | Long DC outage |

---

# Preventive Best Practices

- Monitor replication health regularly  
- Ensure DNS is correctly configured  
- Keep DCs synchronized with NTP  
- Use AD Sites & Services correctly  
- Avoid long DC outages  
- Document replication topology  
- Use AD-integrated DNS zones  

---

# Escalation Criteria

Escalate when:

- Replication broken across multiple DCs  
- SYSVOL missing or corrupted  
- Lingering objects detected  
- USN rollback suspected  
- Inter-site replication failing  
- AD database corruption suspected  

---

# References

- Microsoft Learn – AD Replication  
- Microsoft Learn – repadmin  
- Microsoft Learn – dcdiag  
- Microsoft Learn – DFS Replication  
