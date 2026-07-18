# **Document 21 — User Onboarding (Active Directory)**  
> Enterprise Windows Troubleshooting Knowledge Base  
> Active Directory Module 21 — Intermediate Difficulty  
> *Designed for IT Support, Helpdesk, and Systems Administrators managing new user provisioning in AD environments.*

---

# **Table of Contents**

- Overview  
- Onboarding Workflow Summary  
- Environment  
- Tools Used  
- Step 1 – Gather Required User Information  
- Step 2 – Create User Account in Active Directory  
- Step 3 – Assign Security Groups (AGDLP Model)  
- Step 4 – Configure Home Drive & Profile Paths  
- Step 5 – Configure Mailbox (Exchange / M365)  
- Step 6 – Configure Shared Folder Access  
- Step 7 – Configure Workstation & Login Permissions  
- Step 8 – Configure MFA / Conditional Access  
- Step 9 – Configure Applications & Licensing  
- Step 10 – First Login Checklist  
- Step 11 – Troubleshooting Onboarding Issues  
- Verification  
- Common Issues  
- Preventive Best Practices  
- Escalation Criteria  
- References  

---

# **Overview**

User onboarding is one of the most frequent and critical tasks in enterprise IT. A structured onboarding process ensures:

- Correct access permissions  
- Security compliance  
- Smooth first‑day experience  
- Reduced helpdesk tickets  
- Consistent identity lifecycle management  

This document provides a complete, standardized onboarding workflow for Active Directory environments (on‑prem + hybrid Entra ID).

---

# **Onboarding Workflow Summary**

```
HR Request → Create AD Account → Assign Groups → Configure Drives
→ Configure Email → Configure Apps → Configure MFA → First Login
```

---

# **Environment**

| Component | Details |
|----------|---------|
| Directory Service | Active Directory Domain Services |
| Identity Sync | Azure AD Connect / Entra Connect |
| Email | Exchange On‑Prem / Microsoft 365 |
| File Services | Windows File Server / DFS |
| Workstations | Windows 10 / Windows 11 |
| Security | MFA, Conditional Access, LAPS |

---

# **Tools Used**

- Active Directory Users and Computers (ADUC)  
- Group Policy Management Console (GPMC)  
- Microsoft Entra Admin Center  
- Exchange Admin Center / PowerShell  
- File Server Manager  
- PowerShell AD Module  

---

# **Step 1 – Gather Required User Information**

Collect:

- Full name  
- Username format (e.g., jsmith)  
- Department  
- Job role  
- Manager  
- Location / Site  
- Required security groups  
- Required applications  
- Licensing requirements  
- Email address format  
- Home drive requirements  

Optional:

- Phone number  
- Employee ID  
- Start date  

---

# **Step 2 – Create User Account in Active Directory**

### Using ADUC

```
ADUC → OU = Users → New → User
```

Configure:

- First name  
- Last name  
- Logon name (UPN)  
- Password  
- Account options (password must change, etc.)  

### Using PowerShell

```powershell
New-ADUser -Name "John Smith" -GivenName "John" -Surname "Smith" `
-SamAccountName "jsmith" -UserPrincipalName "jsmith@domain.com" `
-Path "OU=Users,DC=domain,DC=com" -AccountPassword (Read-Host -AsSecureString) `
-Enabled $true
```

---

# **Step 3 – Assign Security Groups (AGDLP Model)**

Follow **AGDLP**:

- **A**ccounts → Users  
- **G**lobal Groups → Department groups  
- **DL** → Domain Local Groups → Permissions  
- **P**ermissions → Assigned to DL groups  

### Example

```powershell
Add-ADGroupMember -Identity "Finance-Users" -Members "jsmith"
```

---

# **Step 4 – Configure Home Drive & Profile Paths**

### Home Drive (H:)

```
ADUC → User → Profile → Home Folder
```

UNC path:

```
\\fileserver\home\jsmith
```

### PowerShell

```powershell
Set-ADUser jsmith -HomeDirectory "\\fileserver\home\jsmith" -HomeDrive "H:"
```

---

# **Step 5 – Configure Mailbox (Exchange / M365)**

### Exchange On‑Prem

```powershell
Enable-Mailbox -Identity "jsmith"
```

### Microsoft 365

Assign license:

```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com -AddLicenses "tenant:ENTERPRISEPACK"
```

---

# **Step 6 – Configure Shared Folder Access**

Assign user to appropriate groups:

```powershell
Add-ADGroupMember -Identity "Finance-Access" -Members "jsmith"
```

Verify NTFS + Share permissions.

---

# **Step 7 – Configure Workstation & Login Permissions**

### Add user to workstation local groups (if needed)

```powershell
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "domain\jsmith"
```

### Assign workstation via AD attribute (optional)

```
ADUC → User → Attribute Editor → msDS-AllowedToDelegateTo
```

---

# **Step 8 – Configure MFA / Conditional Access**

### Entra ID

- Register MFA  
- Apply Conditional Access policies  
- Enforce compliant device requirements  

---

# **Step 9 – Configure Applications & Licensing**

Examples:

- Microsoft 365  
- Teams  
- SharePoint  
- ERP / CRM systems  
- Finance apps  
- HR systems  

Document all assigned licenses.

---

# **Step 10 – First Login Checklist**

User should:

- Log in successfully  
- Change password  
- Access home drive  
- Access shared drives  
- Access email  
- Access required applications  
- Confirm MFA setup  
- Confirm printer access  
- Confirm workstation configuration  

---

# **Step 11 – Troubleshooting Onboarding Issues**

### Common checks:

- AD replication  
- DNS resolution  
- Group membership  
- Licensing delays  
- Azure AD Connect sync  
- GPO application  
- File server permissions  
- MFA registration issues  

### Useful commands

```powershell
gpresult /r
whoami /groups
repadmin /replsummary
Get-MsolUser -UserPrincipalName jsmith@domain.com
```

---

# **Verification**

Confirm:

- AD account active  
- Groups assigned correctly  
- Email working  
- Drives mapped  
- MFA configured  
- Apps accessible  
- No errors in event logs  

---

# **Common Issues**

| Issue | Cause |
|-------|-------|
| User cannot log in | Wrong password / replication delay |
| Drives missing | GPO not applied |
| Email not working | License not assigned |
| MFA not prompting | Conditional Access not applied |
| No shared folder access | Missing group membership |
| Azure AD object missing | OU filtering in AAD Connect |

---

# **Preventive Best Practices**

- Use onboarding templates  
- Standardize usernames  
- Use AGDLP for permissions  
- Document group membership  
- Automate onboarding with scripts  
- Use HR → IT workflow automation  
- Enable AD Recycle Bin  
- Use LAPS for workstation security  

---

# **Escalation Criteria**

Escalate when:

- User cannot authenticate after 30 minutes  
- Azure AD sync delays exceed 2 hours  
- Licensing fails repeatedly  
- GPOs not applying across multiple users  
- File server permissions inconsistent  
- Email provisioning fails domain‑wide  

---

# **References**

- Microsoft Learn – Active Directory Users  
- Microsoft Learn – Entra ID User Management  
- Microsoft Learn – Azure AD Connect  
- Microsoft Learn – Group Policy  
- Microsoft Learn – NTFS Permissions  

