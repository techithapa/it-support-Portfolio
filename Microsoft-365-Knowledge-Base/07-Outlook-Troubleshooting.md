
# Microsoft 365 Knowledge Base  
## 07 — Outlook Troubleshooting

---

## Overview

Outlook is one of the most widely used applications in Microsoft 365 for email, calendar, and contacts. Issues with Outlook can disrupt productivity, communication, and business operations. This document provides a comprehensive troubleshooting guide for Outlook Desktop, Outlook Web (OWA), and Outlook Mobile.

This guide covers:
- Common Outlook issues  
- Connectivity problems  
- Profile corruption  
- Search issues  
- Add‑in problems  
- Mail flow troubleshooting  
- OST/PST issues  
- Performance problems  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Autodiscover** | Service that configures Outlook automatically |
| **MAPI/HTTP** | Modern Outlook connection protocol |
| **Exchange Web Services (EWS)** | API used by Outlook for mailbox access |
| **OST File** | Cached mailbox file for Outlook Desktop |
| **Profile** | Outlook configuration stored locally |
| **Cached Mode** | Local copy of mailbox for performance |
| **Online Mode** | Direct connection to Exchange Online |

---

## Tools Used

- Outlook Desktop  
- Outlook Web (OWA)  
- Outlook Mobile  
- Microsoft 365 Admin Center  
- Exchange Admin Center  
- PowerShell (Exchange Online)  
- TestConnectivity tools  
- Windows Event Viewer  

---

# 1. Common Outlook Issues

| Issue | Symptoms |
|-------|----------|
| Connectivity problems | “Disconnected”, “Trying to connect…” |
| Profile corruption | Crashes, freezes, missing folders |
| Search not working | No results, incomplete results |
| Slow performance | Lag, freezing, slow send/receive |
| OST corruption | Sync issues, missing emails |
| Add‑in conflicts | Crashes, slow startup |
| Calendar sync issues | Missing meetings, duplicate entries |
| Authentication issues | MFA prompts failing, password loops |

---

# 2. Connectivity Troubleshooting

## 2.1 Check Connection Status

Outlook → **Ctrl + Right‑click** Outlook icon → **Connection Status**

Look for:
- Status = Established  
- Protocol = MAPI/HTTP  
- Errors or failures  

---

## 2.2 Test Autodiscover

### Outlook Web
If OWA works, issue is local to Outlook Desktop.

### PowerShell
```powershell
Test-OutlookConnectivity
```

### Web Test
```
https://testconnectivity.microsoft.com
```

---

## 2.3 Reset Autodiscover

```powershell
ipconfig /flushdns
```

Remove old registry entries (if needed):
```
HKEY_CURRENT_USER\Software\Microsoft\Office\<version>\Outlook\AutoDiscover
```

---

# 3. Profile Troubleshooting

## 3.1 Create a New Outlook Profile

```
Control Panel → Mail → Show Profiles → Add
```

Set new profile as default.

---

## 3.2 Remove Old Profiles

Delete unused or corrupted profiles.

---

## 3.3 Recreate OST File

Close Outlook → Delete OST file:

```
%localappdata%\Microsoft\Outlook
```

Outlook will rebuild the file.

---

# 4. Search Troubleshooting

## 4.1 Rebuild Windows Search Index

```
Control Panel → Indexing Options → Advanced → Rebuild
```

---

## 4.2 Ensure Cached Mode is Enabled

```
File → Account Settings → Account Settings → Change → Use Cached Exchange Mode
```

---

## 4.3 Reset Search Catalog

```powershell
Get-Service WSearch | Restart-Service
```

---

# 5. Add‑In Troubleshooting

## 5.1 Start Outlook in Safe Mode

```
outlook.exe /safe
```

If Outlook works:
- Disable add‑ins  
- Re-enable one by one  

---

## 5.2 Disable Problematic Add‑Ins

```
File → Options → Add‑ins → COM Add‑ins → Go
```

Common problematic add‑ins:
- CRM add‑ins  
- Antivirus add‑ins  
- Third‑party sync tools  

---

# 6. OST/PST Troubleshooting

## 6.1 Repair OST/PST

Run Inbox Repair Tool:

```
scanpst.exe
```

Location:
```
C:\Program Files\Microsoft Office\root\Office16\
```

---

## 6.2 Reduce OST Size

- Archive old mail  
- Empty Deleted Items  
- Disable unnecessary shared mailboxes  

---

# 7. Calendar Troubleshooting

## 7.1 Reset Calendar View

```
View → Reset View
```

---

## 7.2 Clear Calendar Cache

```
outlook.exe /cleanviews
outlook.exe /cleanfreebusy
```

---

## 7.3 Check Delegate Permissions

### PowerShell
```powershell
Get-MailboxFolderPermission -Identity jsmith@domain.com:\Calendar
```

---

# 8. Authentication & MFA Issues

## 8.1 Clear Cached Credentials

```
Control Panel → Credential Manager → Windows Credentials
```

Remove:
- Outlook credentials  
- MicrosoftOffice16 credentials  

---

## 8.2 Reset Modern Authentication

```
outlook.exe /resetnavpane
```

---

## 8.3 Check Conditional Access Policies

Common issues:
- Location restrictions  
- Device compliance requirements  
- MFA enforcement  

---

# 9. Performance Troubleshooting

## 9.1 Disable Hardware Acceleration

```
File → Options → Advanced → Disable hardware acceleration
```

---

## 9.2 Reduce Add‑Ins

Disable unnecessary add‑ins.

---

## 9.3 Optimize Mailbox Size

Large mailboxes slow Outlook.

---

# 10. Troubleshooting Mail Flow (Outlook-Specific)

## 10.1 Check Message Trace

```
Microsoft 365 Admin Center → Mail Flow → Message Trace
```

---

## 10.2 Check Transport Rules

Rules may block or redirect mail.

---

## 10.3 Check Junk/Clutter/Focused Inbox

Users may think mail is missing.

---

# 11. Verification Checklist

- Outlook connects successfully  
- Autodiscover working  
- Search functioning  
- No crashes or freezes  
- Calendar syncing correctly  
- Add‑ins stable  
- Mail flow normal  
- No authentication loops  

---

# 12. Best Practices

- Keep Outlook updated  
- Use Cached Mode for performance  
- Limit mailbox size  
- Avoid PST files when possible  
- Use Microsoft Authenticator for MFA  
- Document add‑ins used in the organization  
- Train users on Outlook Web as fallback  

---

# References

- Microsoft Learn — Outlook Troubleshooting  
- Microsoft Learn — Exchange Online Connectivity  
- Microsoft Learn — Autodiscover  
