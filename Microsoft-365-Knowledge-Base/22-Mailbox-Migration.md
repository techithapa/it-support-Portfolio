
# Microsoft 365 Knowledge Base  
## 22 — Mailbox Migration

---

## Overview

Mailbox migration is the process of moving email data from one environment to another—commonly from on‑premises Exchange, IMAP systems, or tenant‑to‑tenant migrations into Exchange Online. Proper planning and execution ensure minimal downtime, data integrity, and a smooth transition for users.

This document covers:
- Migration types  
- Pre‑migration requirements  
- Cutover migration  
- Staged migration  
- Hybrid migration  
- IMAP migration  
- Tenant‑to‑tenant migration  
- Post‑migration tasks  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Cutover Migration** | Moves all mailboxes at once |
| **Staged Migration** | Moves mailboxes in batches |
| **Hybrid Migration** | Coexistence between on‑prem and cloud |
| **IMAP Migration** | Moves email only (no contacts/calendar) |
| **Mailbox Move Request** | Exchange Online process for mailbox migration |
| **Autodiscover** | Automatically configures Outlook profiles |
| **Migration Endpoint** | Connection settings for source environment |

---

## Tools Used

- Exchange Admin Center  
- Microsoft 365 Admin Center  
- Exchange Management Shell  
- PowerShell (Exchange Online)  
- Hybrid Configuration Wizard  
- Migration Dashboard  

---

# 1. Migration Types

## 1.1 Cutover Migration (Small Organizations)

- All mailboxes migrated at once  
- Suitable for <150 mailboxes  
- Requires full DNS cutover  

---

## 1.2 Staged Migration (Medium Organizations)

- Mailboxes migrated in batches  
- Suitable for 150–2000 mailboxes  
- Requires directory synchronization  

---

## 1.3 Hybrid Migration (Large Organizations)

- Full coexistence  
- Gradual mailbox moves  
- Best for >2000 mailboxes  
- Requires Hybrid Configuration Wizard  

---

## 1.4 IMAP Migration

- Moves email only  
- Does not migrate contacts, calendar, tasks  
- Requires manual Outlook export for full data  

---

## 1.5 Tenant‑to‑Tenant Migration

Used for:
- Mergers  
- Acquisitions  
- Domain changes  

Requires:
- Cross‑tenant mailbox migration  
- Domain transfer  
- Re‑provisioning users  

---

# 2. Pre‑Migration Requirements

## 2.1 Verify Domain Ownership

```
Microsoft 365 Admin Center → Settings → Domains → Add Domain
```

Add TXT record for verification.

---

## 2.2 Prepare Source Environment

- Ensure mailboxes are healthy  
- Remove corrupt items  
- Verify Autodiscover  
- Ensure Exchange servers are updated  

---

## 2.3 Prepare Target Environment

- Create users  
- Assign licenses  
- Configure groups  
- Set up migration endpoints  

---

## 2.4 Network & Firewall Requirements

Allow:
- HTTPS (443)  
- Exchange RPC/HTTP (for hybrid)  

---

# 3. Migration Endpoints

Migration endpoints define connection settings for the source server.

## 3.1 Create Migration Endpoint

```
Exchange Admin Center → Migration → Migration Endpoints → New
```

Types:
- Exchange  
- IMAP  
- Hybrid  

---

## 3.2 PowerShell Example

```powershell
New-MigrationEndpoint -Name "OnPremEndpoint" `
-ExchangeServer "mail.domain.com" `
-Credentials (Get-Credential)
```

---

# 4. Cutover Migration

## 4.1 Steps

1. Verify domain  
2. Create migration endpoint  
3. Start migration batch  
4. Wait for synchronization  
5. Change MX records to Microsoft 365  
6. Complete migration batch  
7. Reconfigure Outlook profiles  

---

## 4.2 Create Migration Batch

```powershell
New-MigrationBatch -Name "CutoverBatch" `
-Cutover `
-SourceEndpoint "OnPremEndpoint"
```

---

# 5. Staged Migration

## 5.1 Requirements

- Directory synchronization (Entra Connect)  
- CSV file with mailbox list  

---

## 5.2 Create Migration Batch

```powershell
New-MigrationBatch -Name "StagedBatch1" `
-CSVData (Get-Content "batch1.csv") `
-SourceEndpoint "OnPremEndpoint"
```

---

## 5.3 Complete Batch

```powershell
Complete-MigrationBatch -Identity "StagedBatch1"
```

---

# 6. Hybrid Migration

Hybrid migration provides seamless coexistence.

## 6.1 Run Hybrid Configuration Wizard

```
https://aka.ms/HybridWizard
```

Configures:
- Autodiscover  
- OAuth  
- Mail flow  
- Free/busy sharing  

---

## 6.2 Move Mailbox

```powershell
New-MoveRequest -Identity user@domain.com -Remote
```

---

## 6.3 Check Move Status

```powershell
Get-MoveRequest -Identity user@domain.com
```

---

## 6.4 Complete Move

```powershell
Complete-MoveRequest -Identity user@domain.com
```

---

# 7. IMAP Migration

## 7.1 Prepare CSV File

```
EmailAddress,UserName,Password
user@domain.com,user1,password1
```

---

## 7.2 Create Migration Batch

```powershell
New-MigrationBatch -Name "IMAPBatch" `
-CSVData (Get-Content "imap.csv") `
-SourceEndpoint "IMAPEndpoint"
```

---

# 8. Tenant‑to‑Tenant Migration

## 8.1 Requirements

- Cross‑tenant mailbox migration enabled  
- Source and target tenant configuration  
- Domain transfer plan  
- User re‑provisioning  

---

## 8.2 Start Migration

```powershell
New-MigrationBatch -Name "T2TBatch" `
-CrossTenant `
-SourceEndpoint "SourceTenantEndpoint"
```

---

# 9. Post‑Migration Tasks

## 9.1 Update DNS Records

Update:
- MX  
- SPF  
- Autodiscover  
- DKIM  

---

## 9.2 Reconfigure Outlook Profiles

Users may need to:
- Restart Outlook  
- Recreate profile  
- Re‑authenticate  

---

## 9.3 Validate Mail Flow

Send test messages:
- Internal  
- External  
- Distribution lists  

---

## 9.4 Decommission Old Servers (Hybrid Only)

After migration:
- Remove hybrid configuration  
- Remove connectors  
- Shut down Exchange servers  

---

# 10. Troubleshooting

## Issue 1 — Migration batch stuck in “Syncing”

### Causes
- Network issues  
- Large mailbox  
- Corrupt items  

### Fix
- Increase throttling limits  
- Repair mailbox  
- Restart batch  

---

## Issue 2 — Outlook not connecting after migration

### Causes
- Autodiscover cached  
- Profile corruption  

### Fix
- Delete Autodiscover XML  
- Create new Outlook profile  

---

## Issue 3 — Mail flow issues after cutover

### Causes
- Incorrect MX record  
- SPF not updated  

### Fix
- Update DNS  
- Validate SPF  

---

## Issue 4 — Move request fails

### Causes
- Bad mailbox items  
- Permission issues  

### Fix
```powershell
New-MoveRequest -BadItemLimit 50 -LargeItemLimit 50
```

---

# 11. Verification Checklist

- Migration batch completed  
- Mail flow working  
- Outlook connected  
- OneDrive/SharePoint access intact  
- DNS updated  
- No sync errors  
- Old environment decommissioned (if applicable)  

---

# 12. Best Practices

- Use hybrid for large organizations  
- Test migration with pilot users  
- Use Autopilot for device re‑provisioning  
- Update DNS at off‑peak hours  
- Communicate migration timelines to users  
- Monitor migration dashboard  
- Document migration steps  
- Use retention policies for legacy data  

---

# References

- Microsoft Learn — Exchange Online Migration  
- Microsoft Learn — Hybrid Configuration Wizard  
- Microsoft Learn — IMAP Migration  
- Microsoft Learn — Tenant‑to‑Tenant Migration  
