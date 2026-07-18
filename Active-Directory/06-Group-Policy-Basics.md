# Group Policy Basics (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 06 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Is Group Policy?  
- GPO Structure (Local, Site, Domain, OU)  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Create a Group Policy Object (GPO)  
- Step 2 – Link a GPO to an OU  
- Step 3 – Edit GPO Settings  
- Step 4 – Force Group Policy Update  
- Step 5 – Check GPO Application on Clients  
- Step 6 – Manage GPO Inheritance & Precedence  
- Step 7 – Use Security Filtering & WMI Filtering  
- Step 8 – Backup, Restore, and Copy GPOs  
- Step 9 – Advanced GPO Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Group Policy is one of the most powerful features of Active Directory. It allows administrators to centrally manage security settings, software deployment, user configurations, and computer configurations across the domain.

This document provides a structured guide for understanding, creating, applying, and troubleshooting Group Policy Objects (GPOs).

---

# What Is Group Policy?

Group Policy is a framework that controls:

- User configuration  
- Computer configuration  
- Security policies  
- Software installation  
- Scripts (startup/shutdown/logon/logoff)  
- Windows settings and restrictions  

GPOs apply to:

- Sites  
- Domains  
- Organizational Units (OUs)  

---

# GPO Structure (Local, Site, Domain, OU)

| Level | Description |
|-------|-------------|
| **Local GPO** | Applies only to the local machine |
| **Site GPO** | Applies to all computers in an AD site |
| **Domain GPO** | Applies to all users/computers in the domain |
| **OU GPO** | Most common; applied to specific OUs |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Management Tools | GPMC, ADUC, PowerShell |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Create and link GPOs  
- Edit GPO settings  
- Apply GPOs to specific OUs  
- Force GPO updates  
- Troubleshoot GPO application  
- Manage GPO precedence  
- Backup and restore GPOs  

---

# Tools Used

- Group Policy Management Console (GPMC)  
- gpresult  
- RSOP.msc  
- PowerShell  
- Event Viewer  

---

# Troubleshooting Workflow

```
          GPO Issue Detected
                 │
                 ▼
       Identify OU & GPO Link Order
                 │
                 ▼
       Check GPO Inheritance & Precedence
                 │
                 ▼
     Validate Security Filtering & WMI Filters
                 │
                 ▼
     Run gpresult & RSOP on Client Device
                 │
                 ▼
     Review Event Viewer for GPO Errors
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Force GPO Update
                 │
                 ▼
             Verify Application
```

---

# Step 1 – Create a Group Policy Object (GPO)

### Using GPMC

```
Group Policy Management
Right-click Group Policy Objects → New
```

Enter:

- GPO name  
- Description (recommended)

### Using PowerShell

```powershell
New-GPO -Name "Finance-Workstation-Policy"
```

---

# Step 2 – Link a GPO to an OU

### Using GPMC

```
Right-click OU → Link an Existing GPO
```

### Using PowerShell

```powershell
New-GPLink -Name "Finance-Workstation-Policy" -Target "OU=Finance,DC=domain,DC=com"
```

---

# Step 3 – Edit GPO Settings

### Using GPMC

```
Right-click GPO → Edit
```

Configure:

- Computer Configuration  
- User Configuration  
- Administrative Templates  
- Security Settings  
- Scripts  

---

# Step 4 – Force Group Policy Update

### On Client Machine

```powershell
gpupdate /force
```

### On Remote Machine

```powershell
Invoke-GPUpdate -Computer "PC-001"
```

---

# Step 5 – Check GPO Application on Clients

### Using gpresult

```powershell
gpresult /r
gpresult /h report.html
```

### Using RSOP

```
rsop.msc
```

---

# Step 6 – Manage GPO Inheritance & Precedence

### Block Inheritance

```
Right-click OU → Block Inheritance
```

### Enforced GPO

```
Right-click GPO link → Enforced
```

### Precedence Order

Higher precedence = lower number.

---

# Step 7 – Use Security Filtering & WMI Filtering

### Security Filtering

```
GPO → Scope → Security Filtering
```

Add:

- Users  
- Groups  
- Computers  

### WMI Filtering

Used for:

- OS version  
- Hardware type  
- Laptop vs desktop  

Example:

```powershell
Select * from Win32_OperatingSystem where Version like "10.%"
```

---

# Step 8 – Backup, Restore, and Copy GPOs

### Backup

```powershell
Backup-GPO -Name "Finance-Workstation-Policy" -Path "C:\GPOBackup"
```

### Restore

```powershell
Restore-GPO -Name "Finance-Workstation-Policy" -Path "C:\GPOBackup"
```

### Copy

```powershell
Copy-GPO -SourceName "Finance-Workstation-Policy" -TargetName "Finance-Workstation-Policy-Test"
```

---

# Step 9 – Advanced GPO Management

## Check GPO Replication

```powershell
repadmin /replsummary
```

## Check SYSVOL Health

```powershell
dcdiag /test:sysvol
```

## Check GPO Version Numbers

```powershell
Get-GPO -Name "Finance-Workstation-Policy"
```

---

# Verification

Confirm:

- GPO applied successfully  
- gpresult shows correct settings  
- No GPO errors in Event Viewer  
- SYSVOL replication healthy  
- OU receives correct policies  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| GPO not applying | Wrong OU placement |
| GPO overwritten | Higher precedence GPO |
| GPO not replicating | SYSVOL/DFSR issues |
| Security filtering blocks GPO | Missing group membership |
| WMI filter excludes device | Incorrect filter logic |

---

# Preventive Best Practices

- Use descriptive GPO names  
- Document GPO purpose and scope  
- Avoid editing Default Domain Policy  
- Avoid editing Default Domain Controllers Policy  
- Use test OUs for new GPOs  
- Regularly audit GPO links and filtering  
- Keep GPO structure simple  

---

# Escalation Criteria

Escalate when:

- GPO fails across multiple OUs  
- SYSVOL replication issues detected  
- WMI filters break policy application  
- GPO corruption suspected  
- Domain-wide policy conflicts occur  

---

# References

- Microsoft Learn – Group Policy  
- Microsoft Learn – GPMC  
- Microsoft Learn – PowerShell GPO Module  
