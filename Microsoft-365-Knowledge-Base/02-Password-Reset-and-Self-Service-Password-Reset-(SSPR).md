
# Microsoft 365 Knowledge Base  
## 02 — Password Reset and Self‑Service Password Reset (SSPR)

---

## Overview

Password Reset and Self‑Service Password Reset (SSPR) in Microsoft 365 allow users to securely reset or change their passwords without IT intervention. Proper configuration reduces helpdesk workload, improves security, and ensures compliance with identity protection policies.

This document covers:
- Admin‑initiated password resets  
- User‑initiated SSPR  
- Unlocking accounts  
- Common issues and troubleshooting  
- Best practices for secure password management  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Password Reset** | Admin resets a user’s password manually |
| **SSPR** | Users reset their own passwords using verification methods |
| **Password Writeback** | Allows cloud password resets to update on‑prem AD |
| **Authentication Methods** | Phone, email, Authenticator app, security questions |
| **Account Lockout** | Triggered by repeated failed sign‑ins |

---

## Tools Used

- Microsoft 365 Admin Center  
- Microsoft Entra Admin Center  
- PowerShell (MSOnline, AzureAD, Graph)  
- Azure AD Connect (for hybrid writeback)  

---

# 1. Admin‑Initiated Password Reset

## 1.1 Microsoft 365 Admin Center

```
Users → Active Users → Select User → Reset Password
```

Options:
- Auto‑generate password  
- Require user to change password at next sign‑in  
- Send password to alternate email (optional)

---

## 1.2 PowerShell (MSOnline)

```powershell
Set-MsolUserPassword -UserPrincipalName jsmith@domain.com `
-NewPassword "NewPass123!" -ForceChangePassword $true
```

---

## 1.3 PowerShell (Graph)

```powershell
Update-MgUser -UserId jsmith@domain.com `
-PasswordProfile @{Password="NewPass123!"; ForceChangePasswordNextSignIn=$true}
```

---

# 2. Self‑Service Password Reset (SSPR)

## 2.1 What SSPR Allows Users To Do

- Reset forgotten passwords  
- Change existing passwords  
- Unlock their accounts  
- Update authentication methods  

---

## 2.2 Enabling SSPR

### Entra Admin Center

```
Identity → Users → Password Reset
```

Configure:
- SSPR enabled for:  
  - All users  
  - Selected groups  
- Authentication methods  
- Registration requirements  
- Notifications  

---

## 2.3 Authentication Methods Supported

| Method | Description |
|--------|-------------|
| **Mobile phone** | SMS or call verification |
| **Email** | Secondary email verification |
| **Microsoft Authenticator** | Push notification or code |
| **Security questions** | Custom questions (not recommended for high security) |

---

## 2.4 User Registration Portal

Users register their methods at:

```
https://aka.ms/ssprsetup
```

---

## 2.5 User Password Reset Portal

Users reset their passwords at:

```
https://aka.ms/sspr
```

---

# 3. Password Writeback (Hybrid Environments)

Password Writeback allows SSPR changes in the cloud to update on‑prem AD.

### Requirements

- Azure AD Connect installed  
- Password Writeback enabled  
- Proper permissions on AD DS  

### Enable Password Writeback

```
Azure AD Connect → Customize Synchronization Options → Password Writeback
```

---

# 4. Unlocking Accounts

## 4.1 Admin Unlock (Cloud)

### Entra Admin Center

```
User → Authentication Methods → Unlock Account
```

### PowerShell

```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -StrongAuthenticationRequirements @()
```

---

## 4.2 User Unlock (SSPR)

Users can unlock their accounts without resetting passwords if enabled.

---

# 5. Common Issues & Troubleshooting

## Issue 1 — User cannot reset password via SSPR

### Causes
- SSPR not enabled  
- User not in SSPR group  
- Missing authentication methods  
- Conditional Access blocking reset  

### Fix
- Verify SSPR settings  
- Check user registration  
- Review CA policies  

---

## Issue 2 — Password Writeback not working

### Causes
- Azure AD Connect misconfiguration  
- Writeback not enabled  
- Service account permissions missing  

### Fix
- Re‑run Azure AD Connect wizard  
- Validate permissions  
- Check sync logs  

---

## Issue 3 — User locked out after password reset

### Causes
- Cached credentials on device  
- MFA misconfiguration  
- Conditional Access restrictions  

### Fix
- Clear cached credentials  
- Re‑register MFA  
- Review CA policies  

---

## Issue 4 — Admin cannot reset password

### Causes
- Privilege restrictions  
- Role assignment missing  

### Fix
Assign one of:
- Helpdesk Administrator  
- Password Administrator  
- User Administrator  

---

# 6. Verification Checklist

- User can sign in with new password  
- MFA prompts working  
- SSPR registration completed  
- Password Writeback functioning (hybrid)  
- No lockout or CA issues  
- Logs show successful reset  

---

# 7. Best Practices

- Require MFA for SSPR  
- Enable Password Writeback for hybrid environments  
- Avoid security questions (weak method)  
- Enforce strong password policies  
- Use Conditional Access to protect password reset portals  
- Train users to register authentication methods early  
- Monitor password reset logs  

---

# References

- Microsoft Learn — Self‑Service Password Reset  
- Microsoft Learn — Password Writeback  
- Microsoft Learn — Microsoft 365 User Management  
