
# Microsoft 365 Knowledge Base  
## 11 — Microsoft 365 Apps Installation

---

## Overview

Microsoft 365 Apps (formerly Office 365 ProPlus) provides cloud‑connected versions of Word, Excel, PowerPoint, Outlook, Teams, OneDrive, and other productivity tools. Proper installation ensures users have secure, updated, and fully functional applications across Windows, macOS, and mobile devices.

This document covers:
- Installation methods  
- System requirements  
- Activation and licensing  
- Update channels  
- Common installation issues  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Microsoft 365 Apps** | Subscription-based Office apps |
| **Click‑to‑Run** | Streaming installation technology |
| **Activation** | Sign‑in required to activate apps |
| **Update Channels** | Determines how frequently apps update |
| **Office Deployment Tool (ODT)** | Customizable installation tool |
| **Intune Deployment** | Cloud-based app deployment |

---

## Tools Used

- Microsoft 365 Admin Center  
- Office.com Portal  
- Office Deployment Tool (ODT)  
- Intune Admin Center  
- PowerShell (Office Management)  
- Windows Installer Logs  

---

# 1. Installation Methods

## 1.1 Install via Office.com (Recommended for Users)

```
[https://www.office.com](https://www.office.com) → Sign in → Install Office
```

Installs:
- Word  
- Excel  
- PowerPoint  
- Outlook  
- Teams  
- OneDrive  
- Access (Windows only)  
- Publisher (Windows only)  

---

## 1.2 Install via Microsoft 365 Admin Center (Admins)

```
Users → Active Users → Select User → Apps & Devices
```

Admins can download installers for:
- Windows  
- macOS  

---

## 1.3 Install via Intune (Enterprise Deployment)

```
Intune Admin Center → Apps → Windows Apps → Add → Microsoft 365 Apps
```

Configure:
- Update channel  
- App exclusions  
- Architecture (32/64-bit)  
- Shared computer activation  

---

## 1.4 Install via Office Deployment Tool (ODT)

### Download ODT
```
`https://www.microsoft.com/en-us/download/details.aspx?id=49117` (microsoft.com in Bing) [(bing.com in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fwww.bing.com%2Fsearch%3Fq%3D%2522https%253A%252F%252Fwww.microsoft.com%252Fen-us%252Fdownload%252Fdetails.aspx%253Fid%253D49117%2522")
```

### Example Configuration XML
```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="MonthlyEnterprise">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-us" />
    </Product>
  </Add>
  <Updates Enabled="TRUE" />
  <Display Level="None" AcceptEULA="TRUE" />
</Configuration>
```

### Install Office
```powershell
setup.exe /configure config.xml
```

---

# 2. System Requirements

## 2.1 Windows

- Windows 10/11  
- 4 GB RAM minimum  
- 4 GB disk space  
- Internet connection required for activation  

## 2.2 macOS

- Latest three macOS versions  
- 4 GB RAM  
- 10 GB disk space  

## 2.3 Mobile

- iOS 15+  
- Android 10+  

---

# 3. Activation & Licensing

## 3.1 Sign‑In Required

Users must sign in with:
```
user@domain.com
```

Activation is tied to Microsoft 365 license.

---

## 3.2 Check Activation Status

### Windows
```
File → Account → Product Information
```

### macOS
```
Word → About Microsoft Word
```

---

## 3.3 Shared Computer Activation (SCA)

Used for:
- Remote desktops  
- Citrix  
- Multi-user environments  

Enable via ODT:
```xml
<Configuration>
  <Property Name="SharedComputerLicensing" Value="1" />
</Configuration>
```

---

# 4. Update Channels

| Channel | Description |
|---------|-------------|
| **Current Channel** | Monthly updates |
| **Monthly Enterprise Channel** | Predictable monthly updates |
| **Semi‑Annual Enterprise Channel** | Twice-yearly updates |
| **Beta Channel** | Preview features |

### Change Update Channel (PowerShell)
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration" `
-Name "CDNBaseUrl" -Value "MonthlyEnterprise"
```

---

# 5. Common Installation Issues

## Issue 1 — “We couldn’t install Office”

### Causes
- Previous Office versions installed  
- Click‑to‑Run conflicts  
- Insufficient permissions  

### Fix
- Uninstall old Office  
- Run Office Uninstall Tool  
- Install as admin  

---

## Issue 2 — Activation Failed

### Causes
- License not assigned  
- Sign‑in issues  
- Conditional Access restrictions  

### Fix
- Assign license  
- Clear credentials  
- Review CA policies  

---

## Issue 3 — Apps Missing (e.g., Access, Publisher)

### Causes
- Wrong license SKU  
- macOS limitations  
- ODT exclusions  

### Fix
- Check license  
- Install on Windows  
- Update ODT config  

---

## Issue 4 — Slow Installation

### Causes
- Network congestion  
- VPN interference  
- Low disk space  

### Fix
- Disable VPN  
- Use wired connection  
- Free disk space  

---

## Issue 5 — Outlook Not Configuring

### Causes
- Autodiscover issues  
- Profile corruption  
- MFA/CA restrictions  

### Fix
- Create new profile  
- Clear credentials  
- Check CA policies  

---

# 6. Troubleshooting

## 6.1 Office Repair

### Windows
```
Control Panel → Programs → Office → Change → Quick Repair / Online Repair
```

### macOS
Reinstall Office.

---

## 6.2 Reset Office Activation

```
%localappdata%\Microsoft\Office\16.0\Licensing
```

Delete activation files.

---

## 6.3 Check Office Logs

```
%temp%\Microsoft\Office\Logs
```

---

## 6.4 Remove Old Office Versions

Use Office Removal Tool:
```
https://aka.ms/diag_officeuninstall
```

---

# 7. Verification Checklist

- Apps installed successfully  
- Activation complete  
- Outlook configured  
- Teams functional  
- OneDrive signed in  
- Update channel correct  
- No installation errors  

---

# 8. Best Practices

- Use Intune for enterprise deployment  
- Use Monthly Enterprise Channel for stability  
- Avoid mixing MSI and Click‑to‑Run  
- Document installation standards  
- Train users to install via Office.com  
- Use ODT for custom deployments  
- Regularly audit activation status  

---

# References

- Microsoft Learn — Microsoft 365 Apps  
- Microsoft Learn — Office Deployment Tool  
- Microsoft Learn — Intune App Deployment  
