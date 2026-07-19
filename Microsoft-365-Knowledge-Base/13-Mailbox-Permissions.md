
# Microsoft 365 Knowledge Base  
## 13 — Mailbox Permissions

---

## Overview

Mailbox permissions in Microsoft 365 determine who can access, read, send, or manage another user’s mailbox. Proper permission configuration is essential for delegation, shared workflows, compliance, and secure collaboration.

This document covers:
- Permission types (Full Access, Send As, Send on Behalf)  
- Assigning and removing permissions  
- Calendar permissions  
- Shared mailbox permissions  
- Troubleshooting permission issues  
- Best practices  

---

## Key Concepts

| Permission Type | Description |
|-----------------|-------------|
| **Full Access** | Allows opening and managing mailbox content (read, delete, move) |
| **Send As** | Allows sending email *as* the mailbox |
| **Send on Behalf** | Allows sending email *on behalf of* the mailbox |
| **Calendar Permissions** | Controls access to calendar items |
| **Auto‑Mapping** | Automatically adds mailbox to Outlook when Full Access is granted |

---

## Tools Used

- Microsoft 365 Admin Center  
- Exchange Admin Center  
- PowerShell (Exchange Online)  
- Outlook Desktop / Outlook Web  

---

# 1. Mailbox Permission Types

## 1.1 Full Access

Allows:
- Open mailbox  
- Read emails  
- Delete emails  
- Move items  
- Create folders  

Does **not** allow sending email unless Send As or Send on Behalf is also assigned.

---

## 1.2 Send As

Allows sending email *as* the mailbox.

Example:
```
From: support@domain.com
```

---

## 1.3 Send on Behalf

Allows sending email *on behalf of* the mailbox.

Example:
```
From: John Smith on behalf of support@domain.com
```

---

# 2. Assigning Mailbox Permissions

## 2.1 Assign Full Access

### Exchange Admin Center
```
Recipients → Mailboxes → Select Mailbox → Mailbox Delegation → Full Access
```

### PowerShell
```powershell
Add-MailboxPermission -Identity "jsmith@domain.com" `
-User "adoe@domain.com" -AccessRights FullAccess -InheritanceType All
```

---

## 2.2 Assign Send As

### Exchange Admin Center
```
Recipients → Mailboxes → Mailbox Delegation → Send As
```

### PowerShell
```powershell
Add-RecipientPermission -Identity "jsmith@domain.com" `
-Trustee "adoe@domain.com" -AccessRights SendAs
```

---

## 2.3 Assign Send on Behalf

### Exchange Admin Center
```
Recipients → Mailboxes → Mailbox Delegation → Send on Behalf
```

### PowerShell
```powershell
Set-Mailbox -Identity "jsmith@domain.com" `
-GrantSendOnBehalfTo "adoe@domain.com"
```

---

# 3. Removing Mailbox Permissions

## 3.1 Remove Full Access

```powershell
Remove-MailboxPermission -Identity "jsmith@domain.com" `
-User "adoe@domain.com" -AccessRights FullAccess -Confirm:$false
```

---

## 3.2 Remove Send As

```powershell
Remove-RecipientPermission -Identity "jsmith@domain.com" `
-Trustee "adoe@domain.com" -AccessRights SendAs -Confirm:$false
```

---

## 3.3 Remove Send on Behalf

```powershell
Set-Mailbox -Identity "jsmith@domain.com" -GrantSendOnBehalfTo @{Remove="adoe@domain.com"}
```

---

# 4. Calendar Permissions

## 4.1 View Calendar Permissions

```powershell
Get-MailboxFolderPermission -Identity jsmith@domain.com:\Calendar
```

---

## 4.2 Assign Calendar Permissions

### Reviewer (read-only)
```powershell
Add-MailboxFolderPermission -Identity jsmith@domain.com:\Calendar `
-User adoe@domain.com -AccessRights Reviewer
```

### Editor (full modify)
```powershell
Add-MailboxFolderPermission -Identity jsmith@domain.com:\Calendar `
-User adoe@domain.com -AccessRights Editor
```

---

## 4.3 Remove Calendar Permissions

```powershell
Remove-MailboxFolderPermission -Identity jsmith@domain.com:\Calendar `
-User adoe@domain.com -Confirm:$false
```

---

# 5. Auto‑Mapping

Auto‑mapping automatically adds the mailbox to Outlook when Full Access is granted.

## 5.1 Disable Auto‑Mapping

```powershell
Add-MailboxPermission -Identity "jsmith@domain.com" `
-User "adoe@domain.com" -AccessRights FullAccess -AutoMapping:$false
```

---

# 6. Shared Mailbox Permissions

Shared mailboxes use the same permission model.

### Assign Full Access
```powershell
Add-MailboxPermission -Identity "support@domain.com" `
-User "adoe@domain.com" -AccessRights FullAccess
```

### Assign Send As
```powershell
Add-RecipientPermission -Identity "support@domain.com" `
-Trustee "adoe@domain.com" -AccessRights SendAs
```

---

# 7. Troubleshooting Mailbox Permission Issues

## Issue 1 — User cannot see mailbox in Outlook

### Causes
- Auto‑mapping disabled  
- Permission replication delay  
- Outlook profile corruption  

### Fix
- Add mailbox manually  
- Wait up to 2 hours  
- Create new Outlook profile  

---

## Issue 2 — Send As not working

### Causes
- Permission not replicated  
- Cached credentials  
- Incorrect permission type  

### Fix
- Wait 2–4 hours  
- Clear Outlook cache  
- Verify Send As vs Send on Behalf  

---

## Issue 3 — Calendar permissions not applying

### Causes
- Incorrect folder path  
- Legacy permissions  
- Outlook cache  

### Fix
- Use PowerShell to verify  
- Reset calendar view  
- Clear Outlook cache  

---

## Issue 4 — User sees “You do not have permission to send on behalf”

### Causes
- Missing Send on Behalf  
- Incorrect Outlook From address  

### Fix
- Assign Send on Behalf  
- Re-select correct From address  

---

# 8. Verification Checklist

- User can open mailbox  
- User can send as/on behalf (if assigned)  
- Calendar permissions working  
- No permission errors  
- Outlook profile updated  
- Permissions visible in EAC  

---

# 9. Best Practices

- Use security groups for permission assignment  
- Avoid assigning permissions directly to users  
- Document mailbox ownership  
- Review permissions quarterly  
- Limit Send As permissions to trusted staff  
- Use PowerShell for consistent permission management  
- Avoid mixing Full Access with excessive Send As permissions  

---

# References

- Microsoft Learn — Mailbox Permissions  
- Microsoft Learn — Exchange Online Administration  
- Microsoft Learn — Calendar Permissions  
