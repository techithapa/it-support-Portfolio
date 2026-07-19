a
# Microsoft 365 Knowledge Base  
## 20 — User Onboarding

---

## Overview

User onboarding is the structured process of preparing new employees with the accounts, access, devices, and tools they need to work effectively in Microsoft 365. A consistent onboarding workflow reduces errors, improves security, and ensures new users are productive from day one.

This document covers:
- Pre‑onboarding requirements  
- Account creation  
- Licensing  
- Group membership  
- Device setup  
- Email and collaboration tools  
- Security configuration  
- Verification steps  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **User Account** | Identity used to access Microsoft 365 services |
| **Licensing** | Assigns apps and services to the user |
| **Group Membership** | Controls access to resources |
| **Conditional Access** | Enforces security requirements |
| **Autopilot** | Automated Windows device provisioning |
| **Onboarding Checklist** | Ensures consistent onboarding |

---

## Tools Used

- Microsoft 365 Admin Center  
- Microsoft Entra Admin Center  
- Intune Admin Center  
- Exchange Admin Center  
- Teams Admin Center  
- PowerShell (Graph, AzureAD, MSOnline)  

---

# 1. Pre‑Onboarding Requirements

## 1.1 Information Needed

- Full name  
- Username (UPN)  
- Department  
- Job role  
- Manager  
- Location  
- Required licenses  
- Required groups  
- Required applications  
- Device type (Windows, macOS, mobile)  

---

## 1.2 Create Onboarding Ticket

Include:
- Start date  
- Required access  
- Hardware needs  
- Special permissions (if any)  

---

# 2. Create User Account

## 2.1 Microsoft Entra Admin Center

```
Identity → Users → New User
```

Set:
- Display name  
- Username  
- Password (temporary)  
- Usage location  

---

## 2.2 PowerShell (Graph)

```powershell
New-MgUser -DisplayName "John Smith" `
-UserPrincipalName "john.smith@domain.com" `
-AccountEnabled $true `
-PasswordProfile @{Password="TempPass123!"}
```

---

# 3. Assign Licenses

## 3.1 Microsoft 365 Admin Center

```
Users → Active Users → Select User → Licenses and Apps
```

Common licenses:
- Microsoft 365 Business Standard  
- Microsoft 365 E3/E5  
- Teams Phone  
- Intune  
- Defender for Endpoint  

---

## 3.2 PowerShell

```powershell
Set-MgUserLicense -UserId john.smith@domain.com `
-AddLicenses @{SkuId="<LicenseGUID>"}
```

---

# 4. Group Membership

## 4.1 Add User to Security Groups

Examples:
- Department groups  
- Application access groups  
- Distribution lists  
- Microsoft 365 Groups  

### PowerShell
```powershell
Add-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

---

## 4.2 Dynamic Groups

User automatically added based on attributes (e.g., department).

---

# 5. Email & Collaboration Setup

## 5.1 Exchange Online

Mailbox created automatically with license.

### Configure mailbox settings:
- Time zone  
- Language  
- Signature (optional)  

---

## 5.2 Teams

User automatically enabled for Teams.

Check:
- Teams policies  
- Calling policies  
- Meeting policies  

---

## 5.3 OneDrive

Provisioned automatically.

Verify:
- Storage quota  
- Sharing settings  

---

## 5.4 SharePoint Access

Add user to:
- Department sites  
- Project sites  
- Communication sites  

---

# 6. Device Setup

## 6.1 Windows Device (Autopilot Recommended)

Steps:
1. Assign Autopilot profile  
2. User signs in  
3. Device auto‑configures  
4. Apps and policies applied  

---

## 6.2 Manual Setup (Windows/macOS)

Install:
- Microsoft 365 Apps  
- Teams  
- OneDrive  
- Defender  
- Company Portal  

Enroll device in Intune:
```
Settings → Accounts → Access work or school → Connect
```

---

## 6.3 Mobile Device Setup

Install:
- Outlook  
- Teams  
- OneDrive  
- Company Portal  

Enroll device for compliance.

---

# 7. Security Configuration

## 7.1 MFA Enrollment

Require user to register:
- Microsoft Authenticator  
- Backup method (SMS or phone)  

---

## 7.2 Conditional Access

Policies applied:
- Require MFA  
- Require compliant device  
- Block legacy authentication  

---

## 7.3 App Protection Policies (BYOD)

Protect corporate data on personal devices.

---

## 7.4 Defender for Endpoint

Ensure device is onboarded.

---

# 8. Additional Access (Optional)

- Shared mailboxes  
- Distribution lists  
- Shared calendars  
- Admin roles  
- Application access (Salesforce, Jira, etc.)  
- VPN access  

---

# 9. Onboarding Checklist

| Task | Completed |
|------|-----------|
| User account created | ✔ |
| License assigned | ✔ |
| Groups assigned | ✔ |
| MFA configured | ✔ |
| Conditional Access applied | ✔ |
| Mailbox provisioned | ✔ |
| Teams enabled | ✔ |
| OneDrive provisioned | ✔ |
| SharePoint access granted | ✔ |
| Device enrolled | ✔ |
| Apps installed | ✔ |
| Security policies applied | ✔ |

---

# 10. Troubleshooting

## Issue 1 — User cannot sign in

### Causes
- Password expired  
- MFA not configured  
- Conditional Access block  

### Fix
- Reset password  
- Complete MFA setup  
- Review CA policies  

---

## Issue 2 — Mailbox not created

### Causes
- License not assigned  
- Sync delay  

### Fix
- Assign license  
- Wait 5–30 minutes  

---

## Issue 3 — Teams not working

### Causes
- License missing  
- Policy restrictions  

### Fix
- Assign Teams license  
- Review Teams policies  

---

## Issue 4 — Device not compliant

### Causes
- Missing PIN  
- Encryption disabled  

### Fix
- Enable PIN  
- Enable BitLocker/FileVault  

---

# 11. Best Practices

- Use templates for onboarding  
- Automate with PowerShell or workflows  
- Use Autopilot for Windows deployment  
- Apply least privilege access  
- Document onboarding steps  
- Review access after 30 days  
- Train users on Microsoft 365 basics  
- Maintain onboarding checklist  

---

# References

- Microsoft Learn — Microsoft Entra ID  
- Microsoft Learn — Intune Device Management  
- Microsoft Learn — Microsoft 365 Admin Center  
- Microsoft Learn — Exchange Online  
