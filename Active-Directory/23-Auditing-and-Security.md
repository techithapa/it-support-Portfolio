# **Document 23 — Auditing and Security (Active Directory)**  
> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 23 — Intermediate Difficulty  
> *Comprehensive auditing and security framework for enterprise AD environments.*

---

# **Table of Contents**

- Overview  
- Why Auditing & Security Matter  
- Environment  
- Tools Used  
- Core Security Concepts  
- Step 1 – Enable Directory Service Auditing  
- Step 2 – Configure Advanced Audit Policies  
- Step 3 – Audit Authentication (Kerberos, NTLM, Logons)  
- Step 4 – Audit Group Membership & Privileged Access  
- Step 5 – Audit GPO Changes  
- Step 6 – Audit AD Object Changes  
- Step 7 – Audit Domain Controller Security  
- Step 8 – Audit File Access & NTFS Permissions  
- Step 9 – Monitor AD with SIEM / Defender for Identity  
- Step 10 – Detect Common Attack Techniques  
- Step 11 – Troubleshooting Audit Issues  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# **Overview**

Auditing and security in Active Directory ensure visibility, accountability, and protection against:

- Unauthorized access  
- Privilege escalation  
- Insider threats  
- Credential theft  
- Lateral movement  
- Domain compromise  

This document provides a complete auditing and security framework for AD environments.

---

# **Why Auditing & Security Matter**

Without auditing, you cannot detect:

- Who accessed sensitive data  
- Who changed group memberships  
- Who modified GPOs  
- Who attempted unauthorized logins  
- Who escalated privileges  
- Who performed DCSync or DCShadow attacks  

Auditing is essential for:

- Compliance (ISO 27001, SOC2, PCI-DSS)  
- Forensics  
- Incident response  
- Security monitoring  

---

# **Environment**

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Security Tools | SIEM, Defender for Identity, LAPS |
| Logging | Windows Event Logs, Sysmon |
| Identity | On-prem AD / Hybrid Entra ID |

---

# **Tools Used**

- Group Policy Management  
- Event Viewer  
- PowerShell  
- Sysmon  
- Defender for Identity  
- SIEM (Sentinel, Splunk, QRadar)  
- AD ACL auditing tools  

---

# **Core Security Concepts**

| Concept | Description |
|---------|-------------|
| **Auditing** | Tracking changes and access |
| **Monitoring** | Real-time alerting |
| **Hardening** | Reducing attack surface |
| **Least Privilege** | Minimal permissions |
| **Zero Trust** | Continuous verification |

---

# **Step 1 – Enable Directory Service Auditing**

### GPO Path

```
Computer Configuration → Policies → Windows Settings → Security Settings → Advanced Audit Policy Configuration → Audit Policies → Directory Service Access
```

Enable:

- Success  
- Failure  

### PowerShell

```powershell
auditpol /set /subcategory:"Directory Service Changes" /success:enable /failure:enable
```

---

# **Step 2 – Configure Advanced Audit Policies**

Enable:

- **Account Logon**  
- **Logon/Logoff**  
- **Object Access**  
- **Policy Change**  
- **Privilege Use**  
- **System Events**  

### PowerShell

```powershell
auditpol /set /category:"Account Logon" /success:enable /failure:enable
```

---

# **Step 3 – Audit Authentication (Kerberos, NTLM, Logons)**

### Key Events

| Event ID | Description |
|----------|-------------|
| **4768** | Kerberos TGT request |
| **4769** | Kerberos service ticket |
| **4771** | Kerberos pre-auth failure |
| **4624** | Successful logon |
| **4625** | Failed logon |
| **4776** | NTLM authentication |

### Check Kerberos tickets

```powershell
klist
```

---

# **Step 4 – Audit Group Membership & Privileged Access**

Monitor changes to:

- Domain Admins  
- Enterprise Admins  
- Schema Admins  
- Administrators  
- Account Operators  

### Event IDs

| Event ID | Description |
|----------|-------------|
| **4728** | Member added to global group |
| **4729** | Member removed |
| **4732** | Member added to local group |
| **4733** | Member removed |

### PowerShell

```powershell
Get-ADGroupMember "Domain Admins"
```

---

# **Step 5 – Audit GPO Changes**

### Event IDs

| Event ID | Description |
|----------|-------------|
| **5136** | GPO modified |
| **5137** | GPO created |
| **5141** | GPO deleted |

### Check GPO permissions

```powershell
Get-GPO -All | Select DisplayName,Owner
```

---

# **Step 6 – Audit AD Object Changes**

### Event IDs

| Event ID | Description |
|----------|-------------|
| **5136** | Object modified |
| **5137** | Object created |
| **5139** | Object moved |
| **5141** | Object deleted |

### Enable AD Recycle Bin

```powershell
Enable-ADOptionalFeature -Identity 'Recycle Bin Feature' -Scope ForestOrConfigurationSet -Target domain.com
```

---

# **Step 7 – Audit Domain Controller Security**

Monitor:

- Service failures  
- Replication issues  
- DNS failures  
- SYSVOL changes  
- Kerberos failures  

### Key commands

```powershell
dcdiag /v
repadmin /replsummary
dfsrdiag replicationstate
```

---

# **Step 8 – Audit File Access & NTFS Permissions**

### Enable Object Access auditing

```
GPO → Security Settings → Advanced Audit Policy → Object Access
```

### Event IDs

| Event ID | Description |
|----------|-------------|
| **4663** | File access attempt |
| **4656** | Handle requested |
| **4670** | Permission changed |

### Check NTFS permissions

```powershell
icacls "D:\Finance"
```

---

# **Step 9 – Monitor AD with SIEM / Defender for Identity**

### Defender for Identity detects:

- Pass‑the‑Hash  
- Pass‑the‑Ticket  
- Kerberoasting  
- DCSync  
- DCShadow  
- Lateral movement  
- Reconnaissance  

### SIEM should ingest:

- Security logs  
- Directory Service logs  
- DNS logs  
- Sysmon logs  

---

# **Step 10 – Detect Common Attack Techniques**

| Attack | Detection |
|--------|-----------|
| **Kerberoasting** | High volume 4769 events |
| **DCSync** | 4662 with DS-Replication-Get-Changes |
| **Pass‑the‑Hash** | NTLM 4776 anomalies |
| **Golden Ticket** | Long‑lived TGTs |
| **DCShadow** | Unauthorized replication changes |
| **Lateral Movement** | 4624/4625 patterns |

---

# **Step 11 – Troubleshooting Audit Issues**

### Common checks:

- GPO not applying  
- DC replication delays  
- Event logs full  
- Incorrect audit policy  
- SIEM ingestion failures  

### Useful commands

```powershell
gpresult /r
auditpol /get /category:*
Get-EventLog -LogName Security -Newest 20
```

---

# **Verification**

Confirm:

- Audit policies applied  
- Events logged correctly  
- SIEM receiving logs  
- Defender for Identity alerts working  
- Privileged group changes monitored  
- Authentication logs complete  

---

# **Common Issues**

| Issue | Cause |
|-------|-------|
| Missing audit logs | GPO not applied |
| No Kerberos events | Time sync or DNS issue |
| SIEM missing logs | Forwarder misconfiguration |
| Privilege changes unnoticed | Audit policy disabled |
| Excessive noise | Over‑auditing |

---

# **Preventive Best Practices**

- Use tiered administration  
- Enforce MFA for admins  
- Monitor privileged groups  
- Use LAPS for local admin passwords  
- Enable AD Recycle Bin  
- Deploy Defender for Identity  
- Regularly review audit logs  
- Document audit policies  

---

# **Escalation Criteria**

Escalate when:

- DCSync or DCShadow detected  
- Privileged accounts modified unexpectedly  
- Kerberos failures domain‑wide  
- SIEM ingestion stops  
- Domain controllers show suspicious activity  
- Evidence of lateral movement  

---

# **References**

- Microsoft Learn – AD Security  
- Microsoft Learn – Advanced Audit Policies  
- Microsoft Learn – Defender for Identity  
- Microsoft Learn – Kerberos & NTLM  
