
# Microsoft 365 Knowledge Base  
## 21 — User Offboarding

---

## Overview

User offboarding is the structured process of removing access, securing data, and preparing accounts for archival or deletion when an employee leaves the organization. A consistent offboarding workflow protects corporate data, prevents unauthorized access, and ensures business continuity.

This document covers:
- Pre‑offboarding requirements  
- Account disabling  
- License removal  
- Data retention  
- Mailbox management  
- Device actions  
- Security actions  
- Offboarding checklist  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Account Disable** | Prevents sign-in while preserving data |
| **Mailbox Retention** | Keeps mailbox accessible for legal or business needs |
| **Shared Access** | Delegation or forwarding for business continuity |
| **Data Export** | Exporting OneDrive, mailbox, or Teams data |
| **Device Wipe** | Removing corporate data from enrolled devices |
| **Offboarding Checklist** | Ensures consistent and secure offboarding |

---

## Tools Used

- Microsoft 365 Admin Center  
- Microsoft Entra Admin Center  
- Exchange Admin Center  
- Intune Admin Center  
- SharePoint Admin Center  
- PowerShell (Graph, AzureAD, MSOnline, Exchange Online)  

---

# 1. Pre‑Offboarding Requirements

## 1.1 Information Needed

- Employee name  
- Termination date  
- Manager name  
- Required data retention period  
- Access transfer requirements  
- Device return status  
- Legal or HR hold requirements  

---

## 1.2 Create Offboarding Ticket

Include:
- Disable date/time  
- Access transfer instructions  
- Data retention instructions  
- Device wipe instructions  

---

# 2. Disable User Account

## 2.1 Microsoft Entra Admin Center

```
Identity → Users → Select User → Block Sign-In
```

---

## 2.2 PowerShell

```powershell
Update-MgUser -UserId user@domain.com -AccountEnabled $false
```

---

# 3. Remove Licenses

## 3.1 Microsoft 365 Admin Center

```
Users → Active Users → Select User → Licenses and Apps → Remove Licenses
```

---

## 3.2 PowerShell

```powershell
Set-MgUserLicense -UserId user@domain.com -RemoveLicenses @{SkuId="<LicenseGUID>"}
```

---

# 4. Mailbox Management

## 4.1 Convert Mailbox to Shared Mailbox

Shared mailboxes do not require a license.

### Admin Center
```
Exchange Admin Center → Mailboxes → Convert to Shared
```

### PowerShell
```powershell
Set-Mailbox -Identity user@domain.com -Type Shared
```

---

## 4.2 Assign Access to Manager

```powershell
Add-MailboxPermission -Identity user@domain.com `
-User manager@domain.com -AccessRights FullAccess
```

### Optional: Send As
```powershell
Add-RecipientPermission -Identity user@domain.com `
-Trustee manager@domain.com -AccessRights SendAs
```

---

## 4.3 Set Automatic Forwarding (Optional)

```powershell
Set-Mailbox -Identity user@domain.com -ForwardingSMTPAddress manager@domain.com
```

---

## 4.4 Configure Mailbox Retention

Mailbox is retained for **30 days** after deletion unless converted to shared.

---

# 5. OneDrive & SharePoint Data

## 5.1 Transfer OneDrive Ownership

```
SharePoint Admin Center → User Profiles → Manage User Profiles → OneDrive → Transfer Files
```

---

## 5.2 SharePoint Access Review

Remove user from:
- Sites  
- Groups  
- Teams  
- Shared libraries  

---

# 6. Teams Offboarding

## 6.1 Remove User from Teams

Teams membership is removed automatically when account is disabled.

---

## 6.2 Transfer Ownership

Ensure user is not the sole owner of:
- Teams  
- Channels  
- SharePoint sites  
- Planner plans  

---

# 7. Device Offboarding (Intune)

## 7.1 Remove Device from Intune

```
Intune Admin Center → Devices → Select Device → Retire / Wipe
```

### Retire
Removes corporate data.

### Wipe
Factory reset (corporate-owned devices).

---

## 7.2 Remove Company Portal Access

User loses access automatically when account is disabled.

---

# 8. Security Actions

## 8.1 Revoke Sessions

```powershell
Invoke-MgInvalidateUserRefreshToken -UserId user@domain.com
```

---

## 8.2 Remove MFA Methods

```
Entra Admin Center → Users → Authentication Methods → Remove
```

---

## 8.3 Remove Conditional Access Assignments

Ensure user is removed from:
- CA policies  
- Security groups  
- Role assignments  

---

## 8.4 Remove Admin Roles

```powershell
Remove-MgRoleManagementDirectoryRoleAssignment -RoleAssignmentId <Id>
```

---

# 9. Optional: Legal Hold / Retention

## 9.1 Litigation Hold

```powershell
Set-Mailbox -Identity user@domain.com -LitigationHoldEnabled $true
```

---

## 9.2 Retention Policies

Mailbox and OneDrive can be retained via Purview retention policies.

---

# 10. Delete User Account (Final Step)

Only after:
- Data transferred  
- Mailbox converted  
- Legal requirements met  

### Admin Center
```
Users → Active Users → Delete User
```

### PowerShell
```powershell
Remove-MgUser -UserId user@domain.com
```

---

# 11. Offboarding Checklist

| Task | Completed |
|------|-----------|
| Block sign-in | ✔ |
| Remove licenses | ✔ |
| Convert mailbox to shared | ✔ |
| Assign mailbox access | ✔ |
| Transfer OneDrive data | ✔ |
| Remove Teams ownership | ✔ |
| Retire/wipe devices | ✔ |
| Revoke sessions | ✔ |
| Remove MFA methods | ✔ |
| Remove admin roles | ✔ |
| Delete account (optional) | ✔ |

---

# 12. Troubleshooting

## Issue 1 — Manager cannot access mailbox

### Causes
- Permission not applied  
- Outlook cache  

### Fix
- Reapply Full Access  
- Restart Outlook  

---

## Issue 2 — OneDrive transfer not working

### Causes
- User not deleted  
- Incorrect admin permissions  

### Fix
- Delete user first  
- Use SharePoint admin role  

---

## Issue 3 — Device not wiping

### Causes
- Device offline  
- Policy conflict  

### Fix
- Wait until device connects  
- Retry wipe command  

---

## Issue 4 — Emails still going to user

### Causes
- Forwarding not set  
- Mailbox not converted  

### Fix
- Set forwarding  
- Convert mailbox to shared  

---

# 13. Best Practices

- Disable account immediately upon termination  
- Convert mailbox to shared before deletion  
- Transfer OneDrive data before deleting user  
- Use retention policies for compliance  
- Remove admin roles first  
- Document offboarding steps  
- Review access quarterly  
- Automate offboarding with PowerShell or workflows  

---

# References

- Microsoft Learn — User Management  
- Microsoft Learn — Exchange Online  
- Microsoft Learn — Intune Device Management  
- Microsoft Learn — Microsoft Purview  
