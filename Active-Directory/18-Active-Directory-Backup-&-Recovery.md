# Active Directory Backup & Recovery

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 18 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Why Backup & Recovery Matter  
- Types of AD Backups  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Verify AD Backup Status  
- Step 2 – Perform System State Backup  
- Step 3 – Restore Active Directory (Authoritative & Non‑Authoritative)  
- Step 4 – Recover Deleted AD Objects  
- Step 5 – Recover Domain Controllers  
- Step 6 – Manage Recycle Bin & Deleted Objects  
- Step 7 – Advanced AD Recovery Procedures  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory backup and recovery ensure that directory data, domain controllers, and critical identity infrastructure can be restored after corruption, accidental deletion, or disaster. Proper backup strategy prevents catastrophic failures and supports business continuity.

This document provides a structured guide for backing up and restoring Active Directory using Windows Server tools and best practices.

---

# Why Backup & Recovery Matter

AD backup protects:

- User accounts  
- Group memberships  
- Computer accounts  
- GPOs  
- DNS zones  
- FSMO roles  
- SYSVOL contents  

Without proper backups, AD corruption or accidental deletion can cause:

- Authentication failures  
- Domain controller outages  
- Loss of directory data  
- Replication failures  
- Business downtime  

---

# Types of AD Backups

| Backup Type | Description |
|-------------|-------------|
| **System State Backup** | Includes AD database (NTDS.dit), SYSVOL, registry, boot files |
| **Full Server Backup** | Includes OS, applications, AD, and data |
| **Bare Metal Recovery** | Restores entire server including OS and AD |
| **Recycle Bin** | Restores deleted AD objects without reboot |
| **Snapshots (VSS)** | Used for object recovery via AD DS snapshots |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Backup Tool | Windows Server Backup |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Recovery Tools | NTDSUtil, AD Recycle Bin, PowerShell |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Perform system state backups  
- Restore AD database  
- Recover deleted objects  
- Restore domain controllers  
- Perform authoritative restores  
- Validate backup integrity  

---

# Tools Used

- Windows Server Backup  
- NTDSUtil  
- AD Recycle Bin  
- PowerShell  
- Event Viewer  
- VSS Snapshots  

---

# Troubleshooting Workflow

```
        AD Backup/Recovery Issue
                 │
                 ▼
       Check Backup Availability & Integrity
                 │
                 ▼
       Identify Recovery Type Needed
                 │
                 ▼
     Perform Non-Authoritative or Authoritative Restore
                 │
                 ▼
     Validate SYSVOL, DNS, and Replication
                 │
                 ▼
     Review Event Logs for Recovery Errors
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Verify AD Functionality
```

---

# Step 1 – Verify AD Backup Status

### Check backup history

```powershell
wbadmin get versions
```

### Check system state backup

```powershell
wbadmin get versions -backuptarget:F:
```

### Ensure backups include:

- NTDS.dit  
- SYSVOL  
- Registry  
- Boot files  

---

# Step 2 – Perform System State Backup

### Using Windows Server Backup

```
Windows Server Backup → Local Backup → Backup Once → System State
```

### Using PowerShell

```powershell
wbadmin start systemstatebackup -backuptarget:F: -quiet
```

---

# Step 3 – Restore Active Directory

## Non‑Authoritative Restore (most common)

Used when:

- DC corrupted  
- AD database damaged  
- SYSVOL missing  

### Steps

1. Boot into **Directory Services Restore Mode (DSRM)**  
2. Run:

```powershell
wbadmin start systemstaterecovery -version:<version> -quiet
```

3. Reboot normally  
4. Replication restores missing data  

---

## Authoritative Restore (for deleted objects)

Used when:

- OU deleted  
- Users/groups deleted  
- GPOs deleted  

### Steps

1. Boot into DSRM  
2. Restore system state  
3. Run NTDSUtil:

```text
ntdsutil
activate instance ntds
authoritative restore
restore subtree "OU=Finance,DC=domain,DC=com"
```

4. Reboot  
5. AD replicates restored objects to other DCs  

---

# Step 4 – Recover Deleted AD Objects

## Using AD Recycle Bin (recommended)

### Enable Recycle Bin

```powershell
Enable-ADOptionalFeature -Identity 'Recycle Bin Feature' -Scope ForestOrConfigurationSet -Target domain.com
```

### Restore object

```powershell
Restore-ADObject -Identity <GUID>
```

---

# Step 5 – Recover Domain Controllers

## Bare Metal Recovery

Used when:

- DC hardware failure  
- OS corruption  
- Complete server loss  

### Steps

1. Boot from Windows Server media  
2. Select **Repair your computer**  
3. Choose **System Image Recovery**  
4. Restore full server image  

---

## Rebuild DC (if no backup)

1. Remove failed DC from AD  
2. Clean metadata:

```powershell
ntdsutil metadata cleanup
```

3. Promote new DC:

```powershell
Install-ADDSDomainController
```

---

# Step 6 – Manage Recycle Bin & Deleted Objects

### View deleted objects

```powershell
Get-ADObject -Filter 'isDeleted -eq $true' -IncludeDeletedObjects
```

### Restore object

```powershell
Restore-ADObject -Identity <GUID>
```

---

# Step 7 – Advanced AD Recovery Procedures

## Mount AD Snapshot

```powershell
ntdsutil snapshot "list all"
```

Mount snapshot:

```powershell
ntdsutil snapshot "mount <ID>"
```

## Use LDP.exe to browse snapshot

Allows recovery of attributes or objects.

---

# Verification

Confirm:

- DC boots normally  
- AD database healthy  
- SYSVOL and Netlogon available  
- Replication successful  
- DNS records intact  
- No AD errors in event logs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| Restore fails | Wrong backup version |
| Objects missing | Non-authoritative restore used |
| SYSVOL not replicating | DFSR corruption |
| DC stuck in DSRM | Boot configuration issue |
| Duplicate objects | Incorrect authoritative restore |

---

# Preventive Best Practices

- Perform system state backups weekly  
- Store backups offsite  
- Test restores regularly  
- Enable AD Recycle Bin  
- Document recovery procedures  
- Monitor backup success  
- Avoid snapshot-based DC restores (unsupported)  

---

# Escalation Criteria

Escalate when:

- AD database corruption suspected  
- SYSVOL missing across multiple DCs  
- Authoritative restore fails  
- Domain controllers cannot boot  
- Backup media corrupted  
- Forest-wide recovery required  

---

# References

- Microsoft Learn – AD Backup & Recovery  
- Microsoft Learn – NTDSUtil  
- Microsoft Learn – Windows Server Backup  
- Microsoft Learn – AD Recycle Bin  
