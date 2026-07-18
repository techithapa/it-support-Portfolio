# Password Reset and Account Unlock (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 02 — Beginner Difficulty

---

# Table of Contents

- Overview  
- Symptoms  
- Environment  
- Common Tasks  
- Tools Used  
- Step 1 – Reset a User Password  
- Step 2 – Unlock a User Account  
- Step 3 – Force Password Change at Next Logon  
- Step 4 – Configure Password Policies  
- Step 5 – Manage Account Lockout Policies  
- Step 6 – Identify Account Lockout Sources  
- Step 7 – Reset Password Using PowerShell  
- Step 8 – Unlock Account Using PowerShell  
- Step 9 – Advanced Password & Account Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Password resets and account unlocks are among the most frequent tasks performed by Active Directory administrators. Proper handling ensures users regain access quickly while maintaining security and compliance with organizational policies.

This document provides a structured guide for resetting passwords, unlocking accounts, and managing password-related policies in Active Directory.

---

# Symptoms

Users may report:

- “Incorrect password”  
- “Your account has been locked out”  
- Unable to sign in to Windows  
- Unable to access Outlook, Teams, or network shares  
- MFA prompts failing due to password mismatch  
- Repeated lockouts after password change  

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Authentication | Kerberos / NTLM |
| Management Tools | ADUC, PowerShell, RSAT |

---

# Common Tasks

- Reset user passwords  
- Unlock locked accounts  
- Force password change at next logon  
- Configure password policies  
- Investigate repeated lockouts  
- Assist users after password updates  
- Manage account lockout thresholds  

---

# Tools Used

- Active Directory Users and Computers (ADUC)  
- PowerShell  
- Group Policy Management  
- Microsoft Account Lockout Tools  
- Event Viewer  

---

# Step 1 – Reset a User Password

### Using ADUC

```
Active Directory Users and Computers
Right-click user → Reset Password
```

Enter:

- New password  
- Confirm password  
- Optionally check “User must change password at next logon”

### Using PowerShell

```powershell
Set-ADAccountPassword -Identity jsmith -NewPassword (Read-Host -AsSecureString "Enter New Password") -Reset
```

---

# Step 2 – Unlock a User Account

### Using ADUC

```
Right-click user → Properties → Account → Unlock Account
```

### Using PowerShell

```powershell
Unlock-ADAccount -Identity jsmith
```

---

# Step 3 – Force Password Change at Next Logon

### Using ADUC

```
User → Properties → Account → User must change password at next logon
```

### Using PowerShell

```powershell
Set-ADUser jsmith -ChangePasswordAtLogon $true
```

---

# Step 4 – Configure Password Policies

Password policies are configured at the domain level.

Open:

```
Group Policy Management
Default Domain Policy
Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy
```

Configure:

- Minimum password length  
- Password complexity  
- Maximum password age  
- Password history  
- Reversible encryption (should be disabled)  

---

# Step 5 – Manage Account Lockout Policies

Navigate to:

```
Group Policy Management
Default Domain Policy
Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy
```

Configure:

- Account lockout threshold  
- Account lockout duration  
- Reset account lockout counter  

---

# Step 6 – Identify Account Lockout Sources

Use Microsoft ALTools:

- **LockoutStatus.exe**  
- **EventCombMT.exe**

Check DC logs:

```
Event Viewer → Windows Logs → Security
Event ID 4740 – Account Locked Out
Event ID 4625 – Failed Logon
```

PowerShell:

```powershell
Get-ADUser jsmith -Properties BadPwdCount,LastBadPasswordAttempt
```

---

# Step 7 – Reset Password Using PowerShell

```powershell
Set-ADAccountPassword -Identity jsmith -NewPassword (Read-Host -AsSecureString "Enter Password") -Reset
```

---

# Step 8 – Unlock Account Using PowerShell

```powershell
Unlock-ADAccount -Identity jsmith
```

---

# Step 9 – Advanced Password & Account Management

## Clear Cached Credentials

```
Control Panel → Credential Manager → Windows Credentials → Remove stale entries
```

## Check Kerberos Tickets

```powershell
klist
klist purge
```

## Check for stale credentials on devices

- Outlook  
- Teams  
- OneDrive  
- VPN clients  
- Mobile devices  

## Reset Service Account Password

```powershell
Set-ADAccountPassword svcBackup -NewPassword (Read-Host -AsSecureString "Enter Password") -Reset
```

---

# Verification

Confirm:

- User can log in  
- No repeated lockouts  
- Password synchronized across devices  
- Kerberos tickets valid  
- No new Event ID 4740 entries  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| User still locked out | Stale credentials on multiple devices |
| Password reset not working | Incorrect UPN or SAMAccountName |
| Account keeps locking | Service or scheduled task using old password |
| MFA failing | Password mismatch across devices |
| Cannot reset password | Password policy violation |

---

# Preventive Best Practices

- Educate users on password updates  
- Use strong password policies  
- Avoid hardcoded service account passwords  
- Monitor lockout events  
- Use Entra ID MFA for hybrid environments  
- Rotate service account passwords regularly  

---

# Escalation Criteria

Escalate when:

- Account repeatedly locks despite resets  
- Brute-force attack suspected  
- Password resets fail across multiple DCs  
- Kerberos ticket corruption persists  
- Service account lockouts impact production systems  

---

# References

- Microsoft Learn – Active Directory Users  
- Microsoft Learn – Password Policies  
- Microsoft Learn – Account Lockout Tools  
- Microsoft Learn – Kerberos Authentication  
