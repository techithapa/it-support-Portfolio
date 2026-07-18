# Kerberos & NTLM Authentication (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 17 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Is Kerberos?  
- What Is NTLM?  
- Differences Between Kerberos & NTLM  
- Authentication Flow (Kerberos & NTLM)  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Validate Time Synchronization  
- Step 2 – Check DNS & SPN Records  
- Step 3 – Troubleshoot Kerberos Failures  
- Step 4 – Troubleshoot NTLM Authentication  
- Step 5 – Check Secure Channel & Trust  
- Step 6 – Review Event Logs  
- Step 7 – Advanced Authentication Diagnostics  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Kerberos and NTLM are the two primary authentication protocols used in Active Directory. Kerberos is the default and preferred protocol due to its security, efficiency, and mutual authentication. NTLM is older and used only when Kerberos cannot be used.

This document provides a structured guide for understanding, validating, and troubleshooting Kerberos and NTLM authentication issues.

---

# What Is Kerberos?

Kerberos is a **ticket-based authentication protocol** that provides:

- Mutual authentication  
- Strong encryption  
- Fast authentication  
- Reduced reliance on domain controllers  

Kerberos requires:

- Correct DNS  
- Time synchronization  
- Valid Service Principal Names (SPNs)  
- Healthy domain controllers  

---

# What Is NTLM?

NTLM is a **challenge-response authentication protocol** used when Kerberos is unavailable.

NTLM is used when:

- The client cannot reach a domain controller  
- SPNs are missing or incorrect  
- The application does not support Kerberos  
- Cross-domain authentication fails  
- Local authentication is required  

NTLM is less secure and should be minimized.

---

# Differences Between Kerberos & NTLM

| Feature | Kerberos | NTLM |
|---------|----------|------|
| Security | Strong | Weak |
| Speed | Fast | Slower |
| Mutual Authentication | Yes | No |
| Requires SPNs | Yes | No |
| Requires Time Sync | Yes | No |
| Default Protocol | Yes | Fallback |

---

# Authentication Flow

### Kerberos Flow

```
Client → DC (KDC): Request TGT
DC → Client: TGT issued
Client → DC: Request service ticket
DC → Client: Service ticket issued
Client → Server: Present service ticket
Server → Client: Access granted
```

### NTLM Flow

```
Client → Server: Authentication request
Server → Client: Challenge
Client → Server: Response
Server → DC: Validate response
DC → Server: Result
```

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Tools | klist, nltest, setspn, dcdiag |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Validate Kerberos authentication  
- Troubleshoot NTLM fallback  
- Fix SPN issues  
- Check time synchronization  
- Review authentication logs  
- Test secure channel  
- Validate DNS configuration  

---

# Tools Used

- **klist**  
- **nltest**  
- **setspn**  
- **dcdiag**  
- **Event Viewer**  
- **PowerShell AD Module**  

---

# Troubleshooting Workflow

```
     Kerberos/NTLM Issue Detected
                 │
                 ▼
       Check Time Synchronization
                 │
                 ▼
       Validate DNS & SPN Records
                 │
                 ▼
     Test Kerberos Tickets (klist)
                 │
                 ▼
     Check NTLM Fallback Conditions
                 │
                 ▼
     Review Event Logs (Security, System)
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Test Authentication
```

---

# Step 1 – Validate Time Synchronization

Kerberos requires time sync within **5 minutes**.

### Check time source

```powershell
w32tm /query /source
```

### Check time status

```powershell
w32tm /query /status
```

### Sync time

```powershell
w32tm /resync
```

---

# Step 2 – Check DNS & SPN Records

### Validate DNS

```powershell
nslookup dc01.domain.com
```

### Check SPNs

```powershell
setspn -L server01
```

### Add missing SPN

```powershell
setspn -A HTTP/server01.domain.com server01
```

---

# Step 3 – Troubleshoot Kerberos Failures

### Check Kerberos tickets

```powershell
klist
```

### Purge tickets

```powershell
klist purge
```

### Check event logs

```
Event Viewer → System → Kerberos
Event Viewer → Security → Event ID 4769, 4771
```

### Common causes

- Incorrect SPNs  
- DNS misconfiguration  
- Time sync issues  
- Duplicate SPNs  
- Service running under wrong account  

---

# Step 4 – Troubleshoot NTLM Authentication

### Check NTLM usage

```powershell
nltest /trusted_domains
```

### Check secure channel

```powershell
nltest /sc_verify:domain.com
```

### Repair secure channel

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

### Common causes

- Kerberos failure → NTLM fallback  
- Application does not support Kerberos  
- SPN missing  
- DC unreachable  

---

# Step 5 – Check Secure Channel & Trust

### Test secure channel

```powershell
nltest /sc_verify:domain.com
```

### Repair secure channel

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

---

# Step 6 – Review Event Logs

### Kerberos logs

```
Event Viewer → System → Kerberos
```

### NTLM logs

```
Event Viewer → Security → Event ID 4624, 4625
```

### Domain controller logs

```
Event Viewer → Directory Service
Event Viewer → DNS Server
```

---

# Step 7 – Advanced Authentication Diagnostics

## Check for duplicate SPNs

```powershell
setspn -X
```

## Check Kerberos encryption types

```powershell
klist tickets
```

## Check domain controller health

```powershell
dcdiag /v
```

## Check replication

```powershell
repadmin /replsummary
```

---

# Verification

Confirm:

- Kerberos tickets issued correctly  
- No NTLM fallback unless required  
- SPNs correct and unique  
- DNS resolving correctly  
- Time synchronization healthy  
- Secure channel working  
- No authentication errors in event logs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| NTLM fallback | Missing SPN or DNS issue |
| Kerberos failure | Time sync or SPN issue |
| Trust relationship failed | Secure channel broken |
| Duplicate SPNs | Multiple services using same identity |
| Authentication slow | NTLM instead of Kerberos |

---

# Preventive Best Practices

- Ensure correct SPNs for all services  
- Monitor Kerberos event logs  
- Keep time synchronized across all DCs  
- Avoid duplicate service accounts  
- Use Kerberos wherever possible  
- Document service accounts and SPNs  
- Regularly check DNS health  

---

# Escalation Criteria

Escalate when:

- Kerberos fails domain-wide  
- NTLM fallback occurs for multiple services  
- Duplicate SPNs cannot be resolved  
- Secure channel repeatedly breaks  
- Authentication failures impact critical apps  
- Time sync issues affect multiple DCs  

---

# References

- Microsoft Learn – Kerberos Authentication  
- Microsoft Learn – NTLM Authentication  
- Microsoft Learn – SPN Management  
- Microsoft Learn – PowerShell AD Module  
