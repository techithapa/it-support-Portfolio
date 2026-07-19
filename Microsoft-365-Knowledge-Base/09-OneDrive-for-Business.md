
# Microsoft 365 Knowledge Base  
## 09 — OneDrive for Business

---

## Overview

OneDrive for Business provides secure cloud storage for users to store, sync, and share files across devices. It integrates with Microsoft 365 apps, Teams, and SharePoint, enabling seamless collaboration. Issues with OneDrive can disrupt file access, syncing, and sharing.

This document covers:
- OneDrive setup  
- Sync client troubleshooting  
- File sharing and permissions  
- Known sync errors  
- Storage limits  
- Reset and repair procedures  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **OneDrive Sync Client** | Application that syncs files between device and cloud |
| **Personal OneDrive** | User’s private cloud storage |
| **SharePoint Library Sync** | Syncing team site document libraries |
| **Known Folder Move (KFM)** | Redirects Desktop/Documents/Pictures to OneDrive |
| **Storage Quota** | Default 1 TB per user (expandable) |
| **Sharing Links** | Controls access to shared files |

---

## Tools Used

- OneDrive Sync Client  
- OneDrive Web (https://onedrive.live.com)  
- Microsoft 365 Admin Center  
- SharePoint Admin Center  
- PowerShell (SharePoint Online)  
- Windows Event Viewer  

---

# 1. OneDrive Setup

## 1.1 Install OneDrive Sync Client

OneDrive is included with Windows 10/11.  
Download manually if needed:

```
`https://www.microsoft.com/en-us/microsoft-365/onedrive/download` [(microsoft.com in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fwww.microsoft.com%2Fen-us%2Fmicrosoft-365%2Fonedrive%2Fdownload")
```

---

## 1.2 Sign In to OneDrive

```
Start → OneDrive → Sign in → user@domain.com
```

---

## 1.3 Known Folder Move (KFM)

Redirects Desktop, Documents, and Pictures to OneDrive.

### Enable via OneDrive Settings
```
OneDrive → Settings → Backup → Manage Backup
```

### Admin Control (Intune)
```
Devices → Configuration Profiles → OneDrive Settings
```

---

# 2. Sync Client Troubleshooting

## 2.1 Check Sync Status

OneDrive icon states:
- **Blue cloud** = Online only  
- **Green check** = Synced  
- **Red X** = Sync error  
- **Grey icon** = Not signed in  

---

## 2.2 Restart OneDrive

```
Right‑click OneDrive → Quit OneDrive
Start → OneDrive
```

---

## 2.3 Reset OneDrive (Most Common Fix)

```
%localappdata%\Microsoft\OneDrive\onedrive.exe /reset
```

If OneDrive does not restart:
```
%localappdata%\Microsoft\OneDrive\onedrive.exe
```

---

## 2.4 Repair OneDrive

```
OneDrive → Settings → About → Reset OneDrive
```

---

## 2.5 Clear Credential Manager

```
Control Panel → Credential Manager → Windows Credentials
```

Remove:
- OneDrive credentials  
- Office credentials  

---

# 3. Common OneDrive Sync Errors

## Error 1 — “Files not syncing”

### Causes
- No internet  
- Sync paused  
- File path too long  
- Invalid characters  

### Fix
- Resume sync  
- Shorten file path  
- Remove invalid characters  
- Reset OneDrive  

---

## Error 2 — “We couldn’t sync this library”

### Causes
- SharePoint permissions  
- Library requires checkout  
- Unsupported column types  

### Fix
- Check SharePoint permissions  
- Disable checkout requirement  
- Remove unsupported metadata  

---

## Error 3 — “Account needs attention”

### Causes
- Password change  
- MFA requirement  
- Conditional Access  

### Fix
- Sign out and sign in  
- Complete MFA  
- Review CA policies  

---

## Error 4 — “File locked by another user”

### Causes
- File open in Office  
- Sync conflict  
- Co‑authoring issue  

### Fix
- Close file  
- Save changes  
- Check version history  

---

# 4. File Sharing & Permissions

## 4.1 Sharing Options

| Option | Description |
|--------|-------------|
| **People in your organization** | Internal sharing |
| **Specific people** | Most secure |
| **Anyone with the link** | External sharing (if allowed) |

---

## 4.2 Manage Sharing Links

```
OneDrive Web → File → Share → Link Settings
```

Options:
- View only  
- Edit  
- Block download  
- Set expiration  
- Set password  

---

## 4.3 Stop Sharing

```
OneDrive Web → File → Manage Access → Remove links
```

---

# 5. Storage Limits

Default storage:
- **1 TB per user**

Admins can increase to:
- **5 TB** (E3/E5)  
- **25 TB** (upon request)  

### Check storage usage
```
OneDrive Web → Settings → Storage
```

---

# 6. OneDrive Admin Controls

## 6.1 Block external sharing

SharePoint Admin Center:
```
Policies → Sharing → OneDrive Sharing Settings
```

---

## 6.2 Restrict syncing to domain‑joined devices

```
SharePoint Admin Center → Device Access
```

---

## 6.3 Disable personal OneDrive accounts

Intune:
```
Administrative Templates → OneDrive → Block consumer OneDrive
```

---

# 7. Troubleshooting Known Folder Move (KFM)

## Issue — Folders not redirecting

### Causes
- Folder contains unsupported files  
- Permissions issue  
- OneDrive not signed in  

### Fix
- Remove unsupported files  
- Reset OneDrive  
- Re-enable KFM  

---

# 8. Verification Checklist

- OneDrive signed in  
- Sync status green  
- Files accessible online  
- Sharing links working  
- No sync errors  
- KFM functioning  
- Storage within limits  

---

# 9. Best Practices

- Use Known Folder Move for backup  
- Avoid syncing large PST files  
- Use “Specific people” for secure sharing  
- Train users on OneDrive Web  
- Monitor storage usage  
- Use Intune to enforce OneDrive policies  
- Regularly review sharing permissions  

---

# References

- Microsoft Learn — OneDrive for Business  
- Microsoft Learn — OneDrive Sync Troubleshooting  
- Microsoft Learn — SharePoint & OneDrive Admin  
