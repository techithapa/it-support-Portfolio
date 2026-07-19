
# Microsoft 365 Knowledge Base  
## 17 — Device Management with Intune

---

## Overview

Microsoft Intune is Microsoft’s cloud-based endpoint management solution used to manage devices, enforce security policies, deploy applications, and protect corporate data. Intune is part of the Microsoft Endpoint Manager suite and integrates tightly with Microsoft Entra ID (Azure AD), Microsoft 365, and Defender for Endpoint.

This document covers:
- Intune architecture  
- Device enrollment  
- Compliance policies  
- Configuration profiles  
- Application deployment  
- Endpoint security  
- Monitoring and reporting  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **MDM (Mobile Device Management)** | Full device management including settings, compliance, and restrictions |
| **MAM (Mobile Application Management)** | Protects corporate data inside apps without managing the device |
| **Device Enrollment** | Registers devices into Intune for management |
| **Compliance Policies** | Ensure devices meet security requirements |
| **Configuration Profiles** | Configure device settings (Wi-Fi, VPN, restrictions) |
| **App Protection Policies** | Protect corporate data in apps |
| **Endpoint Security** | Controls antivirus, firewall, disk encryption |
| **Co‑Management** | Hybrid management with Intune + Configuration Manager |

---

## Tools Used

- Intune Admin Center  
- Microsoft Entra Admin Center  
- Microsoft 365 Admin Center  
- Company Portal App  
- PowerShell (Graph)  
- Endpoint Security Portal  

---

# 1. Intune Architecture

## 1.1 Components

- **Intune MDM** — full device management  
- **Intune MAM** — app-level protection  
- **Microsoft Entra ID** — identity and Conditional Access  
- **Defender for Endpoint** — endpoint protection  
- **Configuration Manager** — optional co-management  

---

## 1.2 Supported Platforms

- Windows 10/11  
- macOS  
- iOS/iPadOS  
- Android  
- Linux (limited)  

---

# 2. Device Enrollment

## 2.1 Enrollment Methods

| Platform | Enrollment Method |
|----------|-------------------|
| Windows | Azure AD Join, Autopilot, Company Portal |
| iOS/Android | Company Portal, Apple DEP, Android Enterprise |
| macOS | Company Portal, Apple Business Manager |

---

## 2.2 Windows Enrollment

### Azure AD Join
```
Settings → Accounts → Access work or school → Connect
```

### Autopilot (Recommended)
- Zero-touch provisioning  
- Preconfigured profiles  
- Automated setup  

---

## 2.3 Mobile Enrollment (iOS/Android)

### Steps
1. Install **Company Portal**  
2. Sign in with Microsoft 365 account  
3. Register device  
4. Accept management profile  
5. Device becomes compliant  

---

# 3. Compliance Policies

Compliance policies ensure devices meet security requirements before accessing corporate resources.

## 3.1 Common Compliance Settings

- Require PIN/password  
- Require encryption  
- Block jailbroken/rooted devices  
- Require minimum OS version  
- Require antivirus  
- Require firewall  
- Require BitLocker/FileVault  

---

## 3.2 Create Compliance Policy

```
Intune Admin Center → Devices → Compliance Policies → Create Policy
```

---

## 3.3 Non‑Compliant Device Actions

- Email notification  
- Mark device non‑compliant  
- Block access via Conditional Access  
- Wipe corporate data  

---

# 4. Conditional Access Integration

Conditional Access ensures only compliant devices can access corporate apps.

## 4.1 Recommended Policies

- Require MFA  
- Require compliant device  
- Block legacy authentication  
- Require approved apps (Outlook, Teams)  
- Restrict access by location  

---

## 4.2 Example Policy

```
Entra Admin Center → Protection → Conditional Access → New Policy
Users: All users  
Apps: Office 365  
Grant: Require device to be marked as compliant
```

---

# 5. Configuration Profiles

Configuration profiles allow administrators to configure device settings.

## 5.1 Common Profile Types

- Device restrictions  
- Wi-Fi profiles  
- VPN profiles  
- Email profiles  
- Certificates  
- Endpoint protection  
- Custom OMA-URI settings  

---

## 5.2 Create Configuration Profile

```
Intune Admin Center → Devices → Configuration Profiles → Create Profile
```

---

# 6. Application Deployment

## 6.1 Supported App Types

- Win32 apps  
- Microsoft Store apps  
- iOS/Android apps  
- macOS apps  
- Web apps  

---

## 6.2 Deploy Apps

```
Intune Admin Center → Apps → All Apps → Add
```

Configure:
- Install behavior  
- Requirements  
- Detection rules  
- Dependencies  

---

## 6.3 App Protection Policies (MAM)

Protect data inside apps without enrolling the device.

### Common Controls
- Require PIN  
- Prevent copy/paste  
- Encrypt app data  
- Block saving to personal storage  
- Wipe corporate data only  

---

# 7. Endpoint Security

Endpoint security integrates Intune with Microsoft Defender.

## 7.1 Security Controls

- Antivirus policies  
- Firewall policies  
- Disk encryption (BitLocker/FileVault)  
- Attack surface reduction  
- Device control (USB restrictions)  

---

## 7.2 Configure Endpoint Security

```
Intune Admin Center → Endpoint Security
```

---

# 8. Monitoring & Reporting

## 8.1 Device Reports

- Compliance status  
- Enrollment failures  
- App installation status  
- Security baselines  
- Update status  

---

## 8.2 Log Collection

Windows devices support remote log collection.

```
Intune Admin Center → Devices → Select Device → Collect Diagnostics
```

---

# 9. Troubleshooting Intune

## Issue 1 — Device not enrolling

### Causes
- Incorrect license  
- Company Portal not installed  
- Conditional Access blocking  

### Fix
- Assign Intune license  
- Install Company Portal  
- Review CA policies  

---

## Issue 2 — Device not compliant

### Causes
- Missing PIN  
- OS outdated  
- Encryption disabled  

### Fix
- Enable PIN  
- Update OS  
- Enable encryption  

---

## Issue 3 — Apps not installing

### Causes
- Detection rule failure  
- Network restrictions  
- Incorrect app format  

### Fix
- Review detection rules  
- Check firewall/proxy  
- Repackage app  

---

## Issue 4 — Conditional Access blocking access

### Causes
- Device not marked compliant  
- MAM policy conflict  

### Fix
- Check compliance status  
- Review MAM policies  

---

# 10. Verification Checklist

- Device enrolled  
- Compliance policy applied  
- Conditional Access functioning  
- Configuration profiles applied  
- Apps installed successfully  
- Endpoint security active  
- No access errors  
- Device visible in Intune  

---

# 11. Best Practices

- Use Autopilot for Windows deployment  
- Require compliant devices for all corporate apps  
- Use App Protection Policies for BYOD  
- Enforce encryption and PIN requirements  
- Block jailbroken/rooted devices  
- Document Intune policies  
- Review device compliance monthly  
- Use Conditional Access for Zero Trust enforcement  

---

# References

- Microsoft Learn — Intune  
- Microsoft Learn — Endpoint Security  
- Microsoft Learn — Conditional Access  
- Microsoft Learn — App Protection Policies  
