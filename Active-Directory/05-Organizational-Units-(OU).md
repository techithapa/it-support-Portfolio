# Organizational Units (OU) Management (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 05 — Beginner Difficulty

---

# Table of Contents

- Overview  
- What Are Organizational Units?  
- OU vs Containers  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Create an Organizational Unit  
- Step 2 – Modify OU Properties  
- Step 3 – Delegate Control of an OU  
- Step 4 – Move Objects Between OUs  
- Step 5 – Apply Group Policies to OUs  
- Step 6 – Protect OUs from Accidental Deletion  
- Step 7 – OU Design Best Practices  
- Step 8 – Advanced OU Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Organizational Units (OUs) are logical containers within Active Directory used to organize users, computers, groups, and other objects. OUs allow administrators to apply Group Policies, delegate administrative control, and maintain a clean directory structure.

Proper OU design and management are essential for security, scalability, and efficient administration.

---

# What Are Organizational Units?

An OU is:

- A logical container for AD objects  
- Used for grouping users, computers, and groups  
- A target for Group Policy Objects (GPOs)  
- A boundary for delegated administration  
- A key part of AD hierarchy and structure  

---

# OU vs Containers

| Feature | OU | Container |
|--------|----|-----------|
| Can apply GPOs | ✔ Yes | ✘ No |
| Can delegate control | ✔ Yes | ✘ No |
| Default system containers | No | Yes (e.g., *Users*, *Computers*) |
| Recommended for production | ✔ Yes | ✘ No |

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

- Create OUs  
- Rename OUs  
- Move objects between OUs  
- Delegate control  
- Apply GPOs  
- Protect OUs from accidental deletion  
- Design OU structure for departments or locations  

---

# Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell  
- Group Policy Management Console (GPMC)  
- RSAT Tools  

---

# Troubleshooting Workflow

```
          OU Issue Detected
                 │
                 ▼
       Identify Object Placement
                 │
                 ▼
       Check GPO Links & Inheritance
                 │
                 ▼
     Validate Delegation & Permissions
                 │
                 ▼
     Check Replication Across DCs
                 │
                 ▼
     Review Event Logs for GPO/AD Errors
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
             Verify OU Behavior
```

---

# Step 1 – Create an Organizational Unit

### Using ADUC

```
Active Directory Users and Computers
Right-click domain → New → Organizational Unit
```

Enter:

- OU name  
- Ensure “Protect from accidental deletion” is checked  

### Using PowerShell

```powershell
New-ADOrganizationalUnit -Name "Finance" -Path "DC=domain,DC=com" -ProtectedFromAccidentalDeletion $true
```

---

# Step 2 – Modify OU Properties

### Using ADUC

```
Right-click OU → Properties
```

Modify:

- Description  
- Managed By  
- Protection settings  

### Using PowerShell

```powershell
Set-ADOrganizationalUnit -Identity "OU=Finance,DC=domain,DC=com" -Description "Finance Department OU"
```

---

# Step 3 – Delegate Control of an OU

### Using ADUC

```
Right-click OU → Delegate Control
```

Delegate:

- Reset passwords  
- Create/delete user accounts  
- Manage groups  
- Read/write permissions  

### Using PowerShell

```powershell
Add-ADPermission -Identity "OU=Finance,DC=domain,DC=com" -User "jsmith" -AccessRights GenericWrite
```

---

# Step 4 – Move Objects Between OUs

### Using ADUC

Drag and drop objects into the correct OU.

### Using PowerShell

```powershell
Move-ADObject -Identity "CN=PC-001,OU=Workstations,DC=domain,DC=com" -TargetPath "OU=Finance,DC=domain,DC=com"
```

---

# Step 5 – Apply Group Policies to OUs

### Using GPMC

```
Group Policy Management
Right-click OU → Link Existing GPO
```

### Using PowerShell

```powershell
New-GPLink -Name "Finance-GPO" -Target "OU=Finance,DC=domain,DC=com"
```

---

# Step 6 – Protect OUs from Accidental Deletion

### Using ADUC

```
OU → Properties → Object → Protect from accidental deletion
```

### Using PowerShell

```powershell
Set-ADOrganizationalUnit -Identity "OU=Finance,DC=domain,DC=com" -ProtectedFromAccidentalDeletion $true
```

---

# Step 7 – OU Design Best Practices

- Use **department-based** or **location-based** structure  
- Avoid deep nesting (keep structure simple)  
- Do not store objects in default containers  
- Separate **Users**, **Computers**, **Groups**, **Service Accounts**  
- Apply GPOs at OU level, not domain level  
- Use naming conventions (e.g., *OU-Finance*, *OU-Workstations*)  

---

# Step 8 – Advanced OU Management

## Check OU Replication

```powershell
repadmin /showrepl
```

## Export OU Structure

```powershell
Get-ADOrganizationalUnit -Filter * | Select Name,DistinguishedName
```

## Remove an OU

```powershell
Set-ADOrganizationalUnit -Identity "OU=OldOU,DC=domain,DC=com" -ProtectedFromAccidentalDeletion $false
Remove-ADOrganizationalUnit -Identity "OU=OldOU,DC=domain,DC=com"
```

---

# Verification

Confirm:

- Objects placed in correct OUs  
- GPOs applied successfully  
- Delegation works as expected  
- OU protected from accidental deletion  
- Replication consistent across DCs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| GPO not applying | Wrong OU placement |
| OU cannot be deleted | Accidental deletion protection enabled |
| Delegation not working | Incorrect permissions |
| Computers in default container | Domain join misconfiguration |
| Replication delays | DC sync issues |

---

# Preventive Best Practices

- Maintain clean OU structure  
- Avoid storing objects in *Users* or *Computers* containers  
- Regularly audit OU permissions  
- Document OU design and delegation  
- Use GPO inheritance wisely  
- Protect all OUs from accidental deletion  

---

# Escalation Criteria

Escalate when:

- OU permissions break delegation  
- GPOs fail across multiple OUs  
- OU replication issues detected  
- OU corruption suspected  
- Domain-wide policy conflicts occur  

---

# References

- Microsoft Learn – Organizational Units  
- Microsoft Learn – Group Policy Management  
- Microsoft Learn – PowerShell AD Module  
