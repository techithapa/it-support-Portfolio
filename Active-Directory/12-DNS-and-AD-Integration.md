# DNS and Active Directory Integration

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 12 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- Why DNS Is Critical for Active Directory  
- How AD Uses DNS  
- DNS Zones Required for AD  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Verify DNS Configuration  
- Step 2 – Check Required DNS Records  
- Step 3 – Validate DNS Zone Replication  
- Step 4 – Troubleshoot Name Resolution  
- Step 5 – Fix Domain Join & Authentication Issues  
- Step 6 – Manage DNS Scavenging & Aging  
- Step 7 – Advanced DNS & AD Diagnostics  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

DNS is the foundation of Active Directory. Without a healthy DNS infrastructure, domain controllers cannot communicate, clients cannot authenticate, Group Policies fail, and replication breaks.

This document provides a structured guide for understanding, configuring, and troubleshooting DNS in Active Directory environments.

---

# Why DNS Is Critical for Active Directory

Active Directory relies on DNS for:

- Locating domain controllers  
- Kerberos authentication  
- LDAP directory lookups  
- Group Policy application  
- Domain join operations  
- Replication between domain controllers  

If DNS fails, **Active Directory fails**.

---

# How AD Uses DNS

Active Directory automatically registers:

- **SRV records** (service locator records)  
- **A records** (host records)  
- **CNAME records** (alias records)  
- **Global Catalog records**  

Key SRV records include:

```
_ldap._tcp.dc._msdcs.domain.com
_kerberos._tcp.dc._msdcs.domain.com
```

---

# DNS Zones Required for AD

| Zone Type | Purpose |
|-----------|---------|
| **Forward Lookup Zone** | Resolves hostnames → IP addresses |
| **Reverse Lookup Zone** | Resolves IP addresses → hostnames |
| **_msdcs.domain.com** | Stores critical AD SRV records |

Recommended:

- **Active Directory–Integrated DNS Zones**  
- **Secure Dynamic Updates**  

---

# Environment

| Component | Details |
|----------|---------|
| DNS Server | Windows Server 2016/2019/2022 |
| Directory Service | Active Directory Domain Services |
| Domain Controllers | DNS-integrated |
| Clients | Windows 10 / Windows 11 |
| Tools | DNS Manager, PowerShell, dcdiag, nslookup |

---

# Common Tasks

- Verify DNS configuration  
- Check SRV records  
- Fix domain join issues  
- Troubleshoot replication failures  
- Manage DNS aging/scavenging  
- Validate name resolution  
- Repair missing DNS records  

---

# Tools Used

- DNS Manager  
- dcdiag  
- nslookup  
- repadmin  
- PowerShell  
- Event Viewer  

---

# Troubleshooting Workflow

```
        DNS/AD Issue Detected
                 │
                 ▼
       Check DNS Server Configuration
                 │
                 ▼
       Validate SRV & A Records for DCs
                 │
                 ▼
     Test Name Resolution (nslookup)
                 │
                 ▼
     Check Zone Replication & AD Integration
                 │
                 ▼
     Review Event Logs (DNS, Directory Service)
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Verify AD Functionality
```

---

# Step 1 – Verify DNS Configuration

### Check DNS server settings

```powershell
ipconfig /all
```

Ensure:

- DNS server = domain controller  
- No external DNS servers  

### Check DNS service status

```powershell
Get-Service DNS
```

---

# Step 2 – Check Required DNS Records

### Validate SRV records

```
DNS Manager → domain.com → _msdcs → _tcp
```

### Test SRV record lookup

```powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.domain.com
```

### Check A records for domain controllers

```powershell
nslookup dc01.domain.com
```

### Re-register DNS records

```powershell
ipconfig /registerdns
```

---

# Step 3 – Validate DNS Zone Replication

### Check replication summary

```powershell
repadmin /replsummary
```

### Check DNS zone replication

```powershell
repadmin /showrepl dc01
```

### Force replication

```powershell
repadmin /syncall /AeD
```

---

# Step 4 – Troubleshoot Name Resolution

### Forward lookup test

```powershell
nslookup domain.com
```

### Reverse lookup test

```powershell
nslookup 192.168.1.10
```

### Check DNS client events

```
Event Viewer → System → DNS Client Events
```

---

# Step 5 – Fix Domain Join & Authentication Issues

### Test domain controller discovery

```powershell
nltest /dsgetdc:domain.com
```

### Check secure channel

```powershell
nltest /sc_verify:domain.com
```

### Repair secure channel

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

---

# Step 6 – Manage DNS Scavenging & Aging

### Enable scavenging

```
DNS Manager → Server → Properties → Advanced → Enable Scavenging
```

### Configure zone scavenging

```
Zone → Properties → General → Aging
```

### PowerShell

```powershell
Set-DnsServerScavenging -ScavengingState $true -RefreshInterval 7.00:00:00 -NoRefreshInterval 7.00:00:00
```

---

# Step 7 – Advanced DNS & AD Diagnostics

## Check DNS health

```powershell
dcdiag /test:dns
```

## Check AD health

```powershell
dcdiag /v
```

## Check DNS replication metadata

```powershell
repadmin /showobjmeta dc01 "CN=MicrosoftDNS,DC=DomainDnsZones,DC=domain,DC=com"
```

## Clear DNS cache

```powershell
Clear-DnsClientCache
```

---

# Verification

Confirm:

- DNS resolves domain controllers  
- SRV records present  
- Replication healthy  
- Domain join successful  
- Kerberos authentication working  
- No DNS errors in event logs  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| Domain join fails | Wrong DNS server |
| GPO not applying | Missing SRV records |
| Replication errors | DNS zone not replicating |
| Kerberos failures | Time sync or DNS issues |
| DC not discoverable | Missing _msdcs records |

---

# Preventive Best Practices

- Use AD-integrated DNS zones  
- Enable secure dynamic updates  
- Avoid external DNS servers on domain clients  
- Monitor DNS event logs  
- Regularly check SRV records  
- Use scavenging to clean stale records  
- Document DNS architecture  

---

# Escalation Criteria

Escalate when:

- DNS fails across multiple domain controllers  
- SRV records missing or corrupted  
- Replication broken across sites  
- Domain join fails domain-wide  
- Kerberos authentication failing  
- AD-integrated DNS zone corruption suspected  

---

# References

- Microsoft Learn – DNS for Active Directory  
- Microsoft Learn – Domain Controller Locator  
- Microsoft Learn – PowerShell DNS Module  
- Microsoft Learn – Active Directory Replication  
