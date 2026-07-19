
# Microsoft 365 Knowledge Base  
## 16 — Azure AD (Microsoft Entra ID) Administration

---

## Overview

Azure Active Directory (Azure AD), now known as **Microsoft Entra ID**, is the identity and access management service for Microsoft 365. It provides authentication, authorization, user and group management, device identity, Conditional Access, and security controls essential for modern cloud environments.

This document covers:
- Core identity concepts  
- User and group management  
- Roles and administrative units  
- Authentication methods  
- Conditional Access  
- Identity protection  
- Enterprise applications  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Tenant** | The dedicated Microsoft Entra ID instance for an organization |
| **User Object** | Represents a user identity in the directory |
| **Group** | Security or Microsoft 365 group for access control |
| **Role** | Defines administrative permissions |
| **Conditional Access** | Policy-based access control |
| **Identity Protection** | Risk-based detection and remediation |
| **Enterprise Applications** | SSO and app registrations |
| **Devices** | Registered or joined devices for identity-based access |

---

## Tools Used

- Microsoft Entra Admin Center  
- Microsoft 365 Admin Center  
- PowerShell (MSOnline, AzureAD, Graph)  
- Intune Admin Center (for device identity)  
- Security & Compliance Center  

---

# 1. User Management

## 1.1 Create User

### Entra Admin Center
```
Identity → Users → All Users → New User
```

### PowerShell (Graph)
```powershell
New-MgUser -DisplayName "John Smith" `
-UserPrincipalName "jsmith@domain.com" `
-AccountEnabled $true `
-PasswordProfile @{Password="TempPass123!"}
```

---

## 1.2 Manage User Properties

Common properties:
- Display name  
- Username (UPN)  
- Usage location  
- Licenses  
- Authentication methods  
- Assigned roles  

### Set Usage Location
```powershell
Set-MgUser -UserId jsmith@domain.com -UsageLocation "AU"
```

---

## 1.3 Delete and Restore Users

### Delete
```powershell
Remove-MgUser -UserId jsmith@domain.com
```

### Restore (within 30 days)
```powershell
Restore-MgDirectoryDeletedItem -DirectoryObjectId <ObjectId>
```

---

# 2. Group Management

## 2.1 Group Types

| Group Type | Purpose |
|------------|---------|
| **Security Group** | Access control for apps/resources |
| **Microsoft 365 Group** | Collaboration (Teams, SharePoint, Planner) |
| **Dynamic Group** | Membership based on user/device attributes |

---

## 2.2 Create Security Group

```powershell
New-MgGroup -DisplayName "Finance Team" `
-SecurityEnabled $true -MailEnabled $false
```

---

## 2.3 Dynamic Group Example

### Rule: All users in Department = "Sales"
```powershell
New-MgGroup -DisplayName "Sales Users" `
-GroupTypes "DynamicMembership" `
-MembershipRule "(user.department -eq ""Sales"")" `
-MembershipRuleProcessingState "On"
```

---

# 3. Role Management

## 3.1 Assign Admin Roles

Common roles:
- Global Administrator  
- User Administrator  
- Exchange Administrator  
- Teams Administrator  
- Security Administrator  
- Privileged Role Administrator  

### Assign Role
```powershell
Add-MgRoleManagementDirectoryRoleAssignment `
-PrincipalId <UserId> -RoleDefinitionId <RoleId>
```

---

## 3.2 Privileged Identity Management (PIM)

PIM provides:
- Just-in-time role activation  
- Approval workflows  
- Role usage auditing  

---

# 4. Authentication Methods

## 4.1 Supported Methods

- Password  
- Microsoft Authenticator  
- FIDO2 keys  
- SMS  
- Voice call  
- Temporary Access Pass (TAP)  

---

## 4.2 Manage Authentication Methods

### View Methods
```powershell
Get-MgUserAuthenticationMethod -UserId jsmith@domain.com
```

### Create TAP
```powershell
New-MgUserAuthenticationTemporaryAccessPassMethod `
-UserId jsmith@domain.com `
-IsUsableOnce $true `
-ExpirationDateTime (Get-Date).AddHours(8)
```

---

# 5. Conditional Access

Conditional Access enforces security requirements based on user, device, location, and risk.

## 5.1 Common CA Policies

- Require MFA for all users  
- Block legacy authentication  
- Require compliant device  
- Require MFA for admin roles  
- Restrict access by location  

---

## 5.2 Create Conditional Access Policy

### Entra Admin Center
```
Protection → Conditional Access → New Policy
```

### Recommended Settings
- **Users:** All users (exclude break-glass)  
- **Apps:** All cloud apps  
- **Conditions:** Sign-in risk, device platform  
- **Grant:** Require MFA  

---

# 6. Identity Protection

Identity Protection detects:
- Risky sign-ins  
- Risky users  
- Risky credentials  

## 6.1 Risk Policies

- User risk policy  
- Sign-in risk policy  
- MFA registration policy  

---

## 6.2 View Risky Users

```powershell
Get-MgRiskyUser
```

---

# 7. Enterprise Applications

## 7.1 App Types

- Enterprise Applications (SSO)  
- App Registrations (custom apps)  

---

## 7.2 Assign Users to Applications

```powershell
New-MgServicePrincipalAppRoleAssignment `
-PrincipalId <UserId> `
-ResourceId <ServicePrincipalId> `
-AppRoleId <RoleId>
```

---

## 7.3 SSO Methods

- SAML  
- OAuth2  
- OpenID Connect  

---

# 8. Device Identity

## 8.1 Device Types

- Azure AD Registered  
- Azure AD Joined  
- Hybrid Joined  

---

## 8.2 View Devices

```powershell
Get-MgDevice
```

---

## 8.3 Disable Device

```powershell
Update-MgDevice -DeviceId <DeviceId> -AccountEnabled $false
```

---

# 9. Troubleshooting Azure AD / Entra ID

## Issue 1 — User cannot sign in

### Causes
- Incorrect password  
- MFA failure  
- Conditional Access block  

### Fix
- Reset password  
- Check CA policies  
- Review sign-in logs  

---

## Issue 2 — Group membership not updating

### Causes
- Dynamic rule error  
- Sync delay  

### Fix
- Validate rule syntax  
- Wait up to 30 minutes  

---

## Issue 3 — Admin role not applying

### Causes
- PIM activation required  
- Role assignment delay  

### Fix
- Activate via PIM  
- Wait for propagation  

---

## Issue 4 — Device not compliant

### Causes
- Intune policy failure  
- OS version too old  

### Fix
- Review compliance policies  
- Update device  

---

# 10. Verification Checklist

- User created and licensed  
- Groups assigned  
- Roles applied correctly  
- Authentication methods configured  
- Conditional Access enforced  
- Enterprise apps working  
- Devices registered  
- No sign-in errors  

---

# 11. Best Practices

- Use Conditional Access for Zero Trust  
- Enforce MFA for all users  
- Use PIM for admin roles  
- Avoid Global Admin except when necessary  
- Use dynamic groups for automation  
- Regularly review sign-in logs  
- Monitor risky users and sign-ins  
- Document identity governance policies  

---

# References

- Microsoft Learn — Microsoft Entra ID  
- Microsoft Learn — Conditional Access  
- Microsoft Learn — Identity Protection  
- Microsoft Learn — Enterprise Applications  
