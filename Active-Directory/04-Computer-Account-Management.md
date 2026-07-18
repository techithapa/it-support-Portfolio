# Computer Account Management (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 04 — Beginner Difficulty

---

# Table of Contents

- Overview  
- Computer Account Basics  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Create a Computer Account  
- Step 2 – Reset a Computer Account  
- Step 3 – Move Computer Accounts Between OUs  
- Step 4 – Disable/Enable Computer Accounts  
- Step 5 – Rename Computer Accounts  
- Step 6 – Join a Computer to the Domain  
- Step 7 – Remove a Computer from the Domain  
- Step 8 – Manage Computer Account Attributes  
- Step 9 – Advanced Computer Account Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Computer accounts represent domain‑joined devices in Active Directory. They allow Windows systems to authenticate, apply Group Policies, access resources, and participate in domain security.

Proper computer account management ensures devices remain healthy, compliant, and correctly associated with their organizational units (OUs).

---

# Computer Account Basics

A computer account:

- Is created automatically when a device joins the domain  
- Stores authentication secrets  
- Receives Group Policy Objects (GPOs)  
- Must remain synchronized with the domain  
- Can become stale or broken if not maintained  

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

- Create computer accounts  
- Reset computer accounts  
- Move computers between OUs  
- Disable/enable computer accounts  
- Rename computer accounts  
- Join/leave domain  
- Manage computer attributes  
- Clean up stale computer accounts  

---

# Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell  
- Group Policy Management  
- Event Viewer  
- RSAT Tools  

---

# Troubleshooting Workflow

```
        Computer Account Issue
                 │
                 ▼
       Check Domain Join Status
                 │
                 ▼
       Reset Computer Account
                 │
                 ▼
     Validate OU Placement & GPOs
                 │
                 ▼
     Check Secure Channel (Trust)
                 │
                 ▼
     Review Event Logs on Device
                 │
                 ▼
             Apply Fixes
                 │
                 ▼
             Verify Domain Sync
```

---

# Step 1 – Create a Computer Account

### Using ADUC

```
Active Directory Users and Computers
Right-click OU → New → Computer
```

Enter:

- Computer name  
- Pre‑stage account (optional)  

### Using PowerShell

```powershell
New-ADComputer -Name "PC-001" -Path "OU=Workstations,DC=domain,DC=com"
```

---

# Step 2 – Reset a Computer Account

Used when trust relationship breaks.

### Using ADUC

```
Right-click computer → Reset Account
```

### Using PowerShell

```powershell
Reset-ADComputer -Identity "PC-001"
```

---

# Step 3 – Move Computer Accounts Between OUs

### Using ADUC

Drag and drop the computer into the correct OU.

### Using PowerShell

```powershell
Move-ADObject -Identity "CN=PC-001,OU=Workstations,DC=domain,DC=com" -TargetPath "OU=Finance,DC=domain,DC=com"
```

---

# Step 4 – Disable/Enable Computer Accounts

### Disable

```powershell
Disable-ADAccount -Identity "PC-001"
```

### Enable

```powershell
Enable-ADAccount -Identity "PC-001"
```

### Using ADUC

```
Right-click computer → Disable Account
```

---

# Step 5 – Rename Computer Accounts

### Using ADUC

```
Right-click computer → Rename
```

### Using PowerShell

```powershell
Rename-Computer -NewName "PC-002" -Restart
```

---

# Step 6 – Join a Computer to the Domain

### Using System Settings

```
Settings → System → About → Rename this PC (Advanced)
```

Enter:

- Domain name  
- Domain credentials  

### Using PowerShell

```powershell
Add-Computer -DomainName "domain.com" -Credential domain\admin -Restart
```

---

# Step 7 – Remove a Computer from the Domain

### Using System Settings

```
Settings → System → About → Disconnect from organization
```

### Using PowerShell

```powershell
Remove-Computer -UnjoinDomainCredential domain\admin -Restart
```

---

# Step 8 – Manage Computer Account Attributes

### Using ADUC

```
Computer → Properties
```

Modify:

- Description  
- Location  
- Managed By  
- OS version (auto-populated)  

### Using PowerShell

```powershell
Set-ADComputer -Identity "PC-001" -Description "Finance workstation"
```

---

# Step 9 – Advanced Computer Account Management

## Check Secure Channel (Trust Relationship)

On the affected PC:

```powershell
Test-ComputerSecureChannel
```

Fix trust:

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

---

## Clean Up Stale Computer Accounts

```powershell
Search-ADAccount -AccountInactive -ComputersOnly
```

---

## Check GPO Application

```powershell
gpresult /r
```

---

## Check Domain Connectivity

```powershell
nltest /dsgetdc:domain.com
nltest /sc_verify:domain.com
```

---

# Verification

Confirm:

- Computer appears in correct OU  
- GPOs apply successfully  
- Secure channel is healthy  
- Device authenticates to domain  
- No trust relationship errors  
- No replication delays  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| “Trust relationship failed” | Broken secure channel |
| Computer not receiving GPOs | Wrong OU placement |
| Computer missing in AD | Deleted or stale account |
| Domain join fails | DNS or credential issues |
| Duplicate computer names | Improper renaming |

---

# Preventive Best Practices

- Use naming conventions (e.g., PC‑Dept‑Number)  
- Store computers in correct OUs  
- Avoid placing computers in the default Computers container  
- Regularly clean stale accounts  
- Monitor domain join failures  
- Ensure DNS is healthy  

---

# Escalation Criteria

Escalate when:

- Secure channel repeatedly breaks  
- Domain join fails across multiple devices  
- Computer accounts disappear unexpectedly  
- GPOs fail despite correct OU placement  
- AD replication issues detected  

---

# References

- Microsoft Learn – Active Directory Computer Accounts  
- Microsoft Learn – Domain Join  
- Microsoft Learn – PowerShell AD Module  
