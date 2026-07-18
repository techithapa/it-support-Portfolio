# User Account Management (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 01 — Beginner Difficulty

---

# Table of Contents

- Overview  
- Responsibilities  
- Environment  
- Common Tasks  
- Tools Used  
- Step 1 – Create a New User Account  
- Step 2 – Modify User Account Attributes  
- Step 3 – Enable/Disable User Accounts  
- Step 4 – Move Users Between OUs  
- Step 5 – Assign Group Memberships  
- Step 6 – Configure User Logon Restrictions  
- Step 7 – Manage User Profile Paths  
- Step 8 – Manage Home Folders  
- Step 9 – Manage Account Expiry  
- Step 10 – Advanced User Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

User account management is one of the core responsibilities of Active Directory administrators. Proper management ensures secure access, correct permissions, and smooth onboarding/offboarding processes.

This document provides a structured guide for creating, modifying, and managing user accounts in Active Directory.

---

# Responsibilities

AD administrators typically handle:

- Creating new user accounts  
- Updating user details (name, department, job title)  
- Managing group memberships  
- Enabling/disabling accounts  
- Resetting passwords  
- Managing profile paths and home folders  
- Applying security policies  
- Ensuring compliance with organizational standards  

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

- Create new user accounts  
- Modify user attributes  
- Reset passwords  
- Unlock accounts  
- Disable accounts for offboarding  
- Move users between OUs  
- Assign security and distribution groups  
- Configure logon hours and workstation restrictions  

---

# Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell  
- Group Policy Management  
- File Explorer (Home folders)  
- Entra ID (Hybrid environments)  

---

# Step 1 – Create a New User Account

### Using ADUC

```
Active Directory Users and Computers
Right-click OU → New → User
```

Enter:

- First name  
- Last name  
- User logon name (UPN)  
- Password  
- Account options (password never expires, must change password, etc.)

### Using PowerShell

```powershell
New-ADUser -Name "John Smith" -GivenName "John" -Surname "Smith" -SamAccountName "jsmith" -UserPrincipalName "jsmith@domain.com" -AccountPassword (Read-Host -AsSecureString "Enter Password") -Enabled $true -Path "OU=Users,DC=domain,DC=com"
```

---

# Step 2 – Modify User Account Attributes

### Using ADUC

Navigate to:

```
User → Properties
```

Modify:

- General info  
- Address  
- Account settings  
- Organization (department, job title)  
- Telephones  
- Profile settings  

### Using PowerShell

```powershell
Set-ADUser jsmith -Department "Finance" -Title "Accountant"
```

---

# Step 3 – Enable/Disable User Accounts

### Disable account (offboarding)

```powershell
Disable-ADAccount jsmith
```

### Enable account

```powershell
Enable-ADAccount jsmith
```

### Using ADUC

```
Right-click user → Disable Account
```

---

# Step 4 – Move Users Between OUs

### Using ADUC

Drag and drop user into new OU.

### Using PowerShell

```powershell
Move-ADObject -Identity "CN=John Smith,OU=Users,DC=domain,DC=com" -TargetPath "OU=Finance,DC=domain,DC=com"
```

---

# Step 5 – Assign Group Memberships

### Using ADUC

```
User → Properties → Member Of → Add
```

### Using PowerShell

```powershell
Add-ADGroupMember -Identity "Finance-Group" -Members jsmith
```

Remove user from group:

```powershell
Remove-ADGroupMember -Identity "Finance-Group" -Members jsmith -Confirm:$false
```

---

# Step 6 – Configure User Logon Restrictions

### Logon Hours

```
User → Properties → Account → Logon Hours
```

### Logon Workstations

```
User → Properties → Account → Log On To
```

### PowerShell

```powershell
Set-ADUser jsmith -LogonWorkstations "PC01","PC02"
```

---

# Step 7 – Manage User Profile Paths

Used for roaming profiles.

### ADUC

```
User → Properties → Profile
Profile Path: \\server\profiles\jsmith
```

### PowerShell

```powershell
Set-ADUser jsmith -ProfilePath "\\server\profiles\jsmith"
```

---

# Step 8 – Manage Home Folders

### ADUC

```
User → Properties → Profile
Home Folder: \\server\home\jsmith
```

### PowerShell

```powershell
Set-ADUser jsmith -HomeDirectory "\\server\home\jsmith" -HomeDrive "H:"
```

---

# Step 9 – Manage Account Expiry

### Using ADUC

```
User → Properties → Account → Account Expires
```

### PowerShell

```powershell
Set-ADUser jsmith -AccountExpirationDate "2026-12-31"
```

---

# Step 10 – Advanced User Management

## Unlock Account

```powershell
Unlock-ADAccount jsmith
```

## Force Password Change at Next Logon

```powershell
Set-ADUser jsmith -ChangePasswordAtLogon $true
```

## Reset Password

```powershell
Set-ADAccountPassword jsmith -NewPassword (Read-Host -AsSecureString "Enter Password") -Reset
```

---

# Verification

Confirm:

- User can log in  
- Group memberships applied  
- Home folder accessible  
- Profile path correct  
- No account lockouts  
- Policies applied correctly  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| User cannot log in | Wrong password / disabled account |
| Incorrect permissions | Missing group membership |
| Home drive not mapping | Incorrect UNC path |
| GPO not applying | Wrong OU placement |
| Account locked | Stale credentials |

---

# Preventive Best Practices

- Use naming conventions  
- Store users in correct OUs  
- Document onboarding/offboarding  
- Use least privilege access  
- Review group memberships regularly  
- Avoid placing users in the default Users container  

---

# Escalation Criteria

Escalate when:

- User cannot authenticate across multiple systems  
- Account repeatedly locks out  
- Profile corruption suspected  
- Home folder permissions broken  
- GPO inheritance conflicts  

---

# References

- Microsoft Learn – Active Directory Users  
- Microsoft Learn – PowerShell AD Module  
- Microsoft Learn – Identity Management  
