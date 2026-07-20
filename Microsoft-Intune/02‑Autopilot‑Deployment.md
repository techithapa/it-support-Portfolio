
# Microsoft Intune Knowledge Base  
## 02 — Autopilot Deployment

---

## Overview

Windows Autopilot is Microsoft’s modern provisioning solution for deploying Windows 10/11 devices with zero‑touch setup. Autopilot enables standardized configuration, automatic Intune enrolment, and seamless user-driven or pre-provisioned deployment workflows.

This document covers:
- Autopilot concepts  
- Deployment methods  
- Hardware hash collection  
- Profile assignment  
- Enrollment status page (ESP)  
- Pre-provisioning (White Glove)  
- Troubleshooting  
- Best practices  
- **Workflow diagram for Autopilot deployment**  

---

## 🧩 Workflow Diagram — Windows Autopilot Deployment

```mermaid
flowchart TD

A[Start: New Windows Device] --> B[Collect Hardware Hash]
B --> C[Upload Hash to Intune]
C --> D[Assign Autopilot Profile]
D --> E[Assign Device to User (Optional)]
E --> F[Device Shipped to User]

F --> G[User Powers On Device]
G --> H[Autopilot OOBE Starts]
H --> I[Device Joins Entra ID]
I --> J[Intune MDM Enrollment]

J --> K[Enrollment Status Page (ESP)]
K --> L[Apps Installed<br/>Policies Applied<br/>Security Baselines Enforced]

L --> M[Device Marked Compliant]
M --> N[Conditional Access Grants Access]
N --> O[Device Fully Managed]
```

---

# 1. Autopilot Concepts

## 1.1 What Autopilot Does

- Zero‑touch provisioning  
- Automatic Intune enrolment  
- Standardized configuration  
- User-driven or pre-provisioned setup  
- Seamless integration with Entra ID  
- Consistent security baseline application  

---

## 1.2 Autopilot Deployment Types

| Deployment Type | Description |
|-----------------|-------------|
| **User-driven** | User signs in and device configures automatically |
| **Pre-provisioned (White Glove)** | IT prepares device before handing to user |
| **Self-deploying** | No user interaction; kiosk/shared devices |
| **Hybrid Join** | Device joins on-prem AD + Entra ID |

---

# 2. Hardware Hash Collection

## 2.1 Collect Hardware Hash (Recommended Method)

Run on the device:

```powershell
Install-Script Get-WindowsAutopilotInfo
Get-WindowsAutopilotInfo -Online
```

This automatically uploads the hash to Intune.

---

## 2.2 Manual CSV Upload

```
Intune Admin Center → Devices → Windows → Windows Enrollment → Devices → Import
```

Upload the CSV file containing:
- Device serial number  
- Hardware hash  
- Manufacturer  
- Model  

---

# 3. Autopilot Profile Configuration

## 3.1 Create Autopilot Profile

```
Intune Admin Center → Devices → Windows → Windows Enrollment → Deployment Profiles → Create Profile
```

Profile settings include:
- Deployment mode (User-driven / Self-deploying / Pre-provisioned)  
- Skip privacy settings  
- Skip EULA  
- Automatically enrol in Intune  
- Language/region settings  
- Device naming template  

---

## 3.2 Assign Autopilot Profile

Assign to:
- Device group  
- User group  
- All Autopilot devices  

---

# 4. Enrollment Status Page (ESP)

ESP ensures devices complete:
- App installation  
- Policy application  
- Security baseline configuration  

Before the user reaches the desktop.

Configure ESP:

```
Intune Admin Center → Devices → Windows → Enrollment Status Page
```

Recommended:
- Block device use until required apps install  
- Show installation progress  
- Allow troubleshooting options for IT  

---

# 5. Pre-Provisioning (White Glove)

Used when IT prepares devices before handing them to users.

## 5.1 Steps

1. Boot device  
2. Press **Windows key + Ctrl + Shift + F3**  
3. Autopilot provisioning begins  
4. Device installs apps and policies  
5. IT seals the device  
6. User receives device ready-to-use  

---

# 6. Hybrid Autopilot Deployment

Used when:
- On-prem AD required  
- Legacy apps need domain join  

Requirements:
- Entra Connect  
- Intune Connector for Active Directory  
- Hybrid Autopilot profile  

---

# 7. Troubleshooting Autopilot

## Issue 1 — Device not showing in Autopilot

### Causes
- Hash not uploaded  
- Incorrect CSV format  

### Fix
- Re-run `Get-WindowsAutopilotInfo`  
- Validate CSV  

---

## Issue 2 — Autopilot profile not applying

### Causes
- Device not assigned  
- Sync delay  

### Fix
- Assign profile  
- Wait 5–30 minutes  

---

## Issue 3 — ESP stuck

### Causes
- Required app failing  
- Network restrictions  

### Fix
- Review ESP logs  
- Check app installation status  

---

## Issue 4 — Hybrid join failing

### Causes
- Entra Connect misconfiguration  
- Connector offline  

### Fix
- Check Entra Connect health  
- Restart connector service  

---

# 8. Verification Checklist

| Task | Completed |
|------|-----------|
| Hardware hash uploaded | ✔ |
| Autopilot profile assigned | ✔ |
| ESP configured | ✔ |
| Device enrolled in Intune | ✔ |
| Apps installed | ✔ |
| Policies applied | ✔ |
| Device marked compliant | ✔ |
| Conditional Access validated | ✔ |

---

# 9. Best Practices

- Use Autopilot for all new devices  
- Standardize Autopilot profiles  
- Require compliance before access  
- Use ESP to block desktop until ready  
- Document Autopilot workflows  
- Use dynamic groups for Autopilot devices  
- Test profiles before production rollout  

---

# References

- Microsoft Learn — Windows Autopilot  
- Microsoft Learn — Intune Device Enrollment  
- Microsoft Learn — Autopilot Troubleshooting  

### **03‑Device‑Compliance‑Policies.md**

with a full workflow diagram and Intune‑specific structure.
