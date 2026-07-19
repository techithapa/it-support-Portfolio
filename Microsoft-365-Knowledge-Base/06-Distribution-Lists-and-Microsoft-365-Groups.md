
# Microsoft 365 Knowledge Base  
## 06 — Distribution Lists and Microsoft 365 Groups

---

## Overview

Distribution Lists (DLs) and Microsoft 365 Groups are essential components of Microsoft 365 collaboration and mail flow. Although both can deliver email to multiple recipients, Microsoft 365 Groups provide additional collaboration features such as shared mailboxes, SharePoint sites, Teams integration, and Planner.

This document covers:
- Differences between DLs and M365 Groups  
- Creating and managing DLs  
- Creating and managing M365 Groups  
- Converting DLs to M365 Groups  
- Mail flow considerations  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Distribution List (DL)** | Email-only group used for message distribution |
| **Microsoft 365 Group** | Collaboration group with shared mailbox, SharePoint, Teams, Planner |
| **Security Group** | Used for access control (not mail-enabled unless configured) |
| **Mail-enabled Security Group** | Security group that can also receive email |
| **Group Owners** | Manage membership and settings |
| **Dynamic Groups** | Membership based on user attributes |

---

## Tools Used

- Microsoft 365 Admin Center  
- Exchange Admin Center  
- Entra Admin Center  
- PowerShell (Exchange Online, Graph)  

---

# 1. Distribution Lists (DLs)

## 1.1 What DLs Are Used For

- Departmental email (e.g., hr@domain.com)  
- Announcements  
- Internal communication  
- Simple email routing  

DLs **do not** include:
- Shared mailbox  
- SharePoint site  
- Teams integration  
- Planner  

---

## 1.2 Creating a Distribution List

### Exchange Admin Center
```
Recipients → Groups → Add Group → Distribution
```

### Microsoft 365 Admin Center
```
Teams & Groups → Active Teams & Groups → Add a group → Distribution
```

### PowerShell
```powershell
New-DistributionGroup -Name "HR Team" -PrimarySmtpAddress hr@domain.com
```

---

## 1.3 Managing DL Membership

### Add member
```powershell
Add-DistributionGroupMember -Identity "HR Team" -Member jsmith@domain.com
```

### Remove member
```powershell
Remove-DistributionGroupMember -Identity "HR Team" -Member jsmith@domain.com -Confirm:$false
```

---

## 1.4 Mail-Enabled Security Groups

Used when:
- You need email distribution  
- AND access control (SharePoint, Teams, apps)

### Create
```powershell
New-DistributionGroup -Name "Finance Access" -Type Security
```

---

# 2. Microsoft 365 Groups

## 2.1 What M365 Groups Provide

| Feature | DL | M365 Group |
|---------|----|------------|
| Email distribution | ✔ | ✔ |
| Shared mailbox | ❌ | ✔ |
| SharePoint site | ❌ | ✔ |
| Teams integration | ❌ | ✔ |
| Planner | ❌ | ✔ |
| Calendar | ❌ | ✔ |
| Guest access | ❌ | ✔ |

---

## 2.2 Creating a Microsoft 365 Group

### Microsoft 365 Admin Center
```
Teams & Groups → Active Teams & Groups → Add a group → Microsoft 365
```

### Exchange Admin Center
```
Recipients → Groups → Add Group → Microsoft 365
```

### PowerShell (Graph)
```powershell
New-MgGroup -DisplayName "Marketing Team" `
-GroupTypes "Unified" `
-MailEnabled $true `
-MailNickname "marketing" `
-SecurityEnabled $false
```

---

## 2.3 Managing M365 Group Membership

### Add member
```powershell
Add-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

### Remove member
```powershell
Remove-MgGroupMember -GroupId <GroupId> -DirectoryObjectId <UserId>
```

---

## 2.4 Group Owners

Owners can:
- Add/remove members  
- Manage group settings  
- Approve guest access  

### Add owner
```powershell
Add-MgGroupOwner -GroupId <GroupId> -DirectoryObjectId <UserId>
```

---

# 3. Converting Distribution Lists to Microsoft 365 Groups

### Requirements
- DL must be cloud-managed  
- DL must not be dynamic  
- DL must not be mail-enabled security group  
- DL must have fewer than 100 members (recommended)

### PowerShell Conversion
```powershell
Set-DistributionGroup -Identity "HR Team" -UnifiedGroup $true
```

---

# 4. Mail Flow Considerations

## DL Mail Flow
- Simple routing  
- No shared mailbox  
- No moderation unless configured  

## M365 Group Mail Flow
- Messages stored in group mailbox  
- Members receive copies (optional)  
- Supports external senders (if enabled)  

### Enable external senders
```powershell
Set-UnifiedGroup -Identity "Marketing Team" -AllowExternalSenders $true
```

---

# 5. Troubleshooting

## Issue 1 — DL not receiving mail

### Causes
- Incorrect mail flow rules  
- DL hidden from GAL  
- DL moderation enabled  

### Fix
- Check message trace  
- Validate DL visibility  
- Review moderation settings  

---

## Issue 2 — M365 Group not showing in Outlook

### Causes
- Group not mail-enabled  
- Outlook cache  
- User not a member  

### Fix
- Verify group type  
- Clear Outlook cache  
- Add user to group  

---

## Issue 3 — Cannot convert DL to M365 Group

### Causes
- DL is on-premises  
- DL is mail-enabled security group  
- DL has unsupported configuration  

### Fix
- Move DL to cloud  
- Create new M365 Group  
- Recreate membership  

---

## Issue 4 — External users cannot email M365 Group

### Causes
- External senders disabled  
- Transport rule blocking  
- Anti-spam policy restrictions  

### Fix
- Enable external senders  
- Review mail flow rules  
- Check anti-spam policies  

---

# 6. Verification Checklist

- DL or M365 Group created  
- Members added correctly  
- Mail flow working  
- External senders (if required) enabled  
- Group visible in Outlook  
- Permissions correct  

---

# 7. Best Practices

- Use M365 Groups for collaboration  
- Use DLs for simple email distribution  
- Avoid mail-enabled security groups unless necessary  
- Use naming conventions (e.g., dept‑team@domain.com)  
- Limit group owners to responsible staff  
- Review group membership quarterly  
- Document group purpose and ownership  

---

# References

- Microsoft Learn — Distribution Groups  
- Microsoft Learn — Microsoft 365 Groups  
- Microsoft Learn — Exchange Online Administration  
