# **Document 24 — AD Automation (PowerShell)**  
> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 24 — Intermediate Difficulty  
> *A complete automation framework for managing AD using PowerShell.*

---

# **Table of Contents**

- Overview  
- Why Automate Active Directory?  
- Environment  
- Tools Used  
- Core Concepts  
- Step 1 – PowerShell Modules for AD  
- Step 2 – Automating User Management  
- Step 3 – Automating Group Management  
- Step 4 – Automating Computer Management  
- Step 5 – Automating OU & Structure Management  
- Step 6 – Automating Group Policy  
- Step 7 – Automating AD Health Checks  
- Step 8 – Automating AD Security  
- Step 9 – Automating Azure AD / Entra ID (Hybrid)  
- Step 10 – Scheduling & Orchestrating Automation  
- Troubleshooting Automation Issues  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# **Overview**

PowerShell automation is essential for modern Active Directory administration. It reduces manual effort, eliminates human error, improves consistency, and enables large‑scale identity management.

This document provides a complete automation framework for AD environments, covering user lifecycle, groups, computers, OUs, GPOs, health checks, and hybrid identity.

---

# **Why Automate Active Directory?**

Automation provides:

- Faster onboarding/offboarding  
- Consistent configuration  
- Reduced human error  
- Repeatable processes  
- Improved security  
- Scalable identity management  
- Integration with HR systems  

---

# **Environment**

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Automation | PowerShell 5/7 |
| Identity Sync | Azure AD Connect / Entra Connect |
| Email | Exchange / Microsoft 365 |
| Security | LAPS, Conditional Access |

---

# **Tools Used**

- PowerShell AD Module  
- PowerShell Graph SDK  
- PowerShell MSOnline Module  
- Task Scheduler  
- Group Policy PowerShell Module  
- Windows Server RSAT  

---

# **Core Concepts**

| Concept | Description |
|---------|-------------|
| **Idempotency** | Scripts produce same result every run |
| **Logging** | Track changes and errors |
| **Error Handling** | Try/Catch blocks |
| **Modular Design** | Reusable functions |
| **Security** | Least privilege execution |

---

# **Step 1 – PowerShell Modules for AD**

### Install RSAT (Windows 10/11)

```powershell
Get-WindowsCapability -Online | Where-Object Name -like "*RSAT*"
Add-WindowsCapability -Online -Name Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0
```

### Import AD module

```powershell
Import-Module ActiveDirectory
```

---

# **Step 2 – Automating User Management**

## Create a new user

```powershell
New-ADUser -Name "John Smith" -GivenName "John" -Surname "Smith" `
-SamAccountName "jsmith" -UserPrincipalName "jsmith@domain.com" `
-Path "OU=Users,DC=domain,DC=com" -Enabled $true `
-AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force)
```

## Bulk user creation (CSV)

CSV format:

```
FirstName,LastName,Username,OU
John,Smith,jsmith,OU=Users,DC=domain,DC=com
```

Script:

```powershell
Import-Csv users.csv | ForEach-Object {
    New-ADUser -Name "$($_.FirstName) $($_.LastName)" `
    -SamAccountName $_.Username `
    -UserPrincipalName "$($_.Username)@domain.com" `
    -Path $_.OU -Enabled $true `
    -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force)
}
```

## Disable user

```powershell
Disable-ADAccount -Identity jsmith
```

## Move user to Disabled OU

```powershell
Move-ADObject -Identity "CN=jsmith,OU=Users,DC=domain,DC=com" `
-TargetPath "OU=Disabled,DC=domain,DC=com"
```

---

# **Step 3 – Automating Group Management**

## Add user to group

```powershell
Add-ADGroupMember -Identity "Finance-Users" -Members "jsmith"
```

## Remove user from all groups

```powershell
Get-ADUser jsmith -Property MemberOf | Select -Expand MemberOf |
ForEach-Object { Remove-ADGroupMember -Identity $_ -Members jsmith -Confirm:$false }
```

## Create group

```powershell
New-ADGroup -Name "Finance-Users" -GroupScope Global -Path "OU=Groups,DC=domain,DC=com"
```

---

# **Step 4 – Automating Computer Management**

## Create computer object

```powershell
New-ADComputer -Name "PC-001" -Path "OU=Workstations,DC=domain,DC=com"
```

## Move computer to correct OU

```powershell
Move-ADObject -Identity "CN=PC-001,OU=Computers,DC=domain,DC=com" `
-TargetPath "OU=Workstations,DC=domain,DC=com"
```

## Reset computer account

```powershell
Reset-ADComputer -Identity "PC-001"
```

---

# **Step 5 – Automating OU & Structure Management**

## Create OU

```powershell
New-ADOrganizationalUnit -Name "Finance" -Path "DC=domain,DC=com"
```

## Protect OU from accidental deletion

```powershell
Set-ADOrganizationalUnit -Identity "OU=Finance,DC=domain,DC=com" -ProtectedFromAccidentalDeletion $true
```

---

# **Step 6 – Automating Group Policy**

## List all GPOs

```powershell
Get-GPO -All
```

## Backup GPOs

```powershell
Backup-GPO -Name "Default Domain Policy" -Path "D:\GPOBackup"
```

## Restore GPO

```powershell
Restore-GPO -Name "Default Domain Policy" -Path "D:\GPOBackup"
```

---

# **Step 7 – Automating AD Health Checks**

## Replication summary

```powershell
repadmin /replsummary
```

## DC health

```powershell
dcdiag /v
```

## DNS health

```powershell
dcdiag /test:dns
```

## Custom health script (example)

```powershell
Get-ADDomainController -Filter * | ForEach-Object {
    Write-Host "Checking $_.Name"
    repadmin /showrepl $_.Name
    dcdiag /s:$_.Name
}
```

---

# **Step 8 – Automating AD Security**

## Detect privileged group changes

```powershell
Get-EventLog -LogName Security -InstanceId 4728,4729,4732,4733 -Newest 50
```

## Find accounts with weak passwords

```powershell
Search-ADAccount -PasswordNeverExpires -UsersOnly
```

## Find stale accounts

```powershell
Search-ADAccount -AccountInactive -TimeSpan 90.00:00:00
```

---

# **Step 9 – Automating Azure AD / Entra ID (Hybrid)**

## Connect to Entra ID

```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All","Group.ReadWrite.All"
```

## Create cloud-only user

```powershell
New-MgUser -DisplayName "John Smith" -UserPrincipalName "jsmith@domain.com" `
-PasswordProfile @{Password="Password123!"} -AccountEnabled $true
```

## Assign license

```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com -AddLicenses "tenant:ENTERPRISEPACK"
```

---

# **Step 10 – Scheduling & Orchestrating Automation**

### Use Task Scheduler

- Run scripts daily/weekly  
- Automate health checks  
- Automate stale account cleanup  
- Automate GPO backups  

### Use DevOps pipelines (advanced)

- GitHub Actions  
- Azure DevOps  
- CI/CD for scripts  

---

# **Troubleshooting Automation Issues**

| Issue | Cause |
|-------|-------|
| Access denied | Missing permissions |
| Module not found | RSAT not installed |
| Sync delays | Azure AD Connect latency |
| Script fails silently | Missing error handling |
| GPO not applying | Replication or permissions |

---

# **Verification**

Confirm:

- Scripts run successfully  
- Logs generated  
- AD objects created/modified correctly  
- No errors in event logs  
- Scheduled tasks running  
- Security policies enforced  

---

# **Common Issues**

| Issue | Cause |
|-------|-------|
| Duplicate users | Non-idempotent scripts |
| Wrong OU placement | Incorrect path in script |
| Group membership incorrect | CSV errors |
| Password not set | SecureString formatting |
| Azure AD license errors | SKU mismatch |

---

# **Preventive Best Practices**

- Use version control (Git)  
- Use modular scripts  
- Use logging and error handling  
- Test in lab environment  
- Document automation workflows  
- Use least privilege service accounts  
- Review scripts quarterly  

---

# **Escalation Criteria**

Escalate when:

- Automation impacts multiple users  
- Scripts modify privileged groups incorrectly  
- Scheduled tasks fail repeatedly  
- Azure AD automation breaks licensing  
- GPO automation corrupts policies  

---

# **References**

- Microsoft Learn – PowerShell AD Module  
- Microsoft Learn – Graph PowerShell SDK  
- Microsoft Learn – Azure AD Connect  
- Microsoft Learn – Group Policy PowerShell  

