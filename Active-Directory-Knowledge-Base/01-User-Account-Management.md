
# ✅ **01‑User‑Account‑Management.md**

Each document will follow the same professional structure you used in your AD Knowledge Base:  
- Clear headings  
- Tables  
- Workflows  
- PowerShell examples  
- Troubleshooting  
- Verification  
- Best practices  

No filler, no conversational text — **pure documentation** ready to paste directly into GitHub.

---

# 📘 **01‑User‑Account‑Management.md**

```md
# Microsoft 365 Knowledge Base  
## 01 — User Account Management

---

## Overview

User account management in Microsoft 365 involves creating, modifying, securing, and removing user identities across Azure Active Directory (Microsoft Entra ID), Exchange Online, Teams, SharePoint, and other M365 services. Proper account management ensures security, compliance, and smooth user operations.

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Azure AD / Entra ID** | Identity provider for Microsoft 365 |
| **User Principal Name (UPN)** | Primary login identity (e.g., user@domain.com) |
| **Licensing** | Determines available apps and services |
| **Roles** | Administrative permissions |
| **Groups** | Access control for apps, Teams, SharePoint, etc. |
| **Synchronization** | Hybrid identity via Azure AD Connect |

---

## Tools Used

- Microsoft 365 Admin Center  
- Entra Admin Center  
- Exchange Admin Center  
- PowerShell (MSOnline, AzureAD, Graph)  
- Azure AD Connect (Hybrid environments)

---

## User Lifecycle Workflow

```
Create User → Assign License → Configure Mailbox → Add to Groups
→ Configure MFA → Configure Apps → Maintain → Offboard
```

---

## Creating a User (Cloud Only)

### Microsoft 365 Admin Center

```
Users → Active Users → Add a user
```

Configure:

- Display name  
- Username (UPN)  
- Password settings  
- Roles  
- Licenses  
- Location  

### PowerShell (Graph)

```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All"

New-MgUser -DisplayName "John Smith" `
-UserPrincipalName "jsmith@domain.com" `
-PasswordProfile @{Password="Password123!"} `
-AccountEnabled $true
```

---

## Creating a User (Hybrid AD + Azure AD Connect)

### Step 1 — Create user in on‑prem AD

```powershell
New-ADUser -Name "John Smith" -GivenName "John" -Surname "Smith" `
-SamAccountName "jsmith" -UserPrincipalName "jsmith@domain.com" `
-Path "OU=Users,DC=domain,DC=com" -Enabled $true `
-AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force)
```

### Step 2 — Wait for Azure AD Connect sync  
Or force sync:

```powershell
Start-ADSyncSyncCycle -PolicyType Delta
```

---

## Assigning Licenses

### Microsoft 365 Admin Center

```
Users → Active Users → Select User → Licenses and Apps
```

### PowerShell (MSOnline)

```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com -AddLicenses "tenant:ENTERPRISEPACK"
```

---

## Managing User Roles

### Entra Admin Center

```
Identity → Roles & Administrators → Assignments
```

### PowerShell

```powershell
Add-MgRoleManagementDirectoryRoleAssignment `
-PrincipalId <UserObjectId> `
-RoleDefinitionId <RoleId>
```

---

## Managing Groups

### Add user to group

```powershell
Add-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

### Common group types

| Group Type | Purpose |
|------------|---------|
| **Security Group** | Access control |
| **Microsoft 365 Group** | Teams, SharePoint, Planner |
| **Distribution List** | Email distribution |

---

## Resetting User Password

### Admin Center

```
Users → Active Users → Reset Password
```

### PowerShell

```powershell
Set-MsolUserPassword -UserPrincipalName jsmith@domain.com -NewPassword "NewPass123!" -ForceChangePassword $true
```

---

## Blocking Sign‑In

### Admin Center

```
User → Block sign-in = On
```

### PowerShell

```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -BlockCredential $true
```

---

## Deleting a User

### Admin Center

```
Users → Active Users → Delete User
```

### PowerShell

```powershell
Remove-MsolUser -UserPrincipalName jsmith@domain.com -Force
```

---

## Restoring a Deleted User

### Admin Center

```
Users → Deleted Users → Restore
```

### PowerShell

```powershell
Restore-MsolUser -UserPrincipalName jsmith@domain.com
```

---

## Troubleshooting

### User not syncing to Microsoft 365

- Check Azure AD Connect health  
- Ensure user is in a synced OU  
- Check UPN suffix  
- Check sync errors in Synchronization Service Manager  

### User cannot log in

- Check license assignment  
- Check MFA configuration  
- Check sign‑in blocked status  
- Check password reset  

### User missing apps

- Verify license SKU  
- Check service plan toggles  
- Check Conditional Access policies  

---

## Verification Checklist

- User appears in Microsoft 365 Admin Center  
- License assigned  
- Mailbox created  
- MFA configured  
- Teams/SharePoint access working  
- No sync errors  
- No sign‑in errors  

---

## Best Practices

- Standardize naming conventions  
- Use automation for onboarding/offboarding  
- Enforce MFA for all users  
- Use Conditional Access  
- Use security groups for access control  
- Avoid assigning admin roles directly  
- Document user lifecycle processes  
