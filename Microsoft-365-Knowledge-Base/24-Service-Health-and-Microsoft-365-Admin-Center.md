
# Microsoft 365 Knowledge Base  
## 24 — Service Health and Microsoft 365 Admin Center

---

## Overview

The Microsoft 365 Admin Center is the central hub for managing users, licenses, services, and security across the Microsoft 365 environment. The Service Health dashboard provides real-time visibility into outages, advisories, and planned maintenance events that may impact users.

This document covers:
- Admin Center navigation  
- Service Health dashboard  
- Message Center updates  
- Incident management  
- Service requests  
- Monitoring tools  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Admin Center** | Central portal for managing Microsoft 365 |
| **Service Health** | Real-time status of Microsoft 365 services |
| **Message Center** | Updates, advisories, and upcoming changes |
| **Incident** | Service disruption affecting users |
| **Advisory** | Non-critical issue or degraded performance |
| **Planned Maintenance** | Scheduled updates or downtime |
| **Service Request** | Support ticket submitted to Microsoft |

---

## Tools Used

- Microsoft 365 Admin Center  
- Service Health Dashboard  
- Message Center  
- Microsoft Support Portal  
- PowerShell (Service Communications API)  

---

# 1. Microsoft 365 Admin Center Overview

## 1.1 Navigation Structure

Key sections:
- **Home** — Overview of alerts, usage, and recommendations  
- **Users** — Active users, guests, deleted users  
- **Groups** — Security groups, distribution lists, M365 groups  
- **Devices** — Intune-managed devices  
- **Billing** — Licenses, subscriptions, invoices  
- **Settings** — Org settings, security, privacy  
- **Health** — Service health, message center  
- **Support** — Service requests  

---

## 1.2 Admin Roles Required

To access service health:
- Global Administrator  
- Service Support Administrator  
- Helpdesk Administrator  

---

# 2. Service Health Dashboard

## 2.1 Access Service Health

```
Microsoft 365 Admin Center → Health → Service Health
```

---

## 2.2 Service Status Indicators

| Status | Meaning |
|--------|---------|
| **Healthy** | No issues detected |
| **Advisory** | Minor issue or degraded performance |
| **Incident** | Major issue affecting users |
| **Restoring Service** | Microsoft is actively resolving the issue |
| **Extended Recovery** | Service restored but monitoring continues |

---

## 2.3 Services Monitored

- Exchange Online  
- SharePoint Online  
- OneDrive for Business  
- Microsoft Teams  
- Microsoft Entra ID  
- Intune  
- Microsoft Defender  
- Microsoft 365 Apps  

---

## 2.4 Incident Details

Each incident includes:
- Issue summary  
- Affected services  
- Impact description  
- Start time  
- Current status  
- Root cause (after resolution)  
- Workarounds (if available)  

---

# 3. Message Center

## 3.1 Purpose

Provides:
- Feature updates  
- Deprecation notices  
- Security advisories  
- Compliance changes  
- Admin actions required  

---

## 3.2 Access Message Center

```
Microsoft 365 Admin Center → Health → Message Center
```

---

## 3.3 Message Types

- **Plan for change** — Upcoming changes requiring admin action  
- **Prevent or fix issues** — Security or service advisories  
- **Stay informed** — New features or improvements  

---

## 3.4 Filtering Messages

Filter by:
- Product (Teams, Exchange, SharePoint)  
- Category  
- Importance  
- Admin impact  

---

# 4. Service Requests

## 4.1 Create Support Ticket

```
Microsoft 365 Admin Center → Support → New Service Request
```

---

## 4.2 Ticket Types

- Technical issues  
- Billing issues  
- Licensing issues  
- Security incidents  

---

## 4.3 Microsoft Support Response

Includes:
- Initial triage  
- Engineer assignment  
- Remote troubleshooting  
- Root cause analysis  
- Resolution summary  

---

# 5. Monitoring Tools

## 5.1 Microsoft 365 Health

Provides:
- Service health  
- Network connectivity tests  
- Endpoint analytics  

---

## 5.2 Network Connectivity Test

```
`https://connectivity.office.com` [(connectivity.office.com in Bing)](https://www.bing.com/search?q="https%3A%2F%2Fconnectivity.office.com%2F")
```

Tests:
- DNS  
- Latency  
- Bandwidth  
- Routing  

---

## 5.3 PowerShell Service Communications API

### Example: Get Service Health

```powershell
Connect-MgGraph -Scopes "ServiceHealth.Read.All"
Get-MgServiceAnnouncementHealth
```

---

# 6. Common Issues & Troubleshooting

## Issue 1 — Users reporting email delays

### Causes
- Exchange Online incident  
- Transport rule issues  
- Network latency  

### Fix
- Check Service Health  
- Review message trace  
- Validate transport rules  

---

## Issue 2 — Teams not loading

### Causes
- Teams service outage  
- Network restrictions  
- Conditional Access  

### Fix
- Check Service Health  
- Run connectivity test  
- Review CA policies  

---

## Issue 3 — OneDrive sync failures

### Causes
- OneDrive service advisory  
- Client version outdated  
- File path issues  

### Fix
- Check Service Health  
- Update OneDrive client  
- Shorten file paths  

---

## Issue 4 — Admin Center not loading

### Causes
- Browser cache  
- Service outage  
- Conditional Access  

### Fix
- Clear browser cache  
- Check Service Health  
- Review CA policies  

---

# 7. Verification Checklist

- Service Health shows no incidents  
- Message Center reviewed  
- No unresolved service requests  
- Network connectivity tests passed  
- Admin Center accessible  
- Users not reporting issues  

---

# 8. Best Practices

- Check Service Health daily  
- Review Message Center weekly  
- Subscribe to email notifications  
- Document major incidents  
- Use Conditional Access to protect admin access  
- Train helpdesk staff on Service Health usage  
- Monitor Secure Score for security posture  
- Use PowerShell for automated monitoring  

---

# References

- Microsoft Learn — Microsoft 365 Admin Center  
- Microsoft Learn — Service Health Dashboard  
- Microsoft Learn — Message Center  
- Microsoft Learn — Service Communications API  
