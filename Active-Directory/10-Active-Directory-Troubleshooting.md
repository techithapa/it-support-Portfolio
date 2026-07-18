# Active Directory Troubleshooting

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 10 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Common AD Problems  
- Environment  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Verify Domain Controller Health  
- Step 2 – Check DNS Health  
- Step 3 – Validate AD Replication  
- Step 4 – Test Authentication & Secure Channel  
- Step 5 – Review Event Logs  
- Step 6 – Check SYSVOL & Netlogon Health  
- Step 7 – Troubleshoot Group Policy Issues  
- Step 8 – Troubleshoot Domain Join Issues  
- Step 9 – Advanced AD Troubleshooting  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory is the backbone of identity, authentication, and authorization in Windows enterprise environments. When AD experiences issues, users may face login failures, GPO problems, missing resources, or domain controller outages.

This document provides a structured troubleshooting guide for diagnosing and resolving common Active Directory issues.

---

# Common AD Problems

- Domain controller not responding  
- DNS misconfiguration  
- Replication failures  
- Group Policy not applying  
- Secure channel trust failures  
- SYSVOL not replicating  
- Kerberos authentication issues  
- Domain join failures  
- Time synchronization issues  
- AD database corruption  

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Management Tools | ADUC, GPMC, PowerShell, RSAT |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Tools Used

- **dcdiag**  
- **repadmin**  
- **nltest**  
- **PowerShell AD Module**  
- **Event Viewer**  
- **Group Policy Management Console**  
- **DNS Manager**  

---

# Troubleshooting Workflow

```
         Active Directory Issue
                 │
                 ▼
     Check Domain Controller Health (dcdiag)
                 │
                 ▼
     Validate DNS Configuration & Records
                 │
                 ▼
     Test AD Replication (repadmin)
                 │
                 ▼
     Verify Authentication & Secure Channel
                 │
                 ▼
     Review Event Logs (Directory Service, DNS, System)
                 │
                 ▼
     Check SYSVOL & Netlogon Status
                 │
                 ▼
     Troubleshoot GPO or Domain Join Issues
                 │
                 ▼
             Apply Fixes
                 │
                 ▼
            Verify AD Health
```

---

# Step 1 – Verify Domain Controller Health

Run:

```powershell
dcdiag /v
```

Check:

- Services running  
- DNS tests  
- Advertising status  
- SYSVOL availability  

---

# Step 2 – Check DNS Health

AD relies heavily on DNS.

### Validate DNS records

```powershell
dcdiag /test:dns
```

### Check SRV records

```
DNS Manager → _msdcs.domain.com
```

### Test name resolution

```powershell
nslookup domain.com
nslookup dc01.domain.com
```

---

# Step 3 – Validate AD Replication

### Summary

```powershell
repadmin /replsummary
```

### Check replication failures

```powershell
repadmin /showrepl
```

### Force replication

```powershell
repadmin /syncall /AeD
```

---

# Step 4 – Test Authentication & Secure Channel

### Test secure channel

```powershell
nltest /sc_verify:domain.com
```

### Repair secure channel (client)

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

---

# Step 5 – Review Event Logs

### On Domain Controller

```
Event Viewer → Windows Logs → System
Event Viewer → Applications and Services Logs → Directory Service
Event Viewer → DNS Server
Event Viewer → File Replication Service / DFSR
```

Look for:

- Replication errors  
- DNS failures  
- Kerberos issues  
- SYSVOL/DFSR errors  

---

# Step 6 – Check SYSVOL & Netlogon Health

### Check SYSVOL

```powershell
dcdiag /test:sysvolcheck
```

### Check Netlogon

```powershell
dcdiag /test:netlogons
```

### Check DFSR (Windows Server 2008+)

```powershell
dfsrdiag replicationstate
```

---

# Step 7 – Troubleshoot Group Policy Issues

### Check GPO application

```powershell
gpresult /r
gpresult /h report.html
```

### Check GPO health

```powershell
dcdiag /test:advertising
```

### Check SYSVOL replication

```powershell
repadmin /showrepl
```

---

# Step 8 – Troubleshoot Domain Join Issues

### Check DNS

```powershell
nslookup domain.com
```

### Check domain join status

```powershell
nltest /dsgetdc:domain.com
```

### Join domain

```powershell
Add-Computer -DomainName "domain.com" -Credential domain\admin -Restart
```

---

# Step 9 – Advanced AD Troubleshooting

## Check AD Database Health

```powershell
ntdsutil "activate instance ntds" "files" "info" quit quit
```

## Check replication metadata

```powershell
repadmin /showobjmeta
```

## Check Kerberos tickets

```powershell
klist
```

## Reset computer account

```powershell
Reset-ADComputer PC-001
```

---

# Verification

Confirm:

- Domain controllers healthy  
- DNS resolving correctly  
- Replication successful  
- SYSVOL and Netlogon available  
- GPOs applying correctly  
- Authentication working  
- No errors in event logs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| GPO not applying | SYSVOL or replication issue |
| Domain join fails | DNS misconfiguration |
| Trust relationship failed | Secure channel broken |
| Replication errors | Network or DC failure |
| Kerberos errors | Time sync issues |
| Missing SYSVOL | DFSR or FRS failure |

---

# Preventive Best Practices

- Monitor DC health regularly  
- Ensure DNS is correctly configured  
- Keep DCs synchronized with NTP  
- Avoid multi-master conflicts  
- Use AD Sites & Services correctly  
- Document AD topology  
- Regularly check replication health  

---

# Escalation Criteria

Escalate when:

- Multiple DCs fail health checks  
- Replication broken across sites  
- SYSVOL missing or corrupted  
- AD database corruption suspected  
- Kerberos authentication failing domain-wide  
- Domain join fails across multiple devices  

---

# References

- Microsoft Learn – Active Directory  
- Microsoft Learn – DNS for AD  
- Microsoft Learn – Group Policy  
- Microsoft Learn – PowerShell AD Module  
