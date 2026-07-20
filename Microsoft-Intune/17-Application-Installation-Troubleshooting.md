
# Microsoft Intune Knowledge Base  
## 17 — Application Installation Troubleshooting

---

## Overview

Application installation issues are among the most common challenges in Microsoft Intune environments. Whether deploying Win32 apps, Microsoft Store apps, LOB apps, macOS PKGs, or mobile apps, failures can occur due to configuration errors, detection rule mismatches, device health issues, or network restrictions.

This document provides a structured troubleshooting guide for resolving Intune application installation failures across Windows, macOS, iOS/iPadOS, and Android.

This document covers:
- Application deployment flow  
- Win32 app troubleshooting  
- Store app troubleshooting  
- LOB app troubleshooting  
- IME logs  
- Detection rule issues  
- Network & certificate issues  
- Device sync issues  
- Best practices  
- **Workflow diagram for app installation troubleshooting**  

---

## 🧩 Workflow Diagram — Application Installation Troubleshooting Flow

```mermaid
flowchart TD

A[User Reports App Not Installing] --> B[Identify App Type]

B --> C1[Win32 App (.intunewin)]
B --> C2[Microsoft Store App]
B --> C3[Line-of-Business (LOB) App]
B --> C4[Mobile App (iOS/Android)]
B --> C5[macOS PKG App]

C1 --> D1[Check IME Logs<br/>Install/Uninstall Commands]
D1 --> E1[Validate Detection Rules]

C2 --> D2[Check Store Availability<br/>Network/Proxy]
D2 --> E2[Sync Device & Retry]

C3 --> D3[Check App Requirements<br/>OS, Architecture]
D3 --> E3[Validate Installer Package]

C4 --> D4[Check App Assignment<br/>Managed Google Play / App Store]
D4 --> E4[Check Device Enrollment & MAM Policies]

C5 --> D5[Check PKG Signing & Permissions]
D5 --> E5[Validate macOS MDM Profile]

E1 --> F[Device Syncs with Intune]
E2 --> F
E3 --> F
E4 --> F
E5 --> F

F --> G[App Installation Attempted Again]
G --> H{Installed Successfully?}

H -->|Yes| I[App Installed → Issue Resolved]
H -->|No| J[Collect Logs & Escalate]

J --> K[Admin Reviews Logs<br/>IME, MDM Diagnostics, Event Viewer]
K --> L[Fix Issue<br/>Policy Adjustments, Repackage App]
L --> F
```

---

# 1. Understanding Intune App Installation Flow

Intune installs apps using:
- Intune Management Extension (IME) for Win32 apps  
- Microsoft Store integration for Store apps  
- MDM channels for LOB and mobile apps  
- PKG/DMG installers for macOS  

A successful installation requires:
- Correct app packaging  
- Valid detection rules  
- Proper assignment  
- Device compliance  
- Network access  
- Healthy IME agent  

---

# 2. Win32 App Troubleshooting

Win32 apps are the most complex and most prone to installation issues.

## 2.1 Check IME Logs

Location:
```
C:\ProgramData\Microsoft\IntuneManagementExtension\Logs
```

Important logs:
- IntuneManagementExtension.log  
- AgentExecutor.log  
- AppInstallWorker.log  

---

## 2.2 Validate Install/Uninstall Commands

Common issues:
- Incorrect silent install switches  
- Installer requiring user interaction  
- Missing dependencies  

Test manually:
```powershell
setup.exe /quiet /norestart
```

---

## 2.3 Validate Detection Rules

Detection rule failures cause:
- App stuck on “Installed”  
- App stuck on “Pending”  
- App reinstall loops  

Check:
- File path  
- Registry key  
- MSI product code  

---

## 2.4 Check App Requirements

Examples:
- OS version  
- Architecture (x64/x86)  
- Disk space  

---

## 2.5 Repackage App

If installation fails repeatedly:
- Repackage using IntuneWinAppUtil  
- Validate folder structure  
- Ensure correct setup file  

---

# 3. Microsoft Store App Troubleshooting

## 3.1 Check Store Availability

Common issues:
- Store disabled via GPO  
- Store blocked by firewall  
- Store service not running  

Fix:
- Enable Microsoft Store  
- Allow Store endpoints  

---

## 3.2 Network/Proxy Issues

Store apps require access to:
- `storeedgefd.dsx.mp.microsoft.com`  
- `login.microsoftonline.com`  

---

## 3.3 Sync Device

```
Company Portal → Devices → Sync
```

---

# 4. Line-of-Business (LOB) App Troubleshooting

## 4.1 Validate Installer Package

Check:
- MSI validity  
- PKG signing  
- APK compatibility  

---

## 4.2 Check App Requirements

Examples:
- OS version  
- Architecture  
- Dependencies  

---

## 4.3 Check Assignment

Ensure app is assigned to:
- Correct user/device group  
- Correct platform  

---

# 5. Mobile App Troubleshooting (iOS/Android)

## 5.1 iOS/iPadOS

Common issues:
- App not available in region  
- Device not enrolled  
- MAM-only access blocking install  

Fix:
- Check App Store availability  
- Ensure device is MDM-enrolled  

---

## 5.2 Android

Common issues:
- Managed Google Play not synced  
- Work profile not created  
- App not approved  

Fix:
- Sync Managed Google Play  
- Recreate work profile  

---

# 6. macOS PKG Troubleshooting

## 6.1 Check PKG Signing

Unsigned PKGs may fail silently.

---

## 6.2 Validate MDM Profile

macOS requires:
- MDM profile installed  
- User-approved MDM (older macOS versions)  

---

## 6.3 Check Permissions

PKG may require:
- Admin rights  
- Pre-install scripts  

---

# 7. Network & Certificate Troubleshooting

## 7.1 Check Intune Endpoints

Ensure access to:
- `manage.microsoft.com`  
- `login.microsoftonline.com`  
- `storeedgefd.dsx.mp.microsoft.com`  

---

## 7.2 SSL Inspection Issues

SSL inspection may break:
- Store apps  
- IME downloads  
- App installations  

Fix:
- Exclude Intune URLs from SSL inspection  

---

# 8. Device Sync Troubleshooting

## 8.1 Force Sync

Windows:
```
Settings → Accounts → Access work or school → Info → Sync
```

Company Portal:
```
Company Portal → Devices → Sync
```

---

## 8.2 Restart IME

Restart service:
```
IntuneManagementExtension
```

---

# 9. Logs & Diagnostics

## 9.1 Windows MDM Diagnostic Logs

```powershell
mdmdiagnosticstool.exe -area DeviceEnrollment -cab C:\MDMDiag.cab
```

---

## 9.2 Company Portal Logs

Windows:
```
%localappdata%\Packages\Microsoft.CompanyPortal_8wekyb3d8bbwe\LocalState\Logs
```

Mobile:
- Export logs via Company Portal settings  

---

# 10. Verification Checklist

| Task | Completed |
|------|-----------|
| App type identified | ✔ |
| Logs reviewed | ✔ |
| Detection rules validated | ✔ |
| Install commands tested | ✔ |
| Device synced | ✔ |
| Network connectivity verified | ✔ |
| App installed successfully | ✔ |

---

# 11. Best Practices

- Always test Win32 apps before deployment  
- Use correct silent install switches  
- Document detection rules  
- Monitor IME logs regularly  
- Use dynamic groups for app targeting  
- Avoid duplicate app assignments  
- Validate Store access in corporate networks  

---

# References

- Microsoft Learn — Intune App Deployment  
- Microsoft Learn — Win32 App Packaging  
- Microsoft Learn — App Troubleshooting  
```
