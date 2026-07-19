
# Microsoft 365 Knowledge Base  
## 15 — Mailbox Archive and Retention

---

## Overview

Mailbox archiving and retention in Microsoft 365 ensure long‑term storage, compliance, and lifecycle management of email data. Archiving provides additional mailbox storage, while retention policies control how long data is kept, deleted, or preserved for legal or regulatory requirements.

This document covers:
- Archive mailboxes  
- Auto‑expanding archive  
- Retention policies  
- Retention tags  
- Litigation hold vs. retention  
- Troubleshooting archive and retention issues  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Archive Mailbox** | Secondary mailbox for long‑term storage |
| **Auto‑Expanding Archive** | Automatically increases archive size beyond 100 GB |
| **Retention Policy** | Defines how long items are kept or deleted |
| **Retention Tags** | Apply retention settings to folders or items |
| **Litigation Hold** | Preserves all mailbox data indefinitely |
| **In‑Place Hold** | Legacy hold mechanism (not recommended) |
| **MFA** | Required for admin access to compliance features |

---

## Tools Used

- Microsoft 365 Admin Center  
- Exchange Admin Center  
- Purview Compliance Portal  
- PowerShell (Exchange Online)  

---

# 1. Archive Mailboxes

## 1.1 What an Archive Mailbox Provides

- Additional mailbox storage  
- Automatic movement of older items  
- Supports retention policies  
- Reduces primary mailbox size  
- Required for auto‑expanding archive  

---

## 1.2 Enable Archive Mailbox

### Admin Center
```
Exchange Admin Center → Recipients → Mailboxes → Select User → Mailbox → Archive → Enable
```

### PowerShell
```powershell
Enable-Mailbox -Identity jsmith@domain.com -Archive
```

---

## 1.3 Disable Archive Mailbox

```powershell
Disable-Mailbox -Identity jsmith@domain.com -Archive
```

---

# 2. Auto‑Expanding Archive

Auto‑expanding archive increases archive mailbox size beyond 100 GB.

## 2.1 Enable Auto‑Expanding Archive

```powershell
Set-OrganizationConfig -AutoExpandingArchive
```

Auto-expansion occurs automatically when archive reaches capacity.

---

## 2.2 Check Archive Status

```powershell
Get-Mailbox -Identity jsmith@domain.com | Select ArchiveStatus,ArchiveQuota
```

---

# 3. Retention Policies

Retention policies determine how long mailbox items are kept and what happens when they reach the end of their lifecycle.

## 3.1 Retention Policy Types

| Policy Type | Description |
|-------------|-------------|
| **Delete Only** | Deletes items after a set period |
| **Archive Only** | Moves items to archive after a set period |
| **Archive + Delete** | Moves items to archive, then deletes later |
| **Preservation** | Keeps items indefinitely |

---

## 3.2 Create Retention Policy

### Purview Compliance Portal
```
Compliance → Data Lifecycle Management → Retention Policies → Create
```

Configure:
- Locations (Exchange, SharePoint, OneDrive, Teams)  
- Retention period  
- Action (retain, delete, retain then delete)  

---

## 3.3 Assign Retention Policy to Mailbox

### Admin Center
```
Exchange Admin Center → Mailboxes → Select User → Mailbox → Retention Policy
```

### PowerShell
```powershell
Set-Mailbox -Identity jsmith@domain.com -RetentionPolicy "Default MRM Policy"
```

---

# 4. Retention Tags

Retention tags define retention actions for specific folders or items.

## 4.1 Types of Retention Tags

| Tag Type | Description |
|----------|-------------|
| **Default Policy Tag (DPT)** | Applies to entire mailbox |
| **Retention Policy Tag (RPT)** | Applies to default folders (Inbox, Deleted Items) |
| **Personal Tag** | Users manually apply to items or folders |

---

## 4.2 Create Retention Tag

```powershell
New-RetentionPolicyTag -Name "Archive After 2 Years" `
-Type All `
-RetentionAction MoveToArchive `
-RetentionPeriod 730
```

---

## 4.3 Add Tag to Retention Policy

```powershell
Set-RetentionPolicy -Identity "Custom Policy" `
-RetentionPolicyTagLinks "Archive After 2 Years"
```

---

# 5. Litigation Hold vs. Retention

## 5.1 Litigation Hold

- Preserves all mailbox data indefinitely  
- Overrides deletion  
- Used for legal investigations  
- Stores data in Recoverable Items folder  

### Enable Litigation Hold
```powershell
Set-Mailbox -Identity jsmith@domain.com -LitigationHoldEnabled $true
```

---

## 5.2 Retention Policies

- Enforce lifecycle management  
- Can delete or preserve data  
- Used for compliance and governance  

---

## 5.3 When to Use Each

| Scenario | Use |
|----------|-----|
| Legal investigation | Litigation Hold |
| Regulatory compliance | Retention Policy |
| Email lifecycle management | Retention Policy |
| Prevent accidental deletion | Retention Policy |

---

# 6. Troubleshooting Archive & Retention Issues

## Issue 1 — Archive mailbox not appearing in Outlook

### Causes
- Archive not enabled  
- Outlook profile corruption  
- Cached mode issues  

### Fix
- Verify archive status  
- Create new Outlook profile  
- Use Outlook Web to confirm  

---

## Issue 2 — Items not moving to archive

### Causes
- Retention policy not applied  
- Retention tags missing  
- MFA/CA blocking policy sync  

### Fix
- Reapply retention policy  
- Check tag configuration  
- Wait 24 hours for policy sync  

---

## Issue 3 — Retention policy not applying

### Causes
- Policy not assigned  
- Mailbox not syncing  
- Incorrect tag type  

### Fix
- Assign policy  
- Run Managed Folder Assistant  
```powershell
Start-ManagedFolderAssistant -Identity jsmith@domain.com
```

---

## Issue 4 — Litigation Hold not working

### Causes
- Hold not enabled  
- Incorrect mailbox type  
- Compliance sync delay  

### Fix
- Verify hold status  
- Wait up to 24 hours  
- Check Recoverable Items folder  

---

# 7. Verification Checklist

- Archive mailbox enabled  
- Auto-expanding archive functioning  
- Retention policy applied  
- Retention tags working  
- Items moving to archive  
- Litigation Hold (if enabled) active  
- No policy errors in mailbox logs  

---

# 8. Best Practices

- Use auto‑expanding archive for large mailboxes  
- Apply retention policies consistently  
- Avoid overly complex retention structures  
- Document retention requirements  
- Use Litigation Hold only when legally required  
- Train users on archive mailbox usage  
- Review retention policies annually  

---

# References

- Microsoft Learn — Archive Mailboxes  
- Microsoft Learn — Retention Policies  
- Microsoft Learn — Litigation Hold  
- Microsoft Learn — Managed Folder Assistant  
