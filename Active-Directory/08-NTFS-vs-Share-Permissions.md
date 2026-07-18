# NTFS vs Share Permissions (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 08 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Are NTFS Permissions?  
- What Are Share Permissions?  
- How Effective Permissions Work  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – View NTFS Permissions  
- Step 2 – View Share Permissions  
- Step 3 – Calculate Effective Permissions  
- Step 4 – Assign Permissions Using Security Groups  
- Step 5 – Manage Permission Inheritance  
- Step 6 – Use Effective Access Tool  
- Step 7 – Advanced Permission Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

NTFS and Share permissions work together to control access to files and folders on Windows servers. Understanding how they interact is essential for securing shared resources, preventing unauthorized access, and ensuring users have the correct level of access.

This document explains the difference between NTFS and Share permissions, how they combine, and how to manage them effectively in Active Directory environments.

---

# What Are NTFS Permissions?

NTFS permissions apply to the **file system** itself. They control access whether the user is local or accessing the folder over the network.

### NTFS Permission Levels

| Permission | Description |
|-----------|-------------|
| **Read** | View files and folders |
| **Write** | Create and modify files |
| **Modify** | Read, write, delete |
| **Full Control** | Modify permissions, take ownership |
| **Special Permissions** | Granular control (advanced) |

NTFS permissions are **more granular** and **more secure** than share permissions.

---

# What Are Share Permissions?

Share permissions apply **only when accessing a folder over the network** (UNC path).

### Share Permission Levels

| Permission | Description |
|-----------|-------------|
| **Read** | View folder contents |
| **Change** | Read, write, modify |
| **Full Control** | Change permissions, take ownership |

Share permissions are **less granular** and typically used to provide broad access.

---

# How Effective Permissions Work

Effective permissions = **most restrictive combination** of NTFS + Share permissions.

### Example

| Share | NTFS | Effective |
|-------|------|-----------|
| Full Control | Read | **Read** |
| Change | Modify | **Modify** |
| Read | Full Control | **Read** |

NTFS permissions always win when they are more restrictive.

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

- Configure NTFS permissions  
- Configure share permissions  
- Combine permissions correctly  
- Assign permissions using AD security groups  
- Troubleshoot access issues  
- Manage inheritance  
- Audit access failures  

---

# Tools Used

- File Explorer  
- PowerShell  
- Effective Access Tool  
- ADUC  
- Event Viewer  

---

# Troubleshooting Workflow

```
       Permission Issue Detected
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
     Calculate Effective Permissions (Most Restrictive)
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

# Step 1 – View NTFS Permissions

### Using File Explorer

```
Folder → Properties → Security
```

### Using PowerShell

```powershell
icacls "C:\Shares\Finance"
```

---

# Step 2 – View Share Permissions

### Using File Explorer

```
Folder → Properties → Sharing → Advanced Sharing → Permissions
```

### Using PowerShell

```powershell
Get-SmbShareAccess -Name "Finance"
```

---

# Step 3 – Calculate Effective Permissions

Use the **Effective Access** tool:

```
Folder → Properties → Security → Advanced → Effective Access
```

Select:

- User  
- Group  

This shows the final combined permissions.

---

# Step 4 – Assign Permissions Using Security Groups

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

# Step 5 – Manage Permission Inheritance

### Disable inheritance

```
Folder → Properties → Security → Advanced → Disable inheritance
```

Choose:

- Convert inherited permissions  
- Remove inherited permissions  

---

# Step 6 – Use Effective Access Tool

This tool helps identify:

- Missing permissions  
- Conflicting permissions  
- Inheritance issues  
- Group membership problems  

---

# Step 7 – Advanced Permission Management

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
