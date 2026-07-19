
# Microsoft 365 Knowledge Base  
## 03 — Multi‑Factor Authentication (MFA)

---

## Overview

Multi‑Factor Authentication (MFA) adds an additional layer of security to Microsoft 365 sign‑ins by requiring users to verify their identity using more than just a password. MFA significantly reduces the risk of account compromise, phishing attacks, and unauthorized access.

This document covers:
- MFA concepts  
- Enabling MFA  
- Authentication methods  
- Conditional Access enforcement  
- User registration  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **MFA** | Requires two or more verification methods |
| **Strong Authentication** | MFA + Conditional Access |
| **Authentication Methods** | Phone, Authenticator app, FIDO2 keys |
| **Per‑User MFA** | Legacy MFA enforcement (not recommended) |
| **Conditional Access MFA** | Modern MFA enforcement (recommended) |
| **Registration** | Users must register MFA methods before use |

---

## Tools Used

- Microsoft Entra Admin Center  
- Microsoft 365 Admin Center  
- PowerShell (Graph, MSOnline)  
- Authenticator App  
- Conditional Access Policies  

---

# 1. MFA Authentication Methods

Microsoft 365 supports multiple MFA methods:

| Method | Description |
|--------|-------------|
| **Microsoft Authenticator App** | Push notifications or verification codes |
| **SMS** | One‑time code sent via text message |
| **Phone Call** | Automated call for verification |
| **FIDO2 Security Keys** | Hardware‑based authentication |
| **Windows Hello for Business** | Biometric authentication |
| **Temporary Access Pass (TAP)** | Time‑limited password alternative |

---

# 2. Enabling MFA (Recommended Method: Conditional Access)

## 2.1 Conditional Access MFA Policy

### Entra Admin Center
```
Identity → Protection → Conditional Access → Policies → New Policy
```

Recommended configuration:
- **Assignments:** All users (exclude break‑glass accounts)  
- **Cloud apps:** All apps  
- **Conditions:** Sign‑in risk, device platform, location  
- **Grant:** Require MFA  
- **Enable policy:** On  

---

## 2.2 Legacy Per‑User MFA (Not Recommended)

### Microsoft 365 Admin Center
```
Users → Active Users → Multi‑Factor Authentication
```

Enable MFA per user.

### PowerShell (MSOnline)
```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com `
-StrongAuthenticationRequirements @(
    @{RelyingParty="*"; State="Enabled"}
)
```

---

# 3. User MFA Registration

Users register MFA methods at:

```
https://aka.ms/mfasetup
```

They can manage methods at:

```
https://mysignins.microsoft.com/security-info
```

---

# 4. Temporary Access Pass (TAP)

TAP allows users to onboard securely without needing a password.

### Create TAP
```powershell
New-MgUserAuthenticationTemporaryAccessPassMethod `
-UserId jsmith@domain.com `
-StartDateTime (Get-Date) `
-ExpirationDateTime (Get-Date).AddHours(8) `
-IsUsableOnce $true
```

---

# 5. Enforcing MFA for Admins

Admins **must always** use MFA.

### Recommended admin roles to enforce MFA:
- Global Administrator  
- Privileged Role Administrator  
- Exchange Administrator  
- SharePoint Administrator  
- Teams Administrator  

Use Conditional Access to enforce MFA for all privileged roles.

---

# 6. Troubleshooting MFA Issues

## Issue 1 — User not receiving MFA prompts

### Causes
- Conditional Access not applied  
- Legacy MFA disabled  
- Authentication method not registered  

### Fix
- Check CA policy assignments  
- Verify user MFA registration  
- Review sign‑in logs  

---

## Issue 2 — User cannot complete MFA

### Causes
- Lost phone  
- Authenticator app removed  
- No backup methods  

### Fix
- Admin generates Temporary Access Pass  
- Reset MFA methods  
- Re‑register authentication methods  

---

## Issue 3 — MFA blocked due to Conditional Access

### Causes
- Location restrictions  
- Device compliance requirements  
- Sign‑in risk policies  

### Fix
- Review CA policy conditions  
- Check user’s device compliance  
- Review Identity Protection logs  

---

## Issue 4 — MFA not enforced for some users

### Causes
- Excluded groups in CA  
- Legacy MFA disabled  
- Break‑glass accounts misconfigured  

### Fix
- Review CA policy assignments  
- Ensure no unintended exclusions  
- Validate break‑glass account configuration  

---

# 7. Verification Checklist

- User can complete MFA successfully  
- MFA enforced via Conditional Access  
- Authentication methods registered  
- No unexpected MFA bypass  
- Admin accounts protected  
- Sign‑in logs show MFA events  

---

# 8. Best Practices

- Use Conditional Access instead of per‑user MFA  
- Require Microsoft Authenticator as primary method  
- Use Temporary Access Pass for onboarding  
- Protect admin accounts with strict MFA policies  
- Avoid SMS as primary method (less secure)  
- Maintain break‑glass accounts with strong controls  
- Monitor MFA usage via Entra sign‑in logs  

---

# References

- Microsoft Learn — Multi‑Factor Authentication  
- Microsoft Learn — Conditional Access  
- Microsoft Learn — Temporary Access Pass  
