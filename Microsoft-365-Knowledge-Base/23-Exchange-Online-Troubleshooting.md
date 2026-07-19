
# Microsoft 365 Knowledge Base  
## 23 — Exchange Online Troubleshooting

---

## Overview

Exchange Online is the cloud-based email and calendaring service within Microsoft 365. When issues arise—such as mail delivery failures, Outlook connectivity problems, mailbox corruption, or permission errors—administrators must diagnose and resolve them quickly to maintain productivity.

This document covers:
- Connectivity issues  
- Mail flow problems  
- Mailbox access issues  
- Outlook client troubleshooting  
- Calendar issues  
- Mobile device issues  
- PowerShell diagnostics  
- Common error codes  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Autodiscover** | Automatically configures Outlook profiles |
| **EWS (Exchange Web Services)** | API used by Outlook and apps |
| **Transport Pipeline** | Mail flow path through Exchange Online |
| **Message Trace** | Tracks email delivery |
| **Mailbox Quota** | Storage limits for mailboxes |
| **MAPI/HTTP** | Outlook connectivity protocol |
| **Exchange Online PowerShell** | Administrative command interface |

---

## Tools Used

- Exchange Admin Center  
- Microsoft 365 Admin Center  
- Outlook Desktop / Outlook Web  
- PowerShell (Exchange Online)  
- Message Trace  
- Service Health Dashboard  

---

# 1. Connectivity Issues

## 1.1 Outlook Cannot Connect

### Symptoms
- “Disconnected” status  
- “Trying to connect…”  
- Cannot send/receive mail  

### Causes
- Autodiscover issues  
- Profile corruption  
- Network/firewall blocks  
- Conditional Access restrictions  

### Fix
1. Restart Outlook  
2. Check network connection  
3. Test Autodiscover  
```powershell
Test-OutlookConnectivity
```
4. Create new Outlook profile  
5. Review Conditional Access policies  

---

## 1.2 Autodiscover Not Working

### Causes
- Incorrect DNS  
- Cached Autodiscover XML  
- Hybrid misconfiguration  

### Fix
- Verify DNS:
```
autodiscover.domain.com → CNAME → autodiscover.outlook.com
```
- Delete Autodiscover cache:
```
%localappdata%\Microsoft\Outlook\16\RoamCache
```

---

## 1.3 Outlook Keeps Asking for Password

### Causes
- MFA not configured  
- Cached credentials  
- Modern auth disabled  

### Fix
- Clear credentials:
```
Control Panel → Credential Manager
```
- Enable modern auth  
- Re-register MFA  

---

# 2. Mail Flow Issues

## 2.1 Email Not Delivered

### Causes
- Incorrect MX record  
- Transport rule blocking  
- Spam filtering  
- Quarantine  

### Fix
- Run message trace  
- Check MX:
```
domain-com.mail.protection.outlook.com
```
- Review transport rules  
- Release from quarantine  

---

## 2.2 Email Stuck in Drafts/Outbox

### Causes
- Outlook offline  
- Large attachments  
- Corrupt profile  

### Fix
- Restart Outlook  
- Reduce attachment size  
- Create new profile  

---

## 2.3 External Email Blocked

### Causes
- Anti-spam policies  
- Connector issues  
- SPF/DKIM misconfiguration  

### Fix
- Review anti-spam policies  
- Validate connectors  
- Check SPF:
```
v=spf1 include:spf.protection.outlook.com -all
```

---

# 3. Mailbox Access Issues

## 3.1 User Cannot Access Mailbox

### Causes
- License removed  
- Mailbox not provisioned  
- Permission issues  

### Fix
- Assign license  
- Wait 5–30 minutes  
- Check mailbox permissions  

---

## 3.2 Shared Mailbox Not Appearing

### Causes
- Auto-mapping disabled  
- Outlook cache  
- Permission replication delay  

### Fix
- Add mailbox manually  
- Restart Outlook  
- Wait up to 2 hours  

---

## 3.3 “You do not have permission to send as this user”

### Causes
- Missing Send As permission  
- Cached credentials  

### Fix
- Assign Send As  
- Clear Outlook cache  

---

# 4. Calendar Issues

## 4.1 Meeting Invites Not Received

### Causes
- Inbox rules  
- Delegate settings  
- Mailbox corruption  

### Fix
- Check rules  
- Review delegate access  
- Run calendar repair:
```powershell
Start-CalendarRepair -Identity user@domain.com
```

---

## 4.2 Calendar Not Syncing

### Causes
- Mobile device sync issues  
- Outlook profile corruption  

### Fix
- Re-sync mobile device  
- Create new Outlook profile  

---

## 4.3 Duplicate Meetings

### Causes
- Multiple delegates  
- Conflicting rules  

### Fix
- Remove extra delegates  
- Review rules  

---

# 5. Mobile Device Issues

## 5.1 Mobile Email Not Syncing

### Causes
- Conditional Access  
- Device not compliant  
- Incorrect profile  

### Fix
- Check CA policies  
- Ensure device compliance  
- Re-add email account  

---

## 5.2 Mobile Device Blocked

### Causes
- Device not enrolled  
- Unsupported OS  

### Fix
- Enroll device in Intune  
- Update OS  

---

# 6. PowerShell Diagnostics

## 6.1 Check Mailbox Health

```powershell
Get-Mailbox -Identity user@domain.com
```

---

## 6.2 Check Mailbox Statistics

```powershell
Get-MailboxStatistics -Identity user@domain.com
```

---

## 6.3 Check Mailbox Quota

```powershell
Get-Mailbox -Identity user@domain.com | Select ProhibitSendQuota,ProhibitSendReceiveQuota
```

---

## 6.4 Check Transport Rules

```powershell
Get-TransportRule
```

---

## 6.5 Check Message Trace

```powershell
Get-MessageTrace -SenderAddress user@domain.com -StartDate (Get-Date).AddDays(-1)
```

---

# 7. Common Error Codes

| Error Code | Meaning | Fix |
|------------|---------|-----|
| **0x800CCC0E** | Cannot connect to server | Check network/firewall |
| **0x8004010F** | Outlook data file error | Create new profile |
| **550 5.7.1** | Permission denied | Check SPF/DKIM/transport rules |
| **554 5.4.14** | DNS lookup failure | Fix MX/SPF records |
| **AADSTS50076** | MFA required | Complete MFA setup |

---

# 8. Service Health

## 8.1 Check Microsoft 365 Service Health

```
Microsoft 365 Admin Center → Health → Service Health
```

Look for:
- Exchange Online outages  
- Degraded performance  
- Advisory notices  

---

# 9. Verification Checklist

- Outlook connected  
- Mail flow working  
- No sync errors  
- Calendar functioning  
- Mobile devices syncing  
- No permission issues  
- Message trace clean  
- Service health normal  

---

# 10. Best Practices

- Use message trace for all mail flow issues  
- Clear Outlook cache regularly  
- Avoid PST files  
- Use modern authentication  
- Document transport rules  
- Monitor service health  
- Use Conditional Access for Zero Trust  
- Train users on Outlook basics  

---

# References

- Microsoft Learn — Exchange Online  
- Microsoft Learn — Message Trace  
- Microsoft Learn — Outlook Connectivity  
- Microsoft Learn — Mail Flow Troubleshooting  
