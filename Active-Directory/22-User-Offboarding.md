
# **Document 22 — User Offboarding (Active Directory)**  
> Enterprise Windows Troublesbling Knowledge Base  
> Active Directory Module 22 — Intermediate Difficulty  
> *Standardized process for secure user deprovisioning in AD + Entra hybrid environments.*

---

# **Table of Contents**

- Overview  
- Offboarding Workflow Summary  
- Environment  
- Tools Used  
- Step 1 – Receive Offboarding Request  
- Step 2 – Disable User Account  
- Step 3 – Block Sign‑In (Entra ID / M365)  
- Step 4 – Revoke Sessions & Tokens  
- Step 5 – Reset Password & Convert Account  
- Step 6 – Remove Group Memberships  
- Step 7 – Handle Email (Forwarding, Shared Mailbox, Archive)  
- Step 8 – Handle Files & Home Drive  
- Step 9 – Disable Workstation & Device Access  
- Step 10 – Disable MFA & Conditional Access  
- Step 11 – License Removal  
- Step 12 – Account Deletion (Final Stage)  
- Step 13 – Troubleshooting Offboarding Issues  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# **Overview**

User offboarding is a critical security process. Incorrect or incomplete offboarding can lead to:

- Unauthorized access  
- Data leakage  
- Compliance violations  
- Insider threats  

This document provides a complete, secure, and repeatable offboarding workflow for Active Directory and hybrid Entra ID environments.

---

# **Offboarding Workflow Summary**

```
HR Request → Disable Account → Block Cloud Sign-In → Revoke Sessions
→ Remove Groups → Secure Email → Secure Files → Remove Licenses
→ Archive → Delete Account (after retention period)
```

---

# **Environment**

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Identity Sync | Azure AD Connect / Entra Connect |
| Email | Exchange On‑Prem / Microsoft 365 |
| File Services | Windows File Server / DFS |
| Security | MFA, Conditional Access, LAPS |
| Compliance | Retention policies, legal hold |

---

# **Tools Used**

- ADUC  
- Entra Admin Center  
- Exchange Admin Center  
- PowerShell (AD, MSOnline, Graph)  
- File Server Manager  
- Group Policy Management  

---

# **Step 1 – Receive Offboarding Request**

Collect:

- Termination date/time  
- Manager approval  
- Required data retention  
- Email forwarding requirements  
- Shared mailbox access  
- Device collection status  
- HR compliance requirements  

---

# **Step 2 – Disable User Account (AD)**

### ADUC

```
Right‑click user → Disable Account
```

### PowerShell

```powershell
Disable-ADAccount -Identity jsmith
```

This immediately blocks domain authentication.

---

# **Step 3 – Block Sign‑In (Entra ID / M365)**

### Entra Admin Center

```
User → Block sign-in = Yes
```

### PowerShell

```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -BlockCredential $true
```

---

# **Step 4 – Revoke Sessions & Tokens**

### Entra ID

```
User → Revoke Sessions
```

### PowerShell

```powershell
Revoke-MgUserSignInSession -UserId jsmith@domain.com
```

This forces logout from all devices and apps.

---

# **Step 5 – Reset Password & Convert Account**

### Reset password

```powershell
Set-ADAccountPassword -Identity jsmith -Reset -NewPassword (ConvertTo-SecureString "Disabled123!" -AsPlainText -Force)
```

### Convert to service/holding state (optional)

- Move to **Disabled Users OU**  
- Apply restrictive GPO  

---

# **Step 6 – Remove Group Memberships**

### PowerShell

```powershell
Get-ADUser jsmith -Property MemberOf | Select -Expand MemberOf | ForEach-Object {
    Remove-ADGroupMember -Identity $_ -Members jsmith -Confirm:$false
}
```

This prevents access to:

- Shared folders  
- Applications  
- Security roles  

---

# **Step 7 – Handle Email (Forwarding, Shared Mailbox, Archive)**

### Options:

- Forward mail to manager  
- Convert mailbox to shared  
- Export PST  
- Apply retention policy  
- Remove mailbox license  

### Convert mailbox to shared

```powershell
Set-Mailbox -Identity jsmith -Type Shared
```

### Set forwarding

```powershell
Set-Mailbox -Identity jsmith -ForwardingSMTPAddress manager@domain.com
```

---

# **Step 8 – Handle Files & Home Drive**

### Secure home drive

- Copy data to manager  
- Archive to secure location  
- Remove NTFS permissions  

### PowerShell

```powershell
icacls "\\server\home\jsmith" /inheritance:r
```

---

# **Step 9 – Disable Workstation & Device Access**

### Remove from local groups

```powershell
Remove-LocalGroupMember -Group "Remote Desktop Users" -Member "domain\jsmith"
```

### Disable BitLocker recovery access  
### Remove device from Entra ID / Intune (if applicable)

---

# **Step 10 – Disable MFA & Conditional Access**

### Entra ID

- Remove MFA methods  
- Remove CA policy assignments  

---

# **Step 11 – License Removal**

### PowerShell

```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com -RemoveLicenses "tenant:ENTERPRISEPACK"
```

---

# **Step 12 – Account Deletion (Final Stage)**

Performed after retention period (30–90 days).

### ADUC

```
Right‑click user → Delete
```

### PowerShell

```powershell
Remove-ADUser -Identity jsmith
```

### Cloud deletion

```powershell
Remove-MsolUser -UserPrincipalName jsmith@domain.com -Force
```

---

# **Step 13 – Troubleshooting Offboarding Issues**

### Common checks:

- AD replication  
- Azure AD Connect sync  
- Licensing delays  
- Email forwarding errors  
- File server permission issues  
- MFA removal failures  

### Useful commands

```powershell
repadmin /replsummary
Get-MsolUser -UserPrincipalName jsmith@domain.com
Get-ADUser jsmith -Properties *
```

---

# **Verification**

Confirm:

- AD account disabled  
- Cloud sign‑in blocked  
- Sessions revoked  
- Groups removed  
- Email configured  
- Files secured  
- Licenses removed  
- No access to any system  

---

# **Common Issues**

| Issue | Cause |
|-------|-------|
| User still logging in | Cloud sign‑in not blocked |
| Email still active | Forwarding not configured |
| Access to shared folders | Group membership not removed |
| MFA still prompting | Methods not removed |
| Azure AD object still active | Sync delay |

---

# **Preventive Best Practices**

- Use automated offboarding workflows  
- Maintain a **Disabled Users OU**  
- Document group membership before removal  
- Use HR → IT ticketing integration  
- Enforce MFA removal  
- Archive mailbox and home drive consistently  
- Review access logs post‑offboarding  

---

# **Escalation Criteria**

Escalate when:

- User still has access after 1 hour  
- Cloud sign‑in cannot be blocked  
- Licensing removal fails repeatedly  
- Email forwarding fails  
- File access persists  
- Account deletion errors occur  

---

# **References**

- Microsoft Learn – Entra ID User Management  
- Microsoft Learn – AD User Lifecycle  
- Microsoft Learn – Exchange Mailbox Management  
- Microsoft Learn – Azure AD Connect  
