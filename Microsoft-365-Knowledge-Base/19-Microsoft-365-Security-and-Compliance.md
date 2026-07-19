
# Microsoft 365 Knowledge Base  
## 19 — Microsoft 365 Security and Compliance

---

## Overview

Microsoft 365 Security & Compliance provides a unified framework for protecting data, identities, devices, and applications across the Microsoft cloud. It combines Microsoft Purview (compliance), Microsoft Defender (security), and Microsoft Entra (identity) to deliver a Zero Trust security model.

This document covers:
- Security architecture  
- Compliance capabilities  
- Data loss prevention (DLP)  
- Information protection  
- Insider risk management  
- eDiscovery  
- Audit and logging  
- Secure Score  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Zero Trust** | “Never trust, always verify” security model |
| **Microsoft Purview** | Compliance, governance, and data protection |
| **Microsoft Defender** | Threat protection across endpoints, email, identity, and cloud |
| **Microsoft Entra ID** | Identity and access management |
| **DLP** | Prevents sensitive data leakage |
| **Sensitivity Labels** | Classify and protect data |
| **eDiscovery** | Legal search and investigation tools |
| **Audit Logs** | Track user and admin activity |

---

## Tools Used

- Microsoft Purview Compliance Portal  
- Microsoft Defender Portal  
- Microsoft Entra Admin Center  
- Microsoft 365 Admin Center  
- PowerShell (Graph, Security & Compliance)  

---

# 1. Microsoft 365 Security Architecture

## 1.1 Core Components

- **Identity Security** — MFA, Conditional Access, Identity Protection  
- **Threat Protection** — Defender for Endpoint, Office 365, Cloud Apps  
- **Data Protection** — DLP, sensitivity labels, encryption  
- **Compliance** — eDiscovery, retention, auditing  
- **Device Security** — Intune, endpoint hardening  

---

## 1.2 Zero Trust Principles

- Verify explicitly  
- Use least privilege access  
- Assume breach  

---

# 2. Microsoft Purview Compliance Portal

Purview provides governance, compliance, and data protection tools.

## 2.1 Key Purview Solutions

- Data Loss Prevention (DLP)  
- Information Protection (Sensitivity Labels)  
- Insider Risk Management  
- eDiscovery (Standard & Premium)  
- Audit  
- Compliance Manager  
- Communication Compliance  

---

# 3. Data Loss Prevention (DLP)

DLP prevents sensitive data from being shared inappropriately.

## 3.1 DLP Locations

- Exchange Online  
- SharePoint Online  
- OneDrive  
- Teams chat  
- Endpoint devices  

---

## 3.2 Create DLP Policy

```
Purview → Data Loss Prevention → Policies → Create Policy
```

Choose templates:
- Financial data  
- Health data  
- Personal data  
- Custom sensitive info types  

---

## 3.3 DLP Actions

- Block sharing  
- Warn user  
- Audit only  
- Encrypt content  

---

# 4. Information Protection (Sensitivity Labels)

Sensitivity labels classify and protect data.

## 4.1 Label Capabilities

- Encryption  
- Content marking (headers/footers)  
- Watermarks  
- Access restrictions  
- Auto-labeling  

---

## 4.2 Create Sensitivity Label

```
Purview → Information Protection → Labels → Create Label
```

---

## 4.3 Publish Label

```
Purview → Information Protection → Label Policies → Publish
```

---

# 5. Insider Risk Management

Detects risky user behavior such as:
- Data exfiltration  
- Privilege misuse  
- Security violations  
- HR-related risks  

---

## 5.1 Configure Insider Risk Policies

```
Purview → Insider Risk Management → Policies → Create Policy
```

---

# 6. eDiscovery

## 6.1 eDiscovery Standard

Used for:
- Basic legal searches  
- Exporting mailbox and SharePoint data  

---

## 6.2 eDiscovery Premium

Provides:
- Custodian management  
- Legal hold  
- Advanced search  
- Review sets  
- Analytics  

---

## 6.3 Create eDiscovery Case

```
Purview → eDiscovery → Create Case
```

---

# 7. Audit & Logging

## 7.1 Enable Audit Logging

Audit is enabled by default.

### View Logs
```
Purview → Audit → Search
```

---

## 7.2 Common Audit Events

- User sign-ins  
- File access  
- Admin actions  
- Mailbox activities  
- Sharing events  

---

# 8. Secure Score

Secure Score measures your security posture across Microsoft 365.

## 8.1 Access Secure Score

```
Defender Portal → Secure Score
```

---

## 8.2 Improve Secure Score

- Enable MFA  
- Block legacy authentication  
- Configure DLP  
- Apply sensitivity labels  
- Enable Safe Links & Safe Attachments  
- Harden endpoints  

---

# 9. Compliance Manager

Provides:
- Compliance assessments  
- Improvement actions  
- Regulatory templates (GDPR, ISO, SOC2)  

---

# 10. Troubleshooting Security & Compliance

## Issue 1 — DLP not applying

### Causes
- Policy not published  
- Unsupported location  
- Sync delay  

### Fix
- Publish policy  
- Check supported workloads  
- Wait up to 24 hours  

---

## Issue 2 — Sensitivity labels not appearing

### Causes
- Label not published  
- User not assigned  
- Client not updated  

### Fix
- Publish label  
- Assign policy  
- Update Office apps  

---

## Issue 3 — eDiscovery search missing results

### Causes
- Indexing delay  
- Incorrect search query  
- Location not included  

### Fix
- Wait for indexing  
- Adjust query  
- Add missing locations  

---

## Issue 4 — Audit logs not showing events

### Causes
- Delay in log ingestion  
- Incorrect filters  

### Fix
- Wait 30–60 minutes  
- Adjust search filters  

---

# 11. Verification Checklist

- DLP policies active  
- Sensitivity labels applied  
- Insider risk policies configured  
- eDiscovery cases functional  
- Audit logs searchable  
- Secure Score improving  
- No compliance errors  

---

# 12. Best Practices

- Enforce MFA for all users  
- Use Conditional Access for Zero Trust  
- Apply sensitivity labels to all data  
- Use DLP for email, Teams, and endpoints  
- Monitor Secure Score weekly  
- Use eDiscovery Premium for legal matters  
- Review audit logs regularly  
- Document compliance policies  

---

# References

- Microsoft Learn — Microsoft Purview  
- Microsoft Learn — Data Loss Prevention  
- Microsoft Learn — Information Protection  
- Microsoft Learn — eDiscovery  
- Microsoft Learn — Secure Score  
