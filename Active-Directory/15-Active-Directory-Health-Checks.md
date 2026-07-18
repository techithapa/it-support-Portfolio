# Active Directory Health Checks

> Enterprise Windows Troublesbling Knowledge Base  
> Active Directory Module 15 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Why Health Checks Matter  
- Core AD Health Components  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Check Domain Controller Health  
- Step 2 – Validate DNS Health  
- Step 3 – Verify AD Replication  
- Step 4 – Check SYSVOL & Netlogon  
- Step 5 – Validate Time Synchronization  
- Step 6 – Review Event Logs  
- Step 7 – Check Authentication & Secure Channel  
- Step 8 – Advanced AD Health Diagnostics  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Active Directory health checks ensure that domain controllers, replication, DNS, authentication, and Group Policy are functioning correctly. Regular health checks prevent outages, reduce troubleshooting time, and maintain a stable identity infrastructure.

This document provides a structured guide for performing AD health checks and diagnosing issues before they impact users.

---

# Why Health Checks Matter

Healthy AD ensures:

- Reliable authentication  
- Consistent replication  
- Correct DNS resolution  
- Proper GPO application  
- Stable domain join operations  
- Secure Kerberos ticketing  
- Accurate directory information  

Unhealthy AD leads to:

- Login failures  
- GPO issues  
- Missing objects  
- Replication delays  
- DNS failures  
- Trust relationship errors  

---

# Core AD Health Components

| Component | Purpose |
|-----------|---------|
| **Domain Controllers** | Authentication, replication, directory services |
| **DNS** | Locates DCs, supports Kerberos & LDAP |
| **Replication** | Synchronizes AD data |
| **SYSVOL/Netlogon** | Stores GPOs and scripts |
| **Time Sync** | Required for Kerberos |
| **Authentication** | Kerberos/NTLM operations |
| **Event Logs** | Diagnostic information |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| DNS | AD-integrated |
| Replication | Intra-site & inter-site |
| Tools | dcdiag, repadmin, PowerShell, Event Viewer |

---

# Common Tasks

- Run AD health diagnostics  
- Validate DNS configuration  
- Check replication status  
- Inspect SYSVOL/Netlogon  
- Review event logs  
- Validate Kerberos authentication  
- Check time synchronization  
- Identify failing domain controllers  

---

# Tools Used

- **dcdiag**  
- **repadmin**  
- **nltest**  
- **PowerShell AD Module**  
- **Event Viewer**  
- **DNS Manager**  
- **Active Directory Sites and Services**  

---

# Troubleshooting Workflow

```
         AD Health Check Initiated
                 │
                 ▼
       Check Domain Controller Health
                 │
                 ▼
       Validate DNS Configuration & SRV Records
                 │
                 ▼
     Test AD Replication (repadmin)
                 │
                 ▼
     Check SYSVOL & Netlogon Availability
                 │
                 ▼
     Validate Time Synchronization (Kerberos)
                 │
                 ▼
     Review Event Logs for Errors
                 │
                 ▼
     Test Authentication & Secure Channel
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Re-run Health Checks
```

---

# Step 1 – Check Domain Controller Health

### Run dcdiag

```powershell
dcdiag /v
```

Check:

- Services running  
- Advertising status  
- DNS tests  
- SYSVOL availability  
- Netlogon health  

### Check DC roles

```powershell
netdom query fsmo
```

---

# Step 2 – Validate DNS Health

### Check DNS tests

```powershell
dcdiag /test:dns
```

### Validate SRV records

```powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.domain.com
```

### Check DC A records

```powershell
nslookup dc01.domain.com
```

### Re-register DNS

```powershell
ipconfig /registerdns
```

---

# Step 3 – Verify AD Replication

### Replication summary

```powershell
repadmin /replsummary
```

### Check inbound/outbound replication

```powershell
repadmin /showrepl
```

### Force replication

```powershell
repadmin /syncall /AeD
```

---

# Step 4 – Check SYSVOL & Netlogon

### SYSVOL health

```powershell
dcdiag /test:sysvolcheck
```

### Netlogon health

```powershell
dcdiag /test:netlogons
```

### DFSR replication state

```powershell
dfsrdiag replicationstate
```

---

# Step 5 – Validate Time Synchronization

Kerberos requires time sync within **5 minutes**.

### Check time source

```powershell
w32tm /query /source
```

### Sync time

```powershell
w32tm /resync
```

### Configure external NTP (PDC Emulator)

```powershell
w32tm /config /manualpeerlist:"time.windows.com" /syncfromflags:manual /update
```

---

# Step 6 – Review Event Logs

### Key logs

```
Event Viewer → Directory Service
Event Viewer → DNS Server
Event Viewer → System
Event Viewer → DFS Replication
```

Look for:

- Replication errors  
- DNS failures  
- Kerberos issues  
- SYSVOL/DFSR errors  

---

# Step 7 – Check Authentication & Secure Channel

### Test secure channel

```powershell
nltest /sc_verify:domain.com
```

### Repair secure channel

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

### Check Kerberos tickets

```powershell
klist
```

---

# Step 8 – Advanced AD Health Diagnostics

## Check lingering objects

```powershell
repadmin /removelingeringobjects
```

## Check AD database health

```powershell
ntdsutil "activate instance ntds" "files" "info" quit quit
```

## Check replication metadata

```powershell
repadmin /showobjmeta dc01 "CN=UserName,OU=Users,DC=domain,DC=com"
```

## Check DFSR backlog

```powershell
dfsrdiag backlog /rgname:"Domain System Volume" /rfname:"SYSVOL" /smem:dc01 /rmem:dc02
```

---

# Verification

Confirm:

- All DCs healthy  
- DNS resolving correctly  
- Replication successful  
- SYSVOL and Netlogon available  
- Kerberos authentication working  
- No errors in event logs  
- Time synchronization correct  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| GPO not applying | SYSVOL or replication issue |
| Domain join fails | DNS misconfiguration |
| Kerberos failures | Time sync issues |
| DC unreachable | Network/firewall issue |
| Replication errors | DNS or topology issues |
| Missing SYSVOL | DFSR/FRS failure |

---

# Preventive Best Practices

- Run AD health checks monthly  
- Monitor DC event logs  
- Ensure DNS is correctly configured  
- Keep DCs synchronized with NTP  
- Use AD Sites & Services correctly  
- Avoid long DC outages  
- Document AD topology  
- Regularly check replication health  

---

# Escalation Criteria

Escalate when:

- Multiple DCs fail health checks  
- Replication broken across sites  
- SYSVOL missing or corrupted  
- Kerberos authentication failing domain-wide  
- AD database corruption suspected  
- DNS failures affecting multiple clients  

---

# References

- Microsoft Learn – Active Directory Health  
- Microsoft Learn – dcdiag  
- Microsoft Learn – repadmin  
- Microsoft Learn – DFS Replication  
- Microsoft Learn – Kerberos Authentication  
