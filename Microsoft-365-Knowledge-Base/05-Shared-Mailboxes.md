
# Microsoft 365 Knowledge Base  
## 05 — Shared Mailboxes

---

## Overview

Shared mailboxes in Microsoft 365 allow multiple users to read and send email from a common mailbox. They are commonly used for departments such as Support, HR, Finance, and Sales. Shared mailboxes do not require a license unless mailbox size exceeds 50 GB or if they need to be accessed via mobile devices.

This document covers:
- Shared mailbox creation  
- Permissions (Full Access, Send As, Send on Behalf)  
- Converting user mailboxes to shared  
- Accessing shared mailboxes  
- Troubleshooting common issues  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Shared Mailbox** | A mailbox accessed by multiple users |
| **Full Access** | Allows reading and managing mailbox content |
| **Send As** | Sends email as the shared mailbox |
| **Send on Behalf** | Sends email on behalf of the shared mailbox |
| **Licensing** | Required only for >50GB or mobile access |
| **Auto‑Mapping** | Automatically adds mailbox to Outlook |

---

## Tools Used

- Microsoft 365 Admin Center  
- Exchange Admin Center  
- PowerShell (Exchange Online)  
- Outlook Desktop / Outlook Web  

---

# 1. Creating a Shared Mailbox

## 1.1 Exchange Admin Center (Recommended)

```
Exchange Admin Center → Recipients → Shared → Add Shared Mailbox
```

Configure:
- Name  
- Email address  
- Members (Full Access + Send As)  

---

## 1.2 Microsoft 365 Admin Center

```
Teams & Groups → Active Teams & Groups → Add a group → Shared Mailbox
```

---

## 1.3 PowerShell (Exchange Online)

```powershell
New-Mailbox -Shared -Name "Support Team" -PrimarySmtpAddress support@domain.com
```

---

# 2. Assigning Permissions

## 2.1 Full Access

Allows users to open the mailbox and read/manage emails.

### PowerShell
```powershell
Add-MailboxPermission -Identity "support@domain.com" `
-User "jsmith@domain.com" -AccessRights FullAccess -InheritanceType All
```

---

## 2.2 Send As

Allows users to send email *as* the shared mailbox.

### PowerShell
```powershell
Add-RecipientPermission -Identity "support@domain.com" `
-Trustee "jsmith@domain.com" -AccessRights SendAs
```

---

## 2.3 Send on Behalf

Allows users to send email *on behalf of* the shared mailbox.

### PowerShell
```powershell
Set-Mailbox -Identity "support@domain.com" `
-GrantSendOnBehalfTo "jsmith@domain.com"
```

---

# 3. Accessing Shared Mailboxes

## 3.1 Outlook Desktop (Auto‑Mapping)

Users with **Full Access** automatically see the mailbox appear in Outlook.

If auto‑mapping is disabled, users can manually add:

```
File → Account Settings → More Settings → Advanced → Add Mailbox
```

---

## 3.2 Outlook Web (OWA)

```
Open another mailbox → support@domain.com
```

---

# 4. Converting Mailboxes

## 4.1 Convert User Mailbox → Shared

### Exchange Admin Center
```
Recipients → Mailboxes → Convert to Shared
```

### PowerShell
```powershell
Set-Mailbox -Identity jsmith@domain.com -Type Shared
```

---

## 4.2 Convert Shared Mailbox → User

```powershell
Set-Mailbox -Identity support@domain.com -Type Regular
```

---

# 5. Licensing Requirements

| Scenario | License Required? |
|----------|-------------------|
| Shared mailbox < 50GB | ❌ No |
| Shared mailbox > 50GB | ✔ Yes |
| Shared mailbox with archive | ✔ Yes |
| Shared mailbox accessed via mobile | ✔ Yes |

---

# 6. Troubleshooting Shared Mailboxes

## Issue 1 — Shared mailbox not appearing in Outlook

### Causes
- Auto‑mapping disabled  
- Permission replication delay  
- Outlook profile corruption  

### Fix
- Reassign Full Access  
- Add mailbox manually  
- Create new Outlook profile  

---

## Issue 2 — Cannot send as shared mailbox

### Causes
- Send As permission missing  
- Permission replication delay  
- Cached credentials  

### Fix
- Assign Send As  
- Wait up to 2 hours  
- Clear Outlook cache  

---

## Issue 3 — Shared mailbox not receiving mail

### Causes
- Incorrect mail flow rules  
- Transport restrictions  
- Mailbox disabled  

### Fix
- Check mail flow rules  
- Validate mailbox status  
- Review message trace  

---

## Issue 4 — Shared mailbox requires license unexpectedly

### Causes
- Mailbox > 50GB  
- Archive enabled  
- Mobile access required  

### Fix
- Assign appropriate license  
- Reduce mailbox size  
- Disable archive  

---

# 7. Verification Checklist

- Shared mailbox created  
- Permissions assigned correctly  
- Users can access mailbox  
- Send As / Send on Behalf working  
- Mail flow functioning  
- No permission errors  

---

# 8. Best Practices

- Use security groups for permission assignment  
- Avoid assigning permissions directly to users  
- Document shared mailbox ownership  
- Monitor mailbox size regularly  
- Use naming conventions (e.g., dept‑shared@domain.com)  
- Limit Send As permissions to trusted users  
- Review permissions quarterly  

---

# References

- Microsoft Learn — Shared Mailboxes  
- Microsoft Learn — Exchange Online Administration  
- Microsoft Learn — Mailbox Permissions  
