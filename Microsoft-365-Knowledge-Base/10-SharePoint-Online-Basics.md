
# Microsoft 365 Knowledge Base  
## 10 — SharePoint Online Basics

---

## Overview

SharePoint Online is Microsoft’s cloud-based platform for document management, collaboration, intranet portals, and team sites. It integrates with OneDrive, Teams, and Microsoft 365 apps to provide secure storage, sharing, and workflow automation.

This document covers:
- SharePoint site types  
- Document libraries  
- Permissions and sharing  
- Syncing with OneDrive  
- Navigation and structure  
- Common issues  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Site Collection** | A top-level SharePoint site with subsites |
| **Team Site** | Collaboration-focused site connected to a Microsoft 365 Group |
| **Communication Site** | Publishing-focused site for broad communication |
| **Document Library** | Storage location for files and folders |
| **List** | Structured data (tasks, inventory, contacts) |
| **Permissions** | Access control using SharePoint groups or M365 groups |
| **Versioning** | Tracks changes to documents |
| **Metadata** | Tags/columns used to organize content |

---

## Tools Used

- SharePoint Admin Center  
- SharePoint Online Web Interface  
- OneDrive Sync Client  
- Microsoft Teams  
- PowerShell (SharePoint Online Module)  

---

# 1. SharePoint Site Types

## 1.1 Team Sites (Collaboration)

Used for:
- Department collaboration  
- Project teams  
- File sharing  
- Integrated with Microsoft 365 Groups and Teams  

Features:
- Shared mailbox  
- Planner  
- OneNote  
- Teams integration  

---

## 1.2 Communication Sites (Publishing)

Used for:
- Company announcements  
- Intranet pages  
- Policies and procedures  
- HR or IT portals  

Features:
- Modern pages  
- News posts  
- Visual layouts  

---

## 1.3 Classic Sites (Legacy)

Older SharePoint experience.  
Avoid creating new classic sites unless required for legacy apps.

---

# 2. Document Libraries

Document libraries store files and folders.

## 2.1 Common Features

- Version history  
- Check-in/check-out  
- Metadata columns  
- Views (list, tiles, grouped)  
- Sharing and permissions  
- Sync with OneDrive  

---

## 2.2 Creating a Document Library

```
Site → Site Contents → New → Document Library
```

---

## 2.3 Version History

Right-click a file → **Version History**

Allows:
- Restore previous versions  
- Compare changes  
- View who modified the file  

---

## 2.4 Metadata Columns

Examples:
- Department  
- Status  
- Category  
- Owner  

Metadata improves search and filtering.

---

# 3. Permissions & Sharing

## 3.1 Permission Levels

| Level | Description |
|--------|-------------|
| **Full Control** | Admin access |
| **Edit** | Add/edit/delete items |
| **Contribute** | Add/edit items |
| **Read** | View only |
| **View Only** | Restricted read |

---

## 3.2 SharePoint Groups

Default groups:
- Owners  
- Members  
- Visitors  

---

## 3.3 Sharing Files & Folders

```
File → Share → Link Settings
```

Options:
- People in your organization  
- Specific people  
- Anyone with the link (if allowed)  
- View/Edit permissions  

---

## 3.4 External Sharing

Controlled via SharePoint Admin Center:
```
Policies → Sharing
```

---

# 4. Syncing SharePoint Libraries with OneDrive

## 4.1 Sync a Library

```
Open library → Click “Sync”
```

OneDrive client begins syncing.

---

## 4.2 Stop Syncing

```
OneDrive → Settings → Stop Sync
```

---

## 4.3 Common Sync Issues

- Unsupported characters  
- Long file paths  
- Permissions issues  
- Library requiring checkout  

---

# 5. Navigation & Structure

## 5.1 Site Navigation

Left navigation:
- Document libraries  
- Lists  
- Pages  
- Recycle bin  

Top navigation:
- Site settings  
- Teams integration  
- SharePoint app bar  

---

## 5.2 Pages & Web Parts

Modern pages support:
- Text  
- Images  
- Document libraries  
- Lists  
- Quick links  
- News posts  
- Embedded content  

---

## 5.3 Recycle Bin

Deleted items remain for:
- **93 days**  
- Can be restored by site owners  

---

# 6. Common SharePoint Issues

## Issue 1 — User cannot access site

### Causes
- Missing permissions  
- Incorrect group membership  
- External sharing disabled  

### Fix
- Add user to Members or Visitors  
- Check M365 Group membership  
- Review sharing settings  

---

## Issue 2 — Files not opening in browser

### Causes
- Default open behavior set to desktop app  
- Browser compatibility issues  

### Fix
```
Library Settings → Advanced Settings → Open in browser
```

---

## Issue 3 — Sync errors

### Causes
- Unsupported characters  
- Long paths  
- Permissions issues  

### Fix
- Rename files  
- Shorten folder structure  
- Reset OneDrive  

---

## Issue 4 — Missing files

### Causes
- User deleted files  
- Sync conflict  
- Versioning confusion  

### Fix
- Check Recycle Bin  
- Review version history  
- Check OneDrive sync logs  

---

# 7. Troubleshooting

## 7.1 Check Site Permissions

### PowerShell
```powershell
Get-SPOSite -Identity https://domain.sharepoint.com/sites/TeamSite
```

---

## 7.2 Check Sharing Settings

```
SharePoint Admin Center → Policies → Sharing
```

---

## 7.3 Check Storage Quota

```
SharePoint Admin Center → Sites → Active Sites → Storage
```

---

## 7.4 Check Sync Health

Use OneDrive sync logs:
```
%localappdata%\Microsoft\OneDrive\logs
```

---

# 8. Verification Checklist

- User can access site  
- Document library loads  
- Files open correctly  
- Sync working  
- Permissions correct  
- No missing files  
- External sharing configured (if needed)  

---

# 9. Best Practices

- Use Microsoft 365 Groups for team sites  
- Use communication sites for intranet content  
- Avoid deep folder structures  
- Use metadata instead of folders  
- Enable versioning  
- Train users on sharing securely  
- Regularly review site permissions  
- Use OneDrive sync for offline access  

---

# References

- Microsoft Learn — SharePoint Online  
- Microsoft Learn — Document Libraries  
- Microsoft Learn — SharePoint Admin Center  
