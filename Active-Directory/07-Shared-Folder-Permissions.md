# Shared Folder Permissions (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 07 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- NTFS vs Share Permissions  
- Permission Types  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Create a Shared Folder  
- Step 2 – Configure Share Permissions  
- Step 3 – Configure NTFS Permissions  
- Step 4 – Combine NTFS + Share Permissions  
- Step 5 – Assign Permissions Using Security Groups  
- Step 6 – Test Access from Client Devices  
- Step 7 – Manage Inheritance  
- Step 8 – Advanced Permission Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Shared folder permissions control access to files and directories on Windows servers. They determine who can read, modify, or manage data stored on network shares. Proper permission management ensures security, prevents unauthorized access, and supports departmental workflows.

This document provides a structured guide for configuring, managing, and troubleshooting shared folder permissions in Active Directory environments.

---

# NTFS vs Share Permissions

| Permission Type | Applies To | Location | Notes |
|-----------------|------------|----------|-------|
| **Share Permissions** | Network access | Share-level | Basic control (Read, Change, Full Control) |
| **NTFS Permissions** | Local & network access | File system | More granular (Read, Modify, Write, Full Control) |

**Effective permissions = Most restrictive combination of NTFS + Share permissions.**

---

# Permission Types

### Share Permissions
- **Read** – View folder contents  
- **Change** – Read, write, modify  
- **Full Control** – Change permissions, take ownership  

### NTFS Permissions
- **Read**  
- **Write**  
- **Modify**  
- **Full Control**  
- **Special permissions** (advanced)

---

# Environment

| Component | Details |
|----------|---------|
| File Server | Windows Server 2016/2019/2022 |
| Directory Service | Active Directory Domain Services |
| Clients | Windows 10 / Windows 11 |
| Management Tools | File Explorer, PowerShell, ADUC |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Create shared folders  
- Configure NTFS and share permissions  
- Assign permissions using AD security groups  
- Test user access  
- Manage inheritance  
- Troubleshoot access issues  

---

# Tools Used

- File Explorer  
- PowerShell  
- ADUC  
- Effective Access Tool  
- Event Viewer  

---

# Troubleshooting Workflow

```
       Shared Folder Access Issue
                 │
                 ▼
       Identify User & Security Groups
                 │
                 ▼
       Check Share Permissions on Server
                 │
                 ▼
       Check NTFS Permissions on Folder
                 │
                 ▼
     Validate Effective Access (Most Restrictive)
                 │
                 ▼
     Check Inheritance & Permission Overrides
                 │
                 ▼
     Review Event Logs for Access Denials
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
             Test User Access
```

---

# Step 1 – Create a Shared Folder

### Using File Explorer

1. Create folder:  
   ```
   C:\Shares\Finance
   ```
2. Right-click → Properties → Sharing → Advanced Sharing  
3. Check **Share this folder**

### Using PowerShell

```powershell
New-Item -ItemType Directory -Path "C:\Shares\Finance"
New-SmbShare -Name "Finance" -Path "C:\Shares\Finance" -FullAccess "Domain Admins"
```

---

# Step 2 – Configure Share Permissions

### Using File Explorer

```
Folder → Properties → Sharing → Advanced Sharing → Permissions
```

Recommended:

- **Everyone = Remove**
- Assign AD security groups only

### Using PowerShell

```powershell
Grant-SmbShareAccess -Name "Finance" -AccountName "Finance-Access" -AccessRight Full -Force
```

---

# Step 3 – Configure NTFS Permissions

### Using File Explorer

```
Folder → Properties → Security
```

Recommended:

- Remove **Everyone**
- Add security groups (not individual users)

### Using PowerShell

```powershell
icacls "C:\Shares\Finance" /grant "Finance-Access:(OI)(CI)M"
```

---

# Step 4 – Combine NTFS + Share Permissions

Effective permissions = **most restrictive**.

Example:

| Share | NTFS | Effective |
|-------|------|-----------|
| Full Control | Read | Read |
| Change | Modify | Modify |
| Read | Full Control | Read |

---

# Step 5 – Assign Permissions Using Security Groups

Follow **AGDLP**:

```
A – Accounts (Users)
G – Global Groups (Finance-Users)
DL – Domain Local Groups (Finance-Access)
P – Permissions (Assigned to DL groups)
```

Example:

```powershell
Add-ADGroupMember -Identity "Finance-Access" -Members "Finance-Users"
```

---

# Step 6 – Test Access from Client Devices

### Using Effective Access Tool

```
Folder → Properties → Security → Advanced → Effective Access
```

### Using PowerShell

```powershell
Test-Path "\\server\Finance"
```

---

# Step 7 – Manage Inheritance

### Disable inheritance (if needed)

```
Folder → Properties → Security → Advanced → Disable inheritance
```

### Convert inherited permissions

Choose:

- Convert inherited permissions  
- Remove inherited permissions  

---

# Step 8 – Advanced Permission Management

## Audit Access Failures

Enable auditing:

```powershell
auditpol /set /subcategory:"File System" /success:enable /failure:enable
```

Check logs:

```
Event Viewer → Security → Event ID 4663
```

## Reset NTFS Permissions

```powershell
icacls "C:\Shares\Finance" /reset /T
```

## Export Permissions

```powershell
icacls "C:\Shares\Finance" /save perms.txt /T
```

---

# Verification

Confirm:

- User can access the share  
- Permissions match group membership  
- Effective access is correct  
- No access denied events  
- Inheritance configured properly  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| User cannot access share | Missing group membership |
| Incorrect permissions | NTFS more restrictive than share |
| Access denied | Inheritance blocking permissions |
| Everyone still has access | Misconfigured share permissions |
| Permissions not applying | Wrong group scope or replication delay |

---

# Preventive Best Practices

- Use AD security groups (never individual users)  
- Follow AGDLP model  
- Document permission structure  
- Avoid mixing NTFS and share permissions inconsistently  
- Regularly audit permissions  
- Remove **Everyone** from shares  
- Use descriptive group names  

---

# Escalation Criteria

Escalate when:

- Permissions fail across multiple users  
- NTFS inheritance corruption suspected  
- Share permissions not applying  
- Access denied despite correct group membership  
- DFS or file server replication issues detected  

---

# References

- Microsoft Learn – NTFS Permissions  
- Microsoft Learn – SMB Shares  
- Microsoft Learn – Access Control Lists  
- Microsoft Learn – PowerShell File Management  
