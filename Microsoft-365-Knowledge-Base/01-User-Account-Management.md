
# Microsoft 365 Knowledge Base  
## 01 — User Account Management

---

## Overview

User account management in Microsoft 365 involves creating, modifying, securing, and removing user identities across Microsoft Entra ID (Azure AD), Exchange Online, Teams, SharePoint, and other cloud services. Proper account management ensures security, compliance, and a smooth user experience.

---

## Key Responsibilities

- Create and manage user accounts  
- Assign and modify licenses  
- Configure roles and permissions  
- Manage group memberships  
- Reset passwords and unblock accounts  
- Manage sign‑in status  
- Restore deleted users  
- Handle user profile attributes  
- Maintain compliance and security policies  

---

## Tools Used

- **Microsoft 365 Admin Center**  
- **Microsoft Entra Admin Center**  
- **Exchange Admin Center**  
- **PowerShell (MSOnline, AzureAD, Graph)**  
- **Azure AD Connect (Hybrid environments)**  

---

## User Lifecycle Stages

```
Provision → Configure → Maintain → Secure → Offboard → Archive
```

---

# 1. Creating User Accounts

## 1.1 Cloud‑Only User Creation

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

## 1.2 Hybrid User Creation (On‑Prem AD + Azure AD Connect)

### Step 1 — Create user in Active Directory
```powershell
New-ADUser -Name "John Smith" -GivenName "John" -Surname "Smith" `
-SamAccountName "jsmith" -UserPrincipalName "jsmith@domain.com" `
-Path "OU=Users,DC=domain,DC=com" -Enabled $true `
-AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force)
```

### Step 2 — Sync to Microsoft 365
```powershell
Start-ADSyncSyncCycle -PolicyType Delta
```

---

# 2. Managing User Attributes

### Common attributes to update:
- Display name  
- Job title  
- Department  
- Manager  
- Office location  
- Phone numbers  

### PowerShell
```powershell
Set-MgUser -UserId jsmith@domain.com -Department "Finance" -JobTitle "Analyst"
```

---

# 3. Managing Licenses

### Admin Center
```
Users → Active Users → Select User → Licenses and Apps
```

### PowerShell (MSOnline)
```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com -AddLicenses "tenant:ENTERPRISEPACK"
```

---

# 4. Managing Roles

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

# 5. Managing Groups

### Add user to group
```powershell
Add-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

### Group Types
| Group Type | Purpose |
|------------|---------|
| **Security Group** | Access control |
| **Microsoft 365 Group** | Teams, SharePoint, Planner |
| **Distribution List** | Email distribution |

---

# 6. Password Management

### Reset Password (Admin Center)
```
Users → Active Users → Reset Password
```

### PowerShell
```powershell
Set-MsolUserPassword -UserPrincipalName jsmith@domain.com `
-NewPassword "NewPass123!" -ForceChangePassword $true
```

---

# 7. Sign‑In Management

### Block Sign‑In
```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -BlockCredential $true
```

### Unblock Sign‑In
```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -BlockCredential $false
```

---

# 8. Restoring Deleted Users

### Admin Center
```
Users → Deleted Users → Restore
```

### PowerShell
```powershell
Restore-MsolUser -UserPrincipalName jsmith@domain.com
```

---

# 9. Deleting Users

### Admin Center
```
Users → Active Users → Delete User
```

### PowerShell
```powershell
Remove-MsolUser -UserPrincipalName jsmith@domain.com -Force
```

---

# 10. Troubleshooting

### User not syncing to Microsoft 365
- Check Azure AD Connect health  
- Ensure user is in a synced OU  
- Validate UPN suffix  
- Check sync errors in Synchronization Service Manager  

### User cannot log in
- Check license assignment  
- Check MFA configuration  
- Check sign‑in blocked status  
- Reset password  

### User missing apps
- Verify license SKU  
- Check service plan toggles  
- Check Conditional Access policies  

---

# 11. Verification Checklist

- User appears in Microsoft 365 Admin Center  
- License assigned  
- Mailbox created  
- MFA configured  
- Teams/SharePoint access working  
- No sync errors  
- No sign‑in errors  

---

# 12. Best Practices

- Standardize naming conventions  
- Automate onboarding/offboarding  
- Enforce MFA for all users  
- Use Conditional Access  
- Use security groups for access control  
- Avoid direct role assignments  
- Document user lifecycle processes  

---

# References

- Microsoft Learn — Microsoft 365 User Management  
- Microsoft Learn — Entra ID Administration  
- Microsoft Learn — PowerShell for Microsoft 365  

