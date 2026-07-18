# Domain Join and Leave (Active Directory)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 11 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Is Domain Join?  
- What Is Domain Leave?  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Prepare DNS & Network Requirements  
- Step 2 – Join a Computer to the Domain  
- Step 3 – Verify Domain Join Success  
- Step 4 – Leave the Domain  
- Step 5 – Reset Computer Account in AD  
- Step 6 – Fix Trust Relationship Failures  
- Step 7 – Troubleshoot Domain Join Failures  
- Step 8 – Advanced Domain Join Management  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Joining a computer to an Active Directory domain enables centralized authentication, Group Policy application, and secure access to domain resources. Leaving the domain removes the computer from AD and returns it to a workgroup environment.

This document provides a structured guide for joining and removing computers from the domain, along with troubleshooting steps for common issues.

---

# What Is Domain Join?

Domain join allows a Windows device to:

- Authenticate using Active Directory  
- Receive Group Policies  
- Access shared folders and printers  
- Use Kerberos and NTLM authentication  
- Be managed centrally by IT  

---

# What Is Domain Leave?

Leaving the domain:

- Removes the computer account from AD  
- Breaks trust relationship  
- Converts the device to a workgroup  
- Removes domain-based policies  

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Domain Controllers | Windows Server 2016/2019/2022 |
| Clients | Windows 10 / Windows 11 |
| Management Tools | ADUC, PowerShell, RSAT |
| Identity | On-prem AD / Hybrid Entra ID |

---

# Common Tasks

- Join computers to the domain  
- Remove computers from the domain  
- Reset computer accounts  
- Fix trust relationship issues  
- Troubleshoot domain join failures  
- Validate DNS and network configuration  

---

# Tools Used

- System Settings  
- PowerShell  
- ADUC  
- Event Viewer  
- nltest  
- dcdiag  

---

# Troubleshooting Workflow

```
        Domain Join/Leave Issue
                 │
                 ▼
       Check DNS & Network Connectivity
                 │
                 ▼
       Validate Domain Controller Availability
                 │
                 ▼
     Test Authentication & Secure Channel (nltest)
                 │
                 ▼
     Reset Computer Account (if needed)
                 │
                 ▼
     Review Event Logs for Join/Leave Errors
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
           Reattempt Join/Leave
                 │
                 ▼
             Verify Domain Status
```

---

# Step 1 – Prepare DNS & Network Requirements

### Ensure correct DNS settings

Client must use **domain controller** as DNS:

```
ipconfig /all
```

Check:

- DNS server = DC IP  
- No external DNS servers  

### Test connectivity

```powershell
ping dc01.domain.com
```

### Test domain discovery

```powershell
nltest /dsgetdc:domain.com
```

---

# Step 2 – Join a Computer to the Domain

### Using System Settings

```
Settings → System → About → Rename this PC (Advanced)
```

Enter:

- Domain name  
- Domain admin credentials  

### Using PowerShell

```powershell
Add-Computer -DomainName "domain.com" -Credential domain\admin -Restart
```

---

# Step 3 – Verify Domain Join Success

### Check domain membership

```powershell
whoami /fqdn
```

### Check secure channel

```powershell
nltest /sc_verify:domain.com
```

### Check computer account in ADUC

```
ADUC → Computers or Workstations OU
```

---

# Step 4 – Leave the Domain

### Using System Settings

```
Settings → System → About → Disconnect from organization
```

### Using PowerShell

```powershell
Remove-Computer -UnjoinDomainCredential domain\admin -Restart
```

---

# Step 5 – Reset Computer Account in AD

Used when trust relationship breaks.

### Using ADUC

```
Right-click computer → Reset Account
```

### Using PowerShell

```powershell
Reset-ADComputer -Identity "PC-001"
```

---

# Step 6 – Fix Trust Relationship Failures

### On the affected PC

```powershell
Test-ComputerSecureChannel
```

### Repair secure channel

```powershell
Test-ComputerSecureChannel -Repair -Credential domain\admin
```

---

# Step 7 – Troubleshoot Domain Join Failures

### Common Causes

- Incorrect DNS  
- Wrong domain name  
- Firewall blocking ports  
- Time synchronization issues  
- Computer account already exists  
- Duplicate computer name  
- Domain controller unreachable  

### Check event logs

```
Event Viewer → System → Netlogon
Event Viewer → System → DNS Client Events
```

### Check time sync

```powershell
w32tm /query /status
```

### Check domain controller health

```powershell
dcdiag /v
```

---

# Step 8 – Advanced Domain Join Management

## Remove stale computer accounts

```powershell
Search-ADAccount -AccountInactive -ComputersOnly
```

## Rename computer before joining

```powershell
Rename-Computer -NewName "PC-Finance-01" -Restart
```

## Force domain join with OU placement

```powershell
Add-Computer -DomainName "domain.com" -OUPath "OU=Finance,DC=domain,DC=com" -Credential domain\admin -Restart
```

---

# Verification

Confirm:

- Device appears in correct OU  
- Secure channel healthy  
- DNS resolving correctly  
- Domain join successful  
- No trust relationship errors  
- GPOs applying correctly  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| “Domain not found” | DNS misconfiguration |
| “Access denied” | Wrong credentials |
| “Trust relationship failed” | Secure channel broken |
| Computer not in correct OU | Domain join without OUPath |
| Domain join fails | DC unreachable or firewall blocking |

---

# Preventive Best Practices

- Use DC as primary DNS  
- Ensure time synchronization  
- Use naming conventions for computers  
- Place computers in correct OUs  
- Regularly clean stale computer accounts  
- Document domain join procedures  

---

# Escalation Criteria

Escalate when:

- Domain join fails across multiple devices  
- Secure channel repeatedly breaks  
- DNS issues affecting multiple clients  
- Domain controllers not discoverable  
- AD replication issues detected  

---

# References

- Microsoft Learn – Domain Join  
- Microsoft Learn – Active Directory Computer Accounts  
- Microsoft Learn – PowerShell AD Module  
