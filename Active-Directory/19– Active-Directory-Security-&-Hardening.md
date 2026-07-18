# Active Directory Security & Hardening

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 19 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Why AD Security & Hardening Matter  
- Core Security Principles  
- Environment  
- Common Tasks  
- Tools Used  
- Threat Landscape  
- Troubleshooting Workflow  
- Step 1 – Secure Domain Controllers  
- Step 2 – Harden Authentication (Kerberos, NTLM, LAPS)  
- Step 3 – Protect Privileged Accounts  
- Step 4 – Secure Group Policy  
- Step 5 – Harden AD Infrastructure (DNS, Replication, SYSVOL)  
- Step 6 – Implement Tiered Administration Model  
- Step 7 – Monitor & Audit AD Security  
- Step 8 – Advanced Hardening Techniques  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory is the backbone of enterprise identity. If compromised, attackers gain access to:

- Domain controllers  
- User credentials  
- Sensitive data  
- Entire network infrastructure  

Hardening AD is essential to prevent credential theft, privilege escalation, lateral movement, and domain compromise.

This document provides a structured guide for securing and hardening Active Directory environments.

---

# Why AD Security & Hardening Matter

A compromised AD environment leads to:

- Full domain takeover  
- Ransomware propagation  
- Credential theft (Kerberoasting, Pass‑the‑Hash)  
- GPO abuse  
- Persistence mechanisms (Golden Ticket, DCSync)  

Hardening AD reduces attack surface and strengthens identity security.

---

# Core Security Principles

| Principle | Description |
|-----------|-------------|
| **Least Privilege** | Users only get required permissions |
| **Tiered Administration** | Separate admin levels |
| **Defense in Depth** | Multiple layers of protection |
| **Zero Trust** | Never trust, always verify |
| **Monitoring & Auditing** | Detect anomalies early |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Security Tools | LAPS, Defender for Identity, SIEM |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Secure domain controllers  
- Harden authentication protocols  
- Protect privileged accounts  
- Secure GPOs  
- Implement LAPS  
- Audit AD activity  
- Detect lateral movement  
- Reduce attack surface  

---

# Tools Used

- Group Policy Management  
- LAPS / Windows LAPS  
- PowerShell  
- Event Viewer  
- Defender for Identity  
- SIEM (Sentinel, Splunk, etc.)  
- AD ACL auditing tools  

---

# Threat Landscape

Common AD attack techniques:

- **Kerberoasting**  
- **Pass‑the‑Hash**  
- **Pass‑the‑Ticket**  
- **DCSync / DCShadow**  
- **Golden Ticket**  
- **Silver Ticket**  
- **Credential dumping (LSASS)**  
- **GPO abuse**  
- **Privilege escalation via ACLs**  

---

# Troubleshooting Workflow

```
         AD Security Issue Detected
                 │
                 ▼
       Check Domain Controller Security
                 │
                 ▼
       Validate Authentication Hardening
                 │
                 ▼
     Review Privileged Accounts & Groups
                 │
                 ▼
     Audit GPO Permissions & SYSVOL
                 │
                 ▼
     Check AD ACLs & Delegation
                 │
                 ▼
     Review Security Logs & Alerts
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Re-run Security Checks
```

---

# Step 1 – Secure Domain Controllers

### Harden DCs

- No internet browsing  
- No third‑party apps  
- No user logins  
- Physical security enforced  
- Firewall enabled  
- Only required roles installed  

### Protect DC network access

```powershell
Set-NetFirewallProfile -Profile Domain -Enabled True
```

### Ensure DCs are patched

```powershell
Get-WindowsUpdateLog
```

---

# Step 2 – Harden Authentication (Kerberos, NTLM, LAPS)

### Disable NTLM where possible

```
GPO → Security Options → Network Security: Restrict NTLM
```

### Enforce Kerberos

- Correct SPNs  
- Time synchronization  
- Strong encryption types  

### Deploy Windows LAPS

```powershell
Install-LapsADMX
```

Configure:

- Unique local admin passwords  
- Automatic rotation  
- Secure storage in AD  

---

# Step 3 – Protect Privileged Accounts

### Remove users from privileged groups

- Domain Admins  
- Enterprise Admins  
- Schema Admins  
- Administrators  
- Account Operators  

### Enforce Privileged Access Workstations (PAWs)

Admins must use hardened devices.

### Enable Smart Card / MFA for admins

```powershell
Set-ADUser jsmith -SmartcardLogonRequired $true
```

---

# Step 4 – Secure Group Policy

### Protect GPOs from tampering

- Limit GPO editing rights  
- Audit GPO changes  
- Secure SYSVOL permissions  

### Check GPO permissions

```powershell
Get-GPO -All | Select DisplayName,Owner
```

---

# Step 5 – Harden AD Infrastructure (DNS, Replication, SYSVOL)

### Secure DNS

- Enable DNSSEC  
- Secure dynamic updates  
- Audit DNS changes  

### Secure SYSVOL

- Ensure DFSR healthy  
- Remove unauthorized scripts  

### Secure replication

```powershell
repadmin /replsummary
```

---

# Step 6 – Implement Tiered Administration Model

### Tier 0

- Domain controllers  
- AD forest  
- PKI  
- ADFS  
- Entra Connect  

### Tier 1

- Servers  
- Applications  

### Tier 2

- Workstations  
- Users  

Admins must not cross tiers.

---

# Step 7 – Monitor & Audit AD Security

### Enable auditing

```powershell
auditpol /set /category:"DS Access" /success:enable /failure:enable
```

### Monitor:

- Logon failures  
- Privilege escalation  
- ACL changes  
- GPO modifications  
- DCSync attempts  

### Deploy Defender for Identity

Detects:

- Pass‑the‑Hash  
- Golden Ticket  
- Lateral movement  
- Reconnaissance  

---

# Step 8 – Advanced Hardening Techniques

### Protect LSASS

- Credential Guard  
- RunAsPPL  
- Disable WDigest  

### Block legacy protocols

- SMBv1  
- NTLMv1  
- LM authentication  

### Harden AD ACLs

```powershell
Get-ACL "AD:\CN=Users,DC=domain,DC=com"
```

### Implement ESAE (Enhanced Security Admin Environment)

Dedicated admin forest.

---

# Verification

Confirm:

- DCs hardened  
- Privileged accounts protected  
- Authentication secure  
- GPOs locked down  
- DNS secure  
- Replication healthy  
- No suspicious activity in logs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| Kerberoasting risk | Weak service account passwords |
| NTLM fallback | Missing SPNs |
| GPO tampering | Excessive permissions |
| DCSync attacks | Weak ACLs |
| Credential theft | LSASS unprotected |

---

# Preventive Best Practices

- Enforce MFA for admins  
- Use Windows LAPS  
- Remove unnecessary privileges  
- Harden DCs and PAWs  
- Monitor AD continuously  
- Patch DCs regularly  
- Disable legacy protocols  
- Document AD security model  

---

# Escalation Criteria

Escalate when:

- Domain compromise suspected  
- DCSync or Golden Ticket detected  
- Privileged accounts breached  
- DCs show signs of tampering  
- Kerberos failures domain‑wide  
- GPOs modified unexpectedly  

---

# References

- Microsoft Learn – AD Security  
- Microsoft Learn – LAPS  
- Microsoft Learn – Defender for Identity  
- Microsoft Learn – Kerberos & NTLM Hardening  
