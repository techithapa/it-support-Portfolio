# Azure AD / Microsoft Entra Sync (Azure AD Connect)

> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 16 — Intermediate Difficulty

---

# Table of Contents

- Overview  
- What Is Entra ID / Azure AD Connect Sync?  
- How Synchronization Works  
- Sync Components  
- Environment  
- Common Tasks  
- Tools Used  
- Troubleshooting Workflow  
- Step 1 – Verify Sync Health  
- Step 2 – Check Connector Status  
- Step 3 – Validate Attribute Synchronization  
- Step 4 – Troubleshoot Password Hash Sync  
- Step 5 – Troubleshoot Object Synchronization  
- Step 6 – Manage OU & Attribute Filtering  
- Step 7 – Advanced Sync Diagnostics  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# Overview

Microsoft Entra ID (formerly Azure Active Directory) integrates with on‑premises Active Directory using **Azure AD Connect Sync**. This enables hybrid identity, allowing users to authenticate to cloud services such as Microsoft 365 using their on‑prem credentials.

This document provides a structured guide for understanding, managing, and troubleshooting Entra ID / Azure AD Connect synchronization.

---

# What Is Entra ID / Azure AD Connect Sync?

Azure AD Connect Sync enables:

- Synchronization of on‑prem AD users, groups, and contacts  
- Password hash synchronization (PHS)  
- Pass-through authentication (PTA)  
- Seamless single sign-on (SSO)  
- Device writeback (optional)  
- Group writeback (optional)  

It is the backbone of hybrid identity.

---

# How Synchronization Works

Azure AD Connect uses:

- **Sync engine** (MIIS)  
- **Connectors** (AD DS connector + Entra ID connector)  
- **Scheduler** (runs every 30 minutes by default)  
- **Metaverse** (internal database of objects)  

Sync flow:

```
AD DS → Connector Space → Metaverse → Entra ID Connector → Entra ID
```

---

# Sync Components

| Component | Purpose |
|-----------|---------|
| **Sync Engine** | Processes sync rules |
| **AD DS Connector** | Reads on‑prem AD |
| **Entra ID Connector** | Writes to cloud |
| **Scheduler** | Controls sync frequency |
| **Metaverse** | Stores object state |
| **Sync Rules** | Define attribute flows |

---

# Environment

| Component | Details |
|----------|---------|
| Directory Service | AD DS + Entra ID |
| Sync Tool | Azure AD Connect / Cloud Sync |
| Servers | Windows Server 2016/2019/2022 |
| Identity | Hybrid (On‑prem + Cloud) |
| Tools | Synchronization Service Manager, Entra Admin Center |

---

# Common Tasks

- Check sync health  
- Force synchronization  
- Troubleshoot sync errors  
- Manage OU filtering  
- Manage attribute filtering  
- Fix duplicate attribute issues  
- Validate password hash sync  
- Review sync logs  

---

# Tools Used

- **Synchronization Service Manager**  
- **Azure AD Connect Health**  
- **Entra Admin Center**  
- **PowerShell (MSOnline / Graph)**  
- **Event Viewer**  

---

# Troubleshooting Workflow

```
        Sync Issue Detected
                 │
                 ▼
       Check Azure AD Connect Health
                 │
                 ▼
       Validate Connector Status & Last Sync
                 │
                 ▼
     Review Synchronization Errors (Sync Manager)
                 │
                 ▼
     Check Attribute Flow & Filtering Rules
                 │
                 ▼
     Validate Password Hash Sync (if applicable)
                 │
                 ▼
     Review Event Logs (Directory Sync)
                 │
                 ▼
               Apply Fixes
                 │
                 ▼
            Force Full Synchronization
                 │
                 ▼
             Verify Cloud Object State
```

---

# Step 1 – Verify Sync Health

### Using PowerShell

```powershell
Get-ADSyncScheduler
```

### Check last sync time

```powershell
Get-ADSyncScheduler | Select LastSyncTime
```

### Using Entra Admin Center

```
Entra ID → Azure AD Connect → Health
```

---

# Step 2 – Check Connector Status

Open:

```
Synchronization Service Manager → Connectors
```

Check:

- AD DS connector  
- Entra ID connector  
- Last run status  
- Errors or warnings  

---

# Step 3 – Validate Attribute Synchronization

### Check object in Metaverse

```
Synchronization Service Manager → Metaverse Search
```

### Check attribute flow

```
Synchronization Rules Editor
```

### PowerShell (cloud object)

```powershell
Get-MsolUser -UserPrincipalName jsmith@domain.com
```

or Graph:

```powershell
Get-MgUser -UserId jsmith@domain.com
```

---

# Step 4 – Troubleshoot Password Hash Sync

### Check PHS status

```powershell
Get-ADSyncAADPasswordSyncConfiguration
```

### Check PHS events

```
Event Viewer → Applications and Services Logs → Directory Synchronization
```

### Force password hash sync

```powershell
Start-ADSyncSyncCycle -PolicyType Delta
```

---

# Step 5 – Troubleshoot Object Synchronization

### Common causes

- Duplicate UPN  
- Duplicate proxyAddresses  
- Missing required attributes  
- Filtered OU  
- Sync rule conflict  
- Soft‑deleted cloud object  

### Check sync errors

```
Synchronization Service Manager → Operations → Errors
```

### Check cloud soft delete

```powershell
Get-MsolUser -ReturnDeletedUsers
```

---

# Step 6 – Manage OU & Attribute Filtering

### Modify OU filtering

```
Azure AD Connect → Configure → Customize Synchronization Options
```

### Modify attribute filtering

```
Synchronization Rules Editor
```

### PowerShell (Cloud Sync)

```powershell
Get-MgDirectorySynchronization
```

---

# Step 7 – Advanced Sync Diagnostics

## Force full sync

```powershell
Start-ADSyncSyncCycle -PolicyType Initial
```

## Check sync logs

```
Event Viewer → Applications and Services Logs → Directory Synchronization
```

## Check duplicate attributes

```powershell
Get-MsolUser -All | Select UserPrincipalName,ProxyAddresses
```

## Check connector space object

```
Synchronization Service Manager → Connector Space
```

---

# Verification

Confirm:

- Sync cycle successful  
- No sync errors in Sync Manager  
- Cloud object updated correctly  
- Password hash sync working  
- OU filtering correct  
- No duplicate attributes  

---

# Common Issues

| Issue | Cause |
|-------|-------|
| Object not syncing | OU filtering or missing attributes |
| Duplicate attribute error | Duplicate UPN or proxyAddress |
| Password not syncing | PHS misconfiguration |
| Cloud object incorrect | Sync rule conflict |
| Sync stopped | Scheduler disabled |

---

# Preventive Best Practices

- Monitor Azure AD Connect Health  
- Document OU filtering  
- Avoid manual cloud edits for synced objects  
- Ensure unique UPN and proxyAddress values  
- Keep Azure AD Connect updated  
- Use a dedicated server for sync  
- Avoid installing other apps on sync server  

---

# Escalation Criteria

Escalate when:

- Sync fails across multiple cycles  
- Password hash sync stops domain‑wide  
- Duplicate attributes cannot be resolved  
- Sync engine corruption suspected  
- Entra ID objects missing or inconsistent  
- Azure AD Connect server unhealthy  

---

# References

- Microsoft Learn – Azure AD Connect  
- Microsoft Learn – Entra ID Sync  
- Microsoft Learn – Hybrid Identity  
- Microsoft Learn – PowerShell Graph Module  
