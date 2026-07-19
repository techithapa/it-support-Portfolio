
# Microsoft 365 Knowledge Base  
## 14 — Mailbox Delegation

---

## Overview

Mailbox delegation allows users to manage another user’s mailbox, calendar, or send messages on their behalf. Delegation is commonly used for executives, assistants, shared workflows, and team-based communication.

This document covers:
- Delegation types  
- Outlook delegation  
- Exchange Online delegation  
- Calendar delegation  
- Delegate access levels  
- Troubleshooting delegation issues  
- Best practices  

---

## Key Concepts

| Delegation Type | Description |
|------------------|-------------|
| **Full Access** | Open and manage mailbox content |
| **Send As** | Send email *as* the mailbox |
| **Send on Behalf** | Send email *on behalf of* the mailbox |
| **Calendar Delegation** | Manage meetings, scheduling, and invitations |
| **Delegate Access (Outlook)** | Client-side delegation for mailbox and calendar |

---

## Tools Used

- Outlook Desktop  
- Outlook Web (OWA)  
- Exchange Admin Center  
- Microsoft 365 Admin Center  
- PowerShell (Exchange Online)  

---

# 1. Delegation Types Explained

## 1.1 Full Access

Allows delegate to:
- Open mailbox  
- Read emails  
- Move/delete items  
- Create folders  

Does **not** allow sending unless Send As or Send on Behalf is assigned.

---

## 1.2 Send As

Delegate sends email *as* the mailbox.

Example:
```
From: ceo@domain.com
```

---

## 1.3 Send on Behalf

Delegate sends email *on behalf of* the mailbox.

Example:
```
From: John Smith on behalf of ceo@domain.com
```

---

## 1.4 Calendar Delegation

Allows delegate to:
- Schedule meetings  
- Accept/decline invitations  
- Manage calendar items  

---

## 1.5 Outlook Delegate Access (Client-Side)

Outlook-specific delegation that:
- Adds mailbox automatically  
- Grants calendar permissions  
- Allows meeting management  

---

# 2. Configuring Delegation in Outlook

## 2.1 Outlook Desktop

```
File → Account Settings → Delegate Access → Add
```

Delegate permission levels:
- Reviewer  
- Author  
- Editor  

Options:
- Receive meeting requests  
- Manage private items (optional)  

---

## 2.2 Outlook Web (OWA)

### Calendar Delegation
```
Calendar → Share → Enter delegate → Select permissions
```

---

# 3. Configuring Delegation in Exchange Online

## 3.1 Assign Full Access

```powershell
Add-MailboxPermission -Identity "ceo@domain.com" `
-User "assistant@domain.com" -AccessRights FullAccess -InheritanceType All
```

---

## 3.2 Assign Send As

```powershell
Add-RecipientPermission -Identity "ceo@domain.com" `
-Trustee "assistant@domain.com" -AccessRights SendAs
```

---

## 3.3 Assign Send on Behalf

```powershell
Set-Mailbox -Identity "ceo@domain.com" `
-GrantSendOnBehalfTo "assistant@domain.com"
```

---

## 3.4 Remove Delegation

### Remove Full Access
```powershell
Remove-MailboxPermission -Identity "ceo@domain.com" `
-User "assistant@domain.com" -AccessRights FullAccess -Confirm:$false
```

### Remove Send As
```powershell
Remove-RecipientPermission -Identity "ceo@domain.com" `
-Trustee "assistant@domain.com" -AccessRights SendAs -Confirm:$false
```

### Remove Send on Behalf
```powershell
Set-Mailbox -Identity "ceo@domain.com" -GrantSendOnBehalfTo @{Remove="assistant@domain.com"}
```

---

# 4. Calendar Delegation (PowerShell)

## 4.1 View Calendar Permissions

```powershell
Get-MailboxFolderPermission -Identity ceo@domain.com:\Calendar
```

---

## 4.2 Assign Calendar Permissions

### Editor
```powershell
Add-MailboxFolderPermission -Identity ceo@domain.com:\Calendar `
-User assistant@domain.com -AccessRights Editor
```

### Reviewer
```powershell
Add-MailboxFolderPermission -Identity ceo@domain.com:\Calendar `
-User assistant@domain.com -AccessRights Reviewer
```

---

## 4.3 Remove Calendar Permissions

```powershell
Remove-MailboxFolderPermission -Identity ceo@domain.com:\Calendar `
-User assistant@domain.com -Confirm:$false
```

---

# 5. Auto‑Mapping in Delegation

Auto‑mapping automatically adds delegated mailboxes to Outlook.

## 5.1 Disable Auto‑Mapping

```powershell
Add-MailboxPermission -Identity "ceo@domain.com" `
-User "assistant@domain.com" -AccessRights FullAccess -AutoMapping:$false
```

---

# 6. Common Delegation Scenarios

## Scenario 1 — Executive Assistant Access

Recommended:
- Full Access  
- Send As  
- Calendar Editor  

---

## Scenario 2 — Team Shared Mailbox

Recommended:
- Full Access  
- Send As  

---

## Scenario 3 — Calendar Management Only

Recommended:
- Calendar Editor  
- Optional: Send on Behalf  

---

# 7. Troubleshooting Delegation Issues

## Issue 1 — Delegate cannot send as mailbox

### Causes
- Send As not assigned  
- Permission replication delay  
- Cached credentials  

### Fix
- Assign Send As  
- Wait 2–4 hours  
- Clear Outlook cache  

---

## Issue 2 — Delegate cannot see mailbox

### Causes
- Auto‑mapping disabled  
- Outlook profile corruption  

### Fix
- Add mailbox manually  
- Create new Outlook profile  

---

## Issue 3 — Calendar invites not received by delegate

### Causes
- Incorrect delegate settings  
- Outlook rules interfering  

### Fix
```
Outlook → File → Account Settings → Delegate Access → Ensure “Receive meeting requests” is enabled
```

---

## Issue 4 — Delegate sees “You do not have permission to send on behalf”

### Causes
- Missing Send on Behalf  
- Incorrect From address  

### Fix
- Assign Send on Behalf  
- Re-select correct From address  

---

## Issue 5 — Duplicate meeting requests

### Causes
- Multiple delegates  
- Conflicting rules  

### Fix
- Review delegate list  
- Remove unnecessary delegates  

---

# 8. Verification Checklist

- Delegate can open mailbox  
- Delegate can send as/on behalf  
- Calendar permissions working  
- Meeting requests routed correctly  
- No permission errors  
- Outlook profile updated  

---

# 9. Best Practices

- Use security groups for delegation  
- Limit Send As permissions  
- Document delegation assignments  
- Review delegation quarterly  
- Avoid mixing Outlook delegate access with PowerShell delegation  
- Train users on correct “From” address usage  
- Use Send As for full impersonation, Send on Behalf for transparency  

---

# References

- Microsoft Learn — Mailbox Delegation  
- Microsoft Learn — Calendar Delegation  
- Microsoft Learn — Exchange Online Administration  
