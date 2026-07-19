
# Microsoft 365 Knowledge Base  
## 04 — License Management

---

## Overview

License Management in Microsoft 365 ensures users have access to the correct applications and services such as Exchange Online, Teams, SharePoint, OneDrive, and Microsoft 365 Apps. Proper license assignment is essential for productivity, compliance, cost control, and security.

This document covers:
- License types  
- Assigning and removing licenses  
- Service plan management  
- Group‑based licensing  
- Troubleshooting license issues  
- Best practices for license governance  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **SKU (License Type)** | A Microsoft 365 product bundle (e.g., E3, Business Premium) |
| **Service Plans** | Individual components inside a license (Exchange, Teams, etc.) |
| **Group‑Based Licensing** | Assign licenses automatically via Azure AD groups |
| **Usage Location** | Determines available services (must be set before licensing) |
| **License Enforcement** | Controls which apps users can access |

---

## Tools Used

- Microsoft 365 Admin Center  
- Microsoft Entra Admin Center  
- PowerShell (MSOnline, AzureAD, Graph)  
- Billing Portal  
- Usage Reports  

---

# 1. License Types

Common Microsoft 365 license SKUs:

| License | Description |
|---------|-------------|
| **Microsoft 365 Business Basic** | Email + cloud services (no desktop apps) |
| **Microsoft 365 Business Standard** | Email + desktop apps |
| **Microsoft 365 Business Premium** | Standard + Intune + Defender |
| **Office 365 E1** | Enterprise cloud services |
| **Office 365 E3** | Email + desktop apps + compliance |
| **Office 365 E5** | Advanced security + analytics |
| **Microsoft 365 E3/E5** | Enterprise security + device management |

---

# 2. Assigning Licenses

## 2.1 Microsoft 365 Admin Center

```
Users → Active Users → Select User → Licenses and Apps
```

Steps:
- Set **Usage Location**  
- Select license SKU  
- Enable/disable service plans  

---

## 2.2 PowerShell (MSOnline)

### Assign license
```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com `
-AddLicenses "tenant:ENTERPRISEPACK"
```

### Remove license
```powershell
Set-MsolUserLicense -UserPrincipalName jsmith@domain.com `
-RemoveLicenses "tenant:ENTERPRISEPACK"
```

---

## 2.3 PowerShell (Graph)

### Assign license
```powershell
Set-MgUserLicense -UserId jsmith@domain.com `
-AddLicenses @{SkuId="<GUID>"}
```

### Remove license
```powershell
Set-MgUserLicense -UserId jsmith@domain.com `
-RemoveLicenses @("<GUID>")
```

---

# 3. Managing Service Plans

Service plans control access to individual apps.

### Example: Disable Teams for a user
```powershell
$license = Get-MgUserLicenseDetail -UserId jsmith@domain.com
Set-MgUserLicense -UserId jsmith@domain.com `
-AddLicenses @{} `
-RemoveLicenses @{} `
-DisabledPlans @("TEAMS1")
```

Common service plans:
- EXCHANGE_S_STANDARD  
- SHAREPOINTWAC  
- TEAMS1  
- INTUNE_A  
- FLOW_O365  

---

# 4. Group‑Based Licensing (Recommended)

Group‑based licensing automates license assignment.

### Entra Admin Center
```
Groups → Select Group → Licenses → Assignments
```

Benefits:
- Automatic license assignment  
- Consistent configuration  
- Reduced manual work  
- Ideal for onboarding/offboarding  

### Requirements
- Azure AD Premium P1 or higher  

---

# 5. Usage Location

Usage location **must be set before assigning licenses**.

### PowerShell
```powershell
Set-MsolUser -UserPrincipalName jsmith@domain.com -UsageLocation AU
```

---

# 6. License Reporting

### Admin Center
```
Billing → Licenses → License Usage
```

### PowerShell
```powershell
Get-MsolAccountSku
```

Shows:
- Total licenses  
- Assigned licenses  
- Available licenses  

---

# 7. Troubleshooting License Issues

## Issue 1 — License cannot be assigned

### Causes
- Usage location not set  
- License SKU unavailable  
- Group‑based licensing conflict  

### Fix
- Set usage location  
- Check SKU availability  
- Review group licensing assignments  

---

## Issue 2 — Mailbox not created after license assignment

### Causes
- Exchange plan disabled  
- Sync delay (hybrid)  
- Incorrect UPN  

### Fix
- Enable EXCHANGE_S_STANDARD  
- Force Azure AD Connect sync  
- Verify UPN domain  

---

## Issue 3 — Teams not available

### Causes
- TEAMS1 disabled  
- Conditional Access blocking  
- License mismatch  

### Fix
- Enable Teams service plan  
- Review CA policies  
- Check license SKU supports Teams  

---

## Issue 4 — OneDrive not provisioned

### Causes
- SharePoint plan disabled  
- User not licensed for SharePoint  
- Provisioning delay  

### Fix
- Enable SHAREPOINTWAC  
- Wait up to 24 hours  

---

# 8. Verification Checklist

- License assigned successfully  
- Service plans enabled correctly  
- Mailbox created  
- Teams/SharePoint/OneDrive accessible  
- No license conflicts  
- Group‑based licensing applied (if used)  

---

# 9. Best Practices

- Use group‑based licensing for automation  
- Standardize license assignment by role/department  
- Regularly audit unused licenses  
- Avoid assigning licenses directly to admin accounts  
- Use cost‑optimization reports  
- Document license assignment workflows  

---

# References

- Microsoft Learn — License Management  
- Microsoft Learn — Group‑Based Licensing  
- Microsoft Learn — PowerShell for Microsoft 365  
