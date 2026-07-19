
# Microsoft 365 Knowledge Base  
## 12 — Mail Flow and Message Trace

---

## Overview

Mail flow in Microsoft 365 determines how email is routed, delivered, filtered, and protected across Exchange Online. When mail fails to deliver or behaves unexpectedly, message tracing and mail flow diagnostics help identify the root cause.

This document covers:
- Mail flow architecture  
- Message trace  
- Transport rules  
- Connectors  
- Anti‑spam and anti‑malware filtering  
- Common mail flow issues  
- Troubleshooting  
- Best practices  

---

## Key Concepts

| Concept | Description |
|--------|-------------|
| **Message Trace** | Tracks email delivery through Exchange Online |
| **Transport Rules** | Rules that modify or control mail flow |
| **Connectors** | Control mail flow between Microsoft 365 and external systems |
| **MX Records** | DNS records that route mail to Microsoft 365 |
| **Anti‑Spam Policies** | Protect against spam and phishing |
| **Quarantine** | Holds suspicious or blocked messages |
| **Mail Flow Reports** | Provide insights into mail traffic |

---

## Tools Used

- Microsoft 365 Admin Center  
- Exchange Admin Center  
- PowerShell (Exchange Online)  
- Security & Compliance Center  
- Message Trace Logs  
- DNS Tools  

---

# 1. Mail Flow Architecture

## 1.1 Inbound Mail Flow

Inbound mail typically follows this path:

```
Internet → Microsoft 365 Front Door → Anti-Spam/Malware Filtering → Transport Rules → Mailbox Delivery
```

---

## 1.2 Outbound Mail Flow

Outbound mail follows:

```
Mailbox → Transport Rules → Anti-Spam → Internet
```

---

## 1.3 Hybrid Mail Flow (Optional)

Hybrid environments use:
- On‑prem Exchange servers  
- Exchange Online  
- Hybrid connectors  

---

# 2. Message Trace

Message trace is the primary tool for diagnosing mail flow issues.

## 2.1 Run Message Trace (Admin Center)

```
Exchange Admin Center → Mail Flow → Message Trace
```

Options:
- Basic trace (last 7 days)  
- Detailed trace (up to 90 days)  
- Download CSV  

---

## 2.2 Run Message Trace (PowerShell)

### Basic Trace
```powershell
Get-MessageTrace -SenderAddress jsmith@domain.com -StartDate (Get-Date).AddDays(-1) -EndDate (Get-Date)
```

### Detailed Trace
```powershell
Get-MessageTraceDetail -MessageTraceId <TraceId> -RecipientAddress user@domain.com
```

---

## 2.3 Common Message Trace Status Codes

| Status | Meaning |
|--------|---------|
| **Delivered** | Successfully delivered |
| **Expanded** | Sent to members of a distribution list |
| **Quarantined** | Held due to spam/malware |
| **Failed** | Delivery failed |
| **Pending** | Still processing |
| **FilteredAsSpam** | Blocked as spam |
| **Redirected** | Transport rule changed routing |

---

# 3. Transport Rules

Transport rules modify mail flow based on conditions.

## 3.1 Common Uses

- Block external mail to specific users  
- Add disclaimers  
- Redirect mail  
- Apply DLP policies  
- Modify headers  

---

## 3.2 View Transport Rules

```powershell
Get-TransportRule
```

---

## 3.3 Create Transport Rule

```powershell
New-TransportRule -Name "Block External to HR" `
-FromScope NotInOrganization -SentTo "hr@domain.com" -RejectMessageReasonText "External mail blocked"
```

---

# 4. Connectors

Connectors control mail flow between Microsoft 365 and external systems.

## 4.1 Types of Connectors

| Connector Type | Purpose |
|----------------|---------|
| **Inbound** | Accept mail from on‑prem or third‑party systems |
| **Outbound** | Route mail to on‑prem or third‑party systems |
| **Partner** | Secure mail flow with trusted partners |

---

## 4.2 View Connectors

```powershell
Get-InboundConnector
Get-OutboundConnector
```

---

## 4.3 Common Connector Issues

- Incorrect IP ranges  
- TLS certificate mismatch  
- Firewall blocks  
- Incorrect routing  

---

# 5. Anti‑Spam & Anti‑Malware Filtering

## 5.1 Policies

```
Security & Compliance → Policies → Threat Policies
```

Includes:
- Anti‑spam  
- Anti‑phishing  
- Anti‑malware  
- Safe Links  
- Safe Attachments  

---

## 5.2 Quarantine

Admins can release quarantined messages.

```
Security & Compliance → Quarantine
```

---

## 5.3 Allow/Block Lists

### Allow sender
```powershell
Set-HostedContentFilterPolicy -Identity Default -AllowedSenders "trusted@domain.com"
```

### Block sender
```powershell
Set-HostedContentFilterPolicy -Identity Default -BlockedSenders "spam@domain.com"
```

---

# 6. DNS Requirements

## 6.1 MX Record

Should point to:
```
domain-com.mail.protection.outlook.com
```

---

## 6.2 SPF Record

Example:
```
v=spf1 include:spf.protection.outlook.com -all
```

---

## 6.3 DKIM

Enable in Security & Compliance Center.

---

## 6.4 DMARC

Example:
```
v=DMARC1; p=none; rua=mailto:dmarc@domain.com
```

---

# 7. Common Mail Flow Issues

## Issue 1 — Email not delivered

### Causes
- Incorrect MX record  
- Transport rule blocking  
- Quarantine  
- Spam filtering  

### Fix
- Check message trace  
- Review transport rules  
- Release from quarantine  
- Validate DNS  

---

## Issue 2 — External mail blocked

### Causes
- Anti‑spam policies  
- Transport rules  
- Connector restrictions  

### Fix
- Review anti‑spam policies  
- Check transport rules  
- Validate connectors  

---

## Issue 3 — Mail stuck in queue (Hybrid)

### Causes
- On‑prem server issues  
- TLS mismatch  
- Firewall blocks  

### Fix
- Check queue viewer  
- Validate TLS certificate  
- Review firewall rules  

---

## Issue 4 — Duplicate emails

### Causes
- Forwarding loops  
- Transport rule misconfiguration  

### Fix
- Remove forwarding loops  
- Review rules  

---

## Issue 5 — Delayed mail

### Causes
- Greylisting  
- High spam score  
- Routing issues  

### Fix
- Check message trace  
- Review anti‑spam logs  
- Validate DNS  

---

# 8. Troubleshooting Steps

## Step 1 — Run Message Trace  
Identify delivery status.

## Step 2 — Check Quarantine  
Release if needed.

## Step 3 — Review Transport Rules  
Look for unexpected routing.

## Step 4 — Validate DNS  
Check MX, SPF, DKIM, DMARC.

## Step 5 — Check Connectors  
Ensure correct configuration.

## Step 6 — Review Security Policies  
Check anti‑spam and anti‑phishing.

## Step 7 — Check Mailbox Settings  
Forwarding, inbox rules, block lists.

---

# 9. Verification Checklist

- Message trace shows successful delivery  
- No quarantine blocks  
- DNS records correct  
- Transport rules functioning  
- Connectors configured correctly  
- No mail delays  
- No spam false positives  

---

# 10. Best Practices

- Use message trace for all mail flow issues  
- Document transport rules  
- Avoid complex forwarding loops  
- Regularly review quarantine  
- Maintain correct DNS records  
- Use DKIM and DMARC  
- Monitor mail flow reports  
- Train users to identify phishing  

---

# References

- Microsoft Learn — Message Trace  
- Microsoft Learn — Mail Flow  
- Microsoft Learn — Exchange Online Protection  
