

# **01 – User Account Management**  
Microsoft 365 Knowledge Base  


## **Overview**
User account management in Microsoft 365 (Microsoft Entra ID) covers the creation, modification, and lifecycle administration of user identities. This document provides step‑by‑step procedures for common tasks performed by IT administrators.

---

## **1. Creating a New User Account**

### **Microsoft 365 Admin Center**
1. Sign in to the **Microsoft 365 Admin Center**:  
   [https://admin.microsoft.com](https://admin.microsoft.com)  
2. Navigate to **Users → Active users**.  
3. Select **Add a user**.  
4. Enter:
   - Display name  
   - Username (UPN)  
   - Password settings  
   - Location  
5. Assign appropriate **licenses**.  
6. Configure **roles** (if required).  
7. Save the user.

### **PowerShell (Microsoft Graph PowerShell)**
```powershell
Connect-MgGraph -Scopes "User.ReadWrite.All"

New-MgUser -DisplayName "John Doe" `
  -UserPrincipalName "john.doe@contoso.com" `
  -MailNickname "john.doe" `
  -AccountEnabled $true `
  -PasswordProfile @{ Password = "TempP@ssw0rd!" }
```

---

## **2. Editing User Details**

### **Common Editable Fields**
- Display name  
- Username (UPN)  
- Job title  
- Department  
- Manager  
- Contact information  
- Usage location  

### **Admin Center**
1. Go to **Users → Active users**.  
2. Select the user.  
3. Edit the required fields under **Account**, **Profile**, or **Contact information**.

### **PowerShell**
```powershell
Update-MgUser -UserId "john.doe@contoso.com" -Department "Finance" -JobTitle "Analyst"
```

---

## **3. Resetting a User’s Password**
> Note: SSPR is covered in detail in **02‑Password‑Reset‑and‑SSPR.md**.

### **Admin Center**
1. Go to **Users → Active users**.  
2. Select the user.  
3. Choose **Reset password**.  
4. Provide the new password or auto‑generate one.

### **PowerShell**
```powershell
Update-MgUser -UserId "john.doe@contoso.com" `
  -PasswordProfile @{ Password = "NewP@ssw0rd!"; ForceChangePasswordNextSignIn = $true }
```

---

## **4. Blocking/Unblocking a User Account**

### **Block Sign‑In**
Used for security incidents or offboarding.

#### Admin Center
1. Open the user profile.  
2. Under **Account**, toggle **Block sign‑in** → **On**.

#### PowerShell
```powershell
Update-MgUser -UserId "john.doe@contoso.com" -AccountEnabled $false
```

### **Unblock Sign‑In**
```powershell
Update-MgUser -UserId "john.doe@contoso.com" -AccountEnabled $true
```

---

## **5. Deleting and Restoring Users**

### **Delete a User**
Admin Center → **Users → Active users → Delete user**

### **Restore a Deleted User**
1. Go to **Users → Deleted users**.  
2. Select the user → **Restore user**.

### **PowerShell**
```powershell
Remove-MgUser -UserId "john.doe@contoso.com"
```

Restore:
```powershell
Restore-MgDirectoryObject -DirectoryObjectId "<ObjectID>"
```

---

## **6. Assigning Roles**

### **Admin Center**
1. Open the user profile.  
2. Select **Roles**.  
3. Assign:
   - Global Administrator  
   - Helpdesk Administrator  
   - Exchange Administrator  
   - Teams Administrator  
   - Etc.

### **PowerShell**
```powershell
# Assign Global Admin
$role = Get-MgDirectoryRole | Where-Object {$_.DisplayName -eq "Global Administrator"}
Add-MgDirectoryRoleMember -DirectoryRoleId $role.Id -DirectoryObjectId "<UserObjectID>"
```

---

## **7. Viewing Sign‑In Logs**

### **Microsoft Entra Admin Center**
1. Go to [https://entra.microsoft.com](https://entra.microsoft.com)  
2. **Users → Sign‑in logs**

Useful for:
- MFA failures  
- Conditional Access blocks  
- Password issues  
- Device compliance failures  

---

## **8. Common Troubleshooting**

### **User Cannot Sign In**
- Check **account enabled**  
- Check **password reset**  
- Check **MFA configuration**  
- Check **Conditional Access policies**  
- Check **license assignment**

### **User Not Receiving Email**
- Verify mailbox license  
- Check mail flow rules  
- Check message trace (see section 12)

---

## **9. Best Practices**
- Enforce MFA for all users  
- Use dynamic groups for automated provisioning  
- Apply least‑privilege role assignments  
- Use naming conventions for UPN and display names  
- Automate onboarding/offboarding with PowerShell or identity governance  
- Enable SSPR for all users  
