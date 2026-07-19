
# Microsoft 365 Knowledge Base  
## 25 — PowerShell for Microsoft 365

---

## Overview

PowerShell is an essential tool for Microsoft 365 administrators, enabling automation, bulk operations, advanced configuration, and troubleshooting across Exchange Online, Microsoft Entra ID, SharePoint Online, Teams, and Intune. Modern Microsoft 365 administration increasingly relies on the **Microsoft Graph PowerShell SDK**, replacing legacy modules.

This document covers:
- Required PowerShell modules  
- Authentication  
- User and group management  
- Exchange Online commands  
- SharePoint Online commands  
- Teams administration  
- Intune device management  
- Reporting and automation  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Microsoft Graph PowerShell** | Unified module for Microsoft 365 administration |
| **Exchange Online PowerShell** | Mailbox, mail flow, and transport management |
| **Entra ID PowerShell** | Identity and access management |
| **Automation** | Scripts for bulk operations and scheduled tasks |
| **Remote PowerShell** | Cloud-based administrative sessions |
| **App Registration** | Service principal authentication for automation |

---

## Tools Used

- PowerShell 7+  
- Microsoft Graph PowerShell SDK  
- Exchange Online PowerShell V3  
- SharePoint Online Management Shell  
- Teams PowerShell Module  
- Intune PowerShell (Graph)  
- Task Scheduler / Azure Automation  

---

# 1. Required PowerShell Modules

## 1.1 Install Microsoft Graph PowerShell SDK

```powershell
Install-Module Microsoft.Graph -Scope AllUsers
```

---

## 1.2 Install Exchange Online Module

```powershell
Install-Module ExchangeOnlineManagement
```

---

## 1.3 Install SharePoint Online Module

```powershell
Install-Module Microsoft.Online.SharePoint.PowerShell
```

---

## 1.4 Install Teams Module

```powershell
Install-Module MicrosoftTeams
```

---

## 1.5 Install MSOnline (Legacy)

```powershell
Install-Module MSOnline
```

---

# 2. Authentication

## 2.1 Connect to Microsoft Graph

```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All","Group.ReadWrite.All"
```

---

## 2.2 Connect to Exchange Online

```powershell
Connect-ExchangeOnline
```

---

## 2.3 Connect to SharePoint Online

```powershell
Connect-SPOService -Url https://domain-admin.sharepoint.com
```

---

## 2.4 Connect to Teams

```powershell
Connect-MicrosoftTeams
```

---

# 3. User Management (Microsoft Graph)

## 3.1 Create User

```powershell
New-MgUser -DisplayName "John Smith" `
-UserPrincipalName "john.smith@domain.com" `
-AccountEnabled $true `
-PasswordProfile @{Password="TempPass123!"}
```

---

## 3.2 Update User

```powershell
Update-MgUser -UserId john.smith@domain.com -Department "Finance"
```

---

## 3.3 Delete User

```powershell
Remove-MgUser -UserId john.smith@domain.com
```

---

## 3.4 Restore Deleted User

```powershell
Restore-MgDirectoryDeletedItem -DirectoryObjectId <ObjectId>
```

---

# 4. Group Management

## 4.1 Create Security Group

```powershell
New-MgGroup -DisplayName "Finance Team" `
-SecurityEnabled $true -MailEnabled $false
```

---

## 4.2 Add Member to Group

```powershell
Add-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

---

## 4.3 Create Dynamic Group

```powershell
New-MgGroup -DisplayName "Sales Users" `
-GroupTypes "DynamicMembership" `
-MembershipRule "(user.department -eq ""Sales"")" `
-MembershipRuleProcessingState "On"
```

---

# 5. Exchange Online Administration

## 5.1 View Mailbox

```powershell
Get-Mailbox -Identity user@domain.com
```

---

## 5.2 Assign Mailbox Permissions

### Full Access
```powershell
Add-MailboxPermission -Identity user@domain.com `
-User manager@domain.com -AccessRights FullAccess
```

### Send As
```powershell
Add-RecipientPermission -Identity user@domain.com `
-Trustee manager@domain.com -AccessRights SendAs
```

---

## 5.3 Message Trace

```powershell
Get-MessageTrace -SenderAddress user@domain.com -StartDate (Get-Date).AddDays(-1)
```

---

## 5.4 Create Transport Rule

```powershell
New-TransportRule -Name "Block External to HR" `
-FromScope NotInOrganization -SentTo "hr@domain.com" -RejectMessageReasonText "External mail blocked"
```

---

# 6. SharePoint Online Administration

## 6.1 List Sites

```powershell
Get-SPOSite
```

---

## 6.2 Add Site Admin

```powershell
Set-SPOUser -Site https://domain.sharepoint.com/sites/Finance `
-LoginName manager@domain.com -IsSiteCollectionAdmin $true
```

---

## 6.3 Create SharePoint Site

```powershell
New-SPOSite -Url https://domain.sharepoint.com/sites/Projects `
-Owner admin@domain.com -StorageQuota 2048
```

---

# 7. Teams Administration

## 7.1 List Teams

```powershell
Get-Team
```

---

## 7.2 Add User to Team

```powershell
Add-TeamUser -GroupId <GroupId> -User user@domain.com
```

---

## 7.3 Create Team

```powershell
New-Team -DisplayName "Project Team" -Visibility Private
```

---

# 8. Intune Device Management (Graph)

## 8.1 List Devices

```powershell
Get-MgDevice
```

---

## 8.2 Disable Device

```powershell
Update-MgDevice -DeviceId <DeviceId> -AccountEnabled $false
```

---

## 8.3 Wipe Device

```powershell
Invoke-MgDeviceManagementManagedDeviceWipe -ManagedDeviceId <DeviceId>
```

---

# 9. Reporting & Automation

## 9.1 Export User List

```powershell
Get-MgUser | Select DisplayName,UserPrincipalName | Export-Csv users.csv -NoTypeInformation
```

---

## 9.2 Export License Report

```powershell
Get-MgUserLicenseDetail | Export-Csv licenses.csv -NoTypeInformation
```

---

## 9.3 Schedule Scripts

Use:
- Windows Task Scheduler  
- Azure Automation  
- GitHub Actions (optional)  

---

# 10. Troubleshooting

## Issue 1 — Cannot connect to Exchange Online

### Causes
- MFA required  
- Legacy auth disabled  

### Fix
- Use `Connect-ExchangeOnline`  
- Ensure modern auth enabled  

---

## Issue 2 — Graph commands failing

### Causes
- Missing scopes  
- Token expired  

### Fix
```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All"
```

---

## Issue 3 — Teams module errors

### Causes
- Outdated module  
- Network restrictions  

### Fix
```powershell
Update-Module MicrosoftTeams
```

---

## Issue 4 — SharePoint commands failing

### Causes
- Incorrect admin URL  
- Missing permissions  

### Fix
- Use correct admin URL  
- Assign SharePoint admin role  

---

# 11. Verification Checklist

- Modules installed  
- Connected to services  
- Commands executing successfully  
- Reports generated  
- Automation configured  
- No authentication errors  

---

# 12. Best Practices

- Use Microsoft Graph instead of legacy modules  
- Store credentials securely (Azure Key Vault)  
- Use least privilege access  
- Document scripts and workflows  
- Test scripts in non-production environment  
- Automate repetitive tasks  
- Regularly update modules  
- Use error handling in scripts  

---

# References

- Microsoft Learn — Microsoft Graph PowerShell  
- Microsoft Learn — Exchange Online PowerShell  
- Microsoft Learn — SharePoint Online PowerShell  
- Microsoft Learn — Teams PowerShell  
- Microsoft Learn — Intune PowerShell  
