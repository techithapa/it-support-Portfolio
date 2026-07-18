# Group Management (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 03 — Beginner Difficulty

---

# Table of Contents

- Overview  
- Group Types  
- Group Scopes  
- Environment  
- Common Tasks  
- Tools Used  
- Step 1 – Create a New Group  
- Step 2 – Add Members to a Group  
- Step 3 – Remove Members from a Group  
- Step 4 – Modify Group Properties  
- Step 5 – Nest Groups (AGDLP Model)  
- Step 6 – Manage Security vs Distribution Groups  
- Step 7 – Manage Group Membership Using PowerShell  
- Step 8 – Manage Group Ownership & Managed By  
- Step 9 – Advanced Group Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory groups are used to manage permissions, access control, and distribution lists. Proper group management ensures users receive the correct access to resources such as shared folders, applications, printers, and email lists.

This document provides a structured guide for creating, modifying, and managing groups in Active Directory.

---

# Group Types

| Type | Purpose |
|------|---------|
| **Security Group** | Used for permissions (file shares, applications, GPO filtering) |
| **Distribution Group** | Used for email distribution lists (Exchange / Microsoft 365) |

---

# Group Scopes

| Scope | Description |
|--------|-------------|
| **Domain Local** | Used to assign permissions within a single domain |
| **Global** | Used to group users with similar roles (recommended for user membership) |
| **Universal** | Used across multiple domains (requires Global Catalog) |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Management Tools | ADUC, PowerShell, RSAT |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Create security or distribution groups  
- Add/remove group members  
- Modify group scope  
- Manage nested groups  
- Assign permissions using groups  
- Manage group ownership  
- Clean up unused groups  

---

# Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell  
- Group Policy Management  
- File Explorer (for permission testing)  

---

# Step 1 – Create a New Group

### Using ADUC

```
Active Directory Users and Computers
Right-click OU → New → Group
```

Configure:

- Group name  
- Group scope (Global, Domain Local, Universal)  
- Group type (Security or Distribution)

### Using PowerShell

```powershell
New-ADGroup -Name "Finance-Users" -GroupScope Global -GroupCategory Security -Path "OU=Groups,DC=domain,DC=com"
```

---

# Step 2 – Add Members to a Group

### Using ADUC

```
Group → Properties → Members → Add
```

### Using PowerShell

```powershell
Add-ADGroupMember -Identity "Finance-Users" -Members jsmith
```

Add multiple users:

```powershell
Add-ADGroupMember -Identity "Finance-Users" -Members jsmith, mjones, tlee
```

---

# Step 3 – Remove Members from a Group

### Using ADUC

```
Group → Properties → Members → Remove
```

### Using PowerShell

```powershell
Remove-ADGroupMember -Identity "Finance-Users" -Members jsmith -Confirm:$false
```

---

# Step 4 – Modify Group Properties

### Using ADUC

```
Group → Properties
```

Modify:

- Description  
- Email address (if distribution group)  
- Managed By  
- Group scope  

### Using PowerShell

```powershell
Set-ADGroup -Identity "Finance-Users" -Description "Finance Department Security Group"
```

---

# Step 5 – Nest Groups (AGDLP Model)

Microsoft recommends the **AGDLP** model:

```
A – Accounts (Users)
G – Global Groups (Department groups)
DL – Domain Local Groups (Resource permissions)
P – Permissions (Assigned to DL groups)
```

Example:

```
Users → Finance-Users (Global Group)
Finance-Users → Share-Finance-Read (Domain Local Group)
Share-Finance-Read → Assigned Read Permissions on \\server\finance
```

---

# Step 6 – Manage Security vs Distribution Groups

### Security Groups

Used for:

- NTFS permissions  
- Shared folder access  
- Application access  
- GPO filtering  

### Distribution Groups

Used for:

- Email lists  
- Microsoft 365 groups  
- Exchange distribution lists  

Convert distribution → security:

```powershell
Set-ADGroup -Identity "AllStaff" -GroupCategory Security
```

---

# Step 7 – Manage Group Membership Using PowerShell

### List group members

```powershell
Get-ADGroupMember -Identity "Finance-Users"
```

### Export group members

```powershell
Get-ADGroupMember "Finance-Users" | Select Name,SamAccountName | Export-Csv FinanceUsers.csv -NoTypeInformation
```

### Check if user is in a group

```powershell
Get-ADPrincipalGroupMembership jsmith
```

---

# Step 8 – Manage Group Ownership & Managed By

### Using ADUC

```
Group → Properties → Managed By
```

Assign group owner:

```powershell
Set-ADGroup -Identity "Finance-Users" -ManagedBy jsmith
```

Allow owner to update membership:

```powershell
Set-ADGroup -Identity "Finance-Users" -Replace @{managedBy="jsmith";}
```

---

# Step 9 – Advanced Group Management

## Clean Up Unused Groups

```powershell
Search-ADAccount -AccountInactive -UsersOnly
```

## Check Group Replication

```powershell
repadmin /replsummary
```

## Check Group Membership Changes

```powershell
Get-EventLog -LogName Security -InstanceId 4728,4729
```

---

# Verification

Confirm:

- Group membership updated  
- Permissions applied correctly  
- User access matches group membership  
- No replication delays  
- Group scope appropriate for usage  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| User cannot access folder | Missing group membership |
| Group not replicating | AD replication failure |
| Distribution group not emailing | Exchange sync issue |
| Group owner cannot modify members | Managed By not configured |
| Incorrect permissions | Wrong group scope |

---

# Preventive Best Practices

- Use AGDLP model  
- Store groups in dedicated OUs  
- Use naming conventions (e.g., Dept-Access-Read)  
- Review group membership regularly  
- Avoid adding users directly to resource ACLs  
- Document group purpose and owner  

---

# Escalation Criteria

Escalate when:

- Group membership fails to replicate  
- Permissions not applying despite correct membership  
- Distribution groups failing across multiple users  
- Group corruption suspected  
- Domain controller replication issues detected  

---

# References

- Microsoft Learn – Active Directory Groups  
- Microsoft Learn – AGDLP Model  
- Microsoft Learn – PowerShell AD Module  
