# Active Directory Disaster Recovery Plan

> Enterprise Windows Troublesbling Knowledge Base  
> Active Directory Module 20 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Why AD Disaster Recovery Matters  
- Core DR Principles  
- Environment  
- Common Tasks  
- Tools Used  
- Disaster Scenarios  
- Recovery Workflow  
- Step 1 – Assess the Impact  
- Step 2 – Stabilize the Environment  
- Step 3 – Restore Domain Controllers  
- Step 4 – Restore Active Directory Data  
- Step 5 – Restore SYSVOL & Group Policy  
- Step 6 – Restore DNS & Replication  
- Step 7 – Validate Authentication & Trusts  
- Step 8 – Post‑Recovery Hardening  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

A Disaster Recovery (DR) plan for Active Directory ensures that identity services can be restored quickly after catastrophic events such as:

- Domain controller failures  
- AD database corruption  
- Ransomware attacks  
- Hardware failures  
- Site outages  
- Accidental mass deletion  

This document provides a structured, actionable DR plan for restoring Active Directory in enterprise environments.

---

# Why AD Disaster Recovery Matters

Active Directory is required for:

- Authentication  
- Authorization  
- Group Policy  
- DNS  
- File shares  
- Applications (Exchange, SQL, M365, etc.)  

If AD fails, **the entire enterprise stops**.

A DR plan ensures:

- Minimal downtime  
- Controlled recovery  
- Data integrity  
- Business continuity  

---

# Core DR Principles

| Principle | Description |
|-----------|-------------|
| **Redundancy** | Multiple DCs per domain/site |
| **Backups** | Regular system state + full server backups |
| **Isolation** | Protect backups from ransomware |
| **Documentation** | Clear recovery steps |
| **Testing** | Regular DR simulations |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Backup Tool | Windows Server Backup / Veeam / Commvault |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Recovery Tools | NTDSUtil, AD Recycle Bin, PowerShell |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Restore domain controllers  
- Recover AD database  
- Restore SYSVOL  
- Rebuild DCs  
- Recover deleted objects  
- Validate replication  
- Restore DNS  
- Re-establish authentication  

---

# Tools Used

- Windows Server Backup  
- NTDSUtil  
- AD Recycle Bin  
- PowerShell  
- Event Viewer  
- repadmin  
- dcdiag  

---

# Disaster Scenarios

| Scenario | Recovery Type |
|----------|----------------|
| Single DC failure | Non-authoritative restore |
| Multiple DC failure | Authoritative restore + rebuild |
| AD database corruption | System state restore |
| Ransomware attack | Bare metal recovery |
| Accidental deletion | Recycle Bin or authoritative restore |
| Site outage | Promote new DCs in alternate site |

---

# Recovery Workflow

```
        AD Disaster Detected
                 │
                 ▼
       Assess Scope & Impact
                 │
                 ▼
       Stabilize Remaining DCs
                 │
                 ▼
     Restore DCs (Non/Authoritative)
                 │
                 ▼
     Restore SYSVOL, DNS, Replication
                 │
                 ▼
     Validate Authentication & GPOs
                 │
                 ▼
     Harden & Document Post-Recovery
```

---

# Step 1 – Assess the Impact

### Identify:

- Number of DCs affected  
- Whether SYSVOL is intact  
- Whether DNS is functional  
- Whether replication is broken  
- Whether AD database is corrupted  

### Tools

```powershell
dcdiag /v
repadmin /replsummary
dfsrdiag replicationstate
```

---

# Step 2 – Stabilize the Environment

### Actions:

- Disconnect compromised DCs  
- Ensure at least one healthy DC remains  
- Validate DNS functionality  
- Stop replication from corrupted DCs  

### Isolate damaged DCs

```powershell
Stop-Service NTDS
Stop-Service DFSR
```

---

# Step 3 – Restore Domain Controllers

## Non‑Authoritative Restore (most common)

Used when:

- DC corrupted  
- AD database damaged  
- SYSVOL missing  

### Steps

1. Boot into DSRM  
2. Restore system state  
3. Reboot normally  
4. Replication restores missing data  

---

## Authoritative Restore (for deleted objects)

Used when:

- OU deleted  
- Users/groups deleted  
- GPOs deleted  

### Steps

```text
ntdsutil
activate instance ntds
authoritative restore
restore subtree "OU=Finance,DC=domain,DC=com"
```

---

## Bare Metal Recovery (ransomware/hardware failure)

1. Boot from Windows Server media  
2. Select **System Image Recovery**  
3. Restore full server image  

---

# Step 4 – Restore Active Directory Data

### Restore AD database (NTDS.dit)

```powershell
wbadmin start systemstaterecovery -version:<version> -quiet
```

### Validate database

```powershell
ntdsutil "activate instance ntds" "files" "info"
```

---

# Step 5 – Restore SYSVOL & Group Policy

### Check SYSVOL

```powershell
dcdiag /test:sysvolcheck
```

### Check DFSR state

```powershell
dfsrdiag replicationstate
```

### Rebuild SYSVOL (if corrupted)

```powershell
net stop dfsr
net start dfsr
```

Or perform authoritative DFSR restore.

---

# Step 6 – Restore DNS & Replication

### Validate DNS

```powershell
dcdiag /test:dns
```

### Validate replication

```powershell
repadmin /showrepl
repadmin /syncall /AeD
```

### Rebuild replication topology

```powershell
repadmin /kcc
```

---

# Step 7 – Validate Authentication & Trusts

### Test secure channel

```powershell
nltest /sc_verify:domain.com
```

### Test Kerberos

```powershell
klist
```

### Test domain controller discovery

```powershell
nltest /dsgetdc:domain.com
```

---

# Step 8 – Post‑Recovery Hardening

### Actions:

- Patch DCs  
- Validate backups  
- Enable AD Recycle Bin  
- Audit privileged accounts  
- Review GPO permissions  
- Validate LAPS deployment  
- Document recovery steps  

---

# Verification

Confirm:

- DCs healthy  
- Replication successful  
- DNS resolving correctly  
- SYSVOL available  
- Authentication working  
- No AD errors in event logs  
- GPOs applying correctly  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| SYSVOL missing | DFSR corruption |
| Replication broken | DNS or topology issue |
| DC stuck in DSRM | Boot config error |
| Duplicate objects | Incorrect authoritative restore |
| Authentication failing | Kerberos or secure channel issue |

---

# Preventive Best Practices

- Weekly system state backups  
- Monthly DR testing  
- Store backups offline  
- Use multiple DCs per site  
- Enable AD Recycle Bin  
- Harden DCs  
- Monitor replication  
- Document DR procedures  

---

# Escalation Criteria

Escalate when:

- Multiple DCs fail simultaneously  
- AD database corruption suspected  
- SYSVOL missing across domain  
- Replication broken domain‑wide  
- Ransomware impacts DCs  
- Forest‑wide recovery required  

---

# References

- Microsoft Learn – AD Disaster Recovery  
- Microsoft Learn – NTDSUtil  
- Microsoft Learn – Windows Server Backup  
- Microsoft Learn – DFS Replication  

---

Ready for **Document 21 – Active Directory Interview Questions (Beginner → Advanced)**.
