
# Microsoft 365 Knowledge Base  
## 08 — Microsoft Teams Troubleshooting

---

## Overview

Microsoft Teams is a core collaboration platform for chat, meetings, calls, and file sharing. Issues with Teams can disrupt communication, productivity, and business operations. This document provides a comprehensive troubleshooting guide for Teams Desktop, Teams Web, and Teams Mobile.

This guide covers:
- Sign‑in issues  
- Connectivity problems  
- Meeting and call issues  
- Audio/video device problems  
- Teams client performance  
- Teams channel and chat issues  
- File sharing and permissions  
- Notifications  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Teams Client** | Desktop, Web, and Mobile applications |
| **Teams Service** | Cloud backend that powers chat, meetings, and collaboration |
| **Teams Policies** | Control features such as meetings, messaging, calling |
| **Teams Cache** | Local data stored by the Teams client |
| **Teams Channels** | Collaboration spaces within a Team |
| **Teams Meetings** | Audio/video conferencing |
| **Teams Calling** | PSTN or VoIP calling features |

---

## Tools Used

- Teams Desktop Client  
- Teams Web (https://teams.microsoft.com)  
- Teams Mobile App  
- Microsoft 365 Admin Center  
- Teams Admin Center  
- PowerShell (Teams Module, Graph)  
- Windows Event Viewer  
- Network diagnostic tools  

---

# 1. Common Teams Issues

| Issue | Symptoms |
|-------|----------|
| Sign‑in issues | MFA loops, password prompts, login errors |
| Connectivity problems | “We’re having trouble connecting”, offline status |
| Meeting issues | Cannot join, dropped calls, poor quality |
| Audio/video issues | No mic, no camera, device not detected |
| File sharing issues | Cannot open files, permissions errors |
| Notifications not working | Missed messages, delayed alerts |
| Teams client crashes | Freezing, high CPU usage |
| Channel access issues | Missing channels, permission errors |

---

# 2. Sign‑In Troubleshooting

## 2.1 Clear Teams Cache

Close Teams completely, then delete cache:

```
%appdata%\Microsoft\Teams
```

Delete folders:
- Cache  
- GPUCache  
- IndexedDB  
- Local Storage  
- tmp  

Restart Teams.

---

## 2.2 Check Modern Authentication

Sign‑in issues often relate to:
- MFA  
- Conditional Access  
- Token expiration  

### Reset Teams token
```
Settings → Accounts → Sign out
```

---

## 2.3 Clear Credential Manager

```
Control Panel → Credential Manager → Windows Credentials
```

Remove:
- Teams credentials  
- Office credentials  

---

# 3. Connectivity Troubleshooting

## 3.1 Check Network Requirements

Teams requires:
- Ports 80/443  
- UDP 3478–3481  
- Stable bandwidth  

---

## 3.2 Run Teams Network Test

```
`https://connectivity.office.com` [(connectivity.office.com in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fconnectivity.office.com%2F")
```

---

## 3.3 Check Service Health

```
Microsoft 365 Admin Center → Health → Service Health → Teams
```

---

# 4. Meeting & Call Troubleshooting

## 4.1 Audio/Video Device Issues

### Reset Devices
```
Teams → Settings → Devices → Reset
```

### Check Windows Sound Settings
```
Settings → System → Sound → Input/Output
```

### Check Camera Settings
```
Settings → Privacy → Camera
```

---

## 4.2 Poor Call Quality

### Causes
- Network congestion  
- VPN interference  
- Low bandwidth  
- Outdated Teams client  

### Fix
- Use wired connection  
- Disable VPN  
- Update Teams client  
- Close background apps  

---

## 4.3 Cannot Join Meetings

### Causes
- Browser restrictions  
- Teams client corruption  
- Conditional Access policies  

### Fix
- Try Teams Web  
- Clear cache  
- Review CA policies  

---

# 5. Teams Client Performance Issues

## 5.1 High CPU or Memory Usage

### Fixes
- Disable GPU acceleration  
- Clear cache  
- Disable unnecessary add‑ins  
- Update Teams client  

---

## 5.2 Teams Crashing

### Fixes
- Reinstall Teams  
- Remove corrupted profile  
- Check Event Viewer logs  

---

# 6. Chat & Channel Troubleshooting

## 6.1 Missing Channels

### Causes
- User removed from Team  
- Hidden channels  
- Permissions changed  

### Fix
- Re-add user  
- Check channel visibility  
- Review Team settings  

---

## 6.2 Chat Not Syncing

### Fixes
- Clear cache  
- Check network connectivity  
- Sign out and sign in again  

---

# 7. File Sharing & Permissions

## 7.1 Cannot Open Files

### Causes
- SharePoint permissions  
- File moved or deleted  
- Sync issues  

### Fix
- Check SharePoint site permissions  
- Verify file location  
- Use Teams Web to test  

---

## 7.2 OneDrive Sync Issues

### Fixes
- Restart OneDrive  
- Reset OneDrive  
```
onedrive.exe /reset
```

---

# 8. Notifications Troubleshooting

## 8.1 Notifications Not Appearing

### Fixes
- Enable Teams notifications  
- Check Windows notification settings  
- Disable Focus Assist  
- Clear Teams cache  

---

## 8.2 Delayed Notifications

### Causes
- Battery saver mode  
- Background app restrictions  
- Network issues  

### Fix
- Allow Teams to run in background  
- Disable battery saver  
- Check network stability  

---

# 9. Admin-Level Troubleshooting

## 9.1 Check Teams Policies

### PowerShell
```powershell
Get-CsTeamsMessagingPolicy
Get-CsTeamsMeetingPolicy
Get-CsTeamsCallingPolicy
```

---

## 9.2 Check User Policy Assignment

```powershell
Get-CsOnlineUser -Identity jsmith@domain.com
```

---

## 9.3 Reset User Policies

```powershell
Grant-CsTeamsMessagingPolicy -PolicyName Global -Identity jsmith@domain.com
```

---

# 10. Verification Checklist

- Teams client launches successfully  
- User can sign in  
- Chat and channels sync  
- Meetings and calls work  
- Audio/video devices detected  
- File sharing functional  
- Notifications working  
- No performance issues  

---

# 11. Best Practices

- Keep Teams updated  
- Use Teams Web for quick troubleshooting  
- Avoid VPN for calls/meetings  
- Train users on Teams basics  
- Use Teams Admin Center for monitoring  
- Document Teams policies  
- Review Teams usage reports  

---

# References

- Microsoft Learn — Teams Troubleshooting  
- Microsoft Learn — Teams Admin Center  
- Microsoft Learn — Teams Connectivity Requirements  
