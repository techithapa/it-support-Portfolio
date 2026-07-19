
# Microsoft 365 Knowledge Base  
## 18 — Microsoft Defender Basics

---

## Overview

Microsoft Defender is Microsoft’s unified security platform that protects identities, endpoints, email, applications, and cloud resources across Microsoft 365. Defender provides threat detection, automated investigation, and response capabilities essential for modern security operations.

This document covers:
- Defender components  
- Defender for Endpoint  
- Defender for Office 365  
- Defender for Identity  
- Defender for Cloud Apps  
- Alerts and investigations  
- Security policies  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Component | Description |
|----------|-------------|
| **Defender for Endpoint** | Endpoint protection, EDR, vulnerability management |
| **Defender for Office 365** | Email and collaboration protection |
| **Defender for Identity** | Identity-based threat detection |
| **Defender for Cloud Apps** | Cloud app security and CASB |
| **Secure Score** | Measures security posture |
| **Automated Investigation** | AI-driven threat analysis and remediation |
| **Attack Surface Reduction** | Hardening endpoints against threats |

---

## Tools Used

- Microsoft Defender Portal  
- Microsoft 365 Admin Center  
- Intune Admin Center  
- Entra Admin Center  
- PowerShell (Graph)  

---

# 1. Microsoft Defender Components

## 1.1 Defender for Endpoint

Provides:
- Antivirus  
- Endpoint Detection & Response (EDR)  
- Threat analytics  
- Vulnerability management  
- Device inventory  
- Attack surface reduction  

---

## 1.2 Defender for Office 365

Protects:
- Email  
- SharePoint  
- OneDrive  
- Teams  

Features:
- Safe Links  
- Safe Attachments  
- Anti-phishing  
- Anti-spam  
- Quarantine  

---

## 1.3 Defender for Identity

Monitors:
- On-prem Active Directory  
- Identity-based attacks  
- Lateral movement  
- Credential theft  

---

## 1.4 Defender for Cloud Apps

Provides:
- Cloud app discovery  
- OAuth app governance  
- Session controls  
- Data protection  
- Threat detection  

---

# 2. Defender for Endpoint

## 2.1 Onboarding Devices

### Windows
- Intune  
- Group Policy  
- Local script  
- Configuration Manager  

### macOS / Linux / Mobile
- Intune  
- Manual installation  

---

## 2.2 Endpoint Security Policies

Configure via Intune:
- Antivirus  
- Firewall  
- Disk encryption  
- Attack surface reduction  
- Device control (USB restrictions)  

---

## 2.3 Vulnerability Management

Includes:
- Software inventory  
- Weakness detection  
- Security recommendations  
- Exposure scoring  

---

# 3. Defender for Office 365

## 3.1 Safe Links

Protects users from malicious URLs.

### Enable Safe Links
```
Defender Portal → Email & Collaboration → Policies → Safe Links
```

---

## 3.2 Safe Attachments

Scans attachments in a sandbox environment.

### Enable Safe Attachments
```
Defender Portal → Email & Collaboration → Policies → Safe Attachments
```

---

## 3.3 Anti-Phishing Policies

Protects against:
- Impersonation  
- Spoofing  
- Credential theft  

---

## 3.4 Quarantine

Admins can:
- Release messages  
- Block senders  
- Review malware detections  

---

# 4. Defender for Identity

## 4.1 Deployment Requirements

- On-prem Active Directory  
- Lightweight sensor installed on domain controllers  

---

## 4.2 Detects

- Pass-the-hash  
- Pass-the-ticket  
- Golden Ticket attacks  
- Lateral movement  
- Suspicious authentication patterns  

---

# 5. Defender for Cloud Apps

## 5.1 Cloud App Discovery

Identifies:
- Shadow IT  
- Risky apps  
- Usage patterns  

---

## 5.2 App Governance

Controls:
- OAuth permissions  
- App access  
- Risky app behavior  

---

## 5.3 Session Controls

Allows:
- Conditional access session restrictions  
- Real-time monitoring  
- Blocking downloads  

---

# 6. Alerts & Investigations

## 6.1 Alert Types

- Malware  
- Phishing  
- Suspicious login  
- Endpoint compromise  
- Data exfiltration  
- Cloud app anomalies  

---

## 6.2 Automated Investigation

Defender automatically:
- Analyzes alerts  
- Correlates signals  
- Suggests remediation  
- Executes safe automated actions  

---

## 6.3 Manual Investigation

Admins can:
- Review alert details  
- Collect device logs  
- Run antivirus scans  
- Isolate devices  
- Suspend user accounts  

---

# 7. Security Policies

## 7.1 Anti-Malware Policies

Configure:
- Real-time protection  
- Cloud-delivered protection  
- Scheduled scans  

---

## 7.2 Attack Surface Reduction (ASR)

Examples:
- Block Office macros  
- Block executable content  
- Block credential theft  

---

## 7.3 Web Content Filtering

Categories:
- Adult content  
- Gambling  
- High-risk sites  
- Malware domains  

---

# 8. Troubleshooting

## Issue 1 — Device not reporting to Defender

### Causes
- Onboarding failure  
- Network restrictions  
- Policy conflict  

### Fix
- Re-onboard device  
- Check firewall/proxy  
- Review Intune policies  

---

## Issue 2 — Safe Links not working

### Causes
- Policy not applied  
- Legacy client  
- Incorrect configuration  

### Fix
- Reapply policy  
- Use supported Outlook client  

---

## Issue 3 — Alerts not triggering

### Causes
- Sensor not installed  
- Device not onboarded  
- Policy disabled  

### Fix
- Install sensor  
- Verify onboarding  
- Enable policies  

---

## Issue 4 — Email going to junk incorrectly

### Causes
- Aggressive anti-spam settings  
- Spoofing detection  
- Transport rules  

### Fix
- Adjust anti-spam policies  
- Add safe senders  
- Review mail flow rules  

---

# 9. Verification Checklist

- Devices onboarded  
- Safe Links and Safe Attachments enabled  
- Anti-phishing policies active  
- Alerts generating correctly  
- Endpoint security policies applied  
- Cloud app discovery functioning  
- No reporting gaps  

---

# 10. Best Practices

- Enable Safe Links and Safe Attachments  
- Use Defender for Endpoint with Intune  
- Enforce Attack Surface Reduction rules  
- Monitor Secure Score weekly  
- Use automated investigation and response  
- Block legacy authentication  
- Review risky users and sign-ins  
- Audit OAuth apps regularly  

---

# References

- Microsoft Learn — Microsoft Defender  
- Microsoft Learn — Defender for Endpoint  
- Microsoft Learn — Defender for Office 365  
- Microsoft Learn — Defender for Cloud Apps  
