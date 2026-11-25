# **Active Directory | Configuring Contractor Account Expiration**

## **1. Service Request | Impact: Low (one contractor account and access security) | Priority: P4 – Low**

> "Our contractor's account must expire at the end of the year."

The Human Resources department contacted IT Support to ensure that Borey Chan, a temporary sales employee, cannot authenticate to the corporate network after his contract ends.

My objective is to investigate the existing account configuration, configure the required expiration date, and verify that the account will expire automatically.

## **2. Support Ticket**

| Field             | Details                                        |
| ----------------- | ---------------------------------------------- |
| Employee          | Borey Chan                                     |
| Department        | Sales                                          |
| Employment type   | Temporary employee                             |
| Username          | bchan                                          |
| Category          | Account Management                             |
| Request type      | Service Request                                |
| Domain            | CorpNet.local                                  |
| Domain Controller | CorpDC                                         |
| Expiration date   | December 31, 2026                              |
| Security risk     | Unauthorized access after contract termination |

## **3. Troubleshooting Workflow**
<img width="718" height="667" alt="image" src="https://github.com/user-attachments/assets/6cfc77a7-2e5d-42dc-bb43-b46e726dc6b6" />

## **4. Step-by-Step Resolution**

### **Step 1 — Verify the contractor's information**

**Goal:** Confirm the authorized account expiration date before modifying the account.

Review the information provided by Human Resources.

| Information                  | Details           |
| ---------------------------- | ----------------- |
| Full name                    | Borey Chan        |
| Username                     | bchan             |
| Employment type              | Temporary         |
| Organizational Unit          | Sales\TempSales   |
| Last authorized working date | December 31, 2026 |

### **Step 2 — Locate the contractor's account**

**Goal:** Identify the correct Active Directory account.

1. Launch the **CorpDC** virtual machine.
2. Open **Server Manager**.
3. Select **Tools → Active Directory Users and Computers**.
4. Expand `CorpNet.local`.
5. Navigate to the following Organizational Unit:

```
CorpNet.local
└── Sales
    └── TempSales
        └── Borey Chan
```

📸 Screenshot 01 — Contractor account in Active Directory.

### **Step 3 — Investigate the current expiration settings**

**Goal:** Determine whether the account already has an expiration date.

1. Right-click **Borey Chan**.
2. Select **Properties**.
3. Open the **Account** tab.
4. Locate the **Account expires** section.
5. Review the current configuration.

Alternatively, use PowerShell:

```
Get-ADUser -Identity bchan -Properties AccountExpirationDate |
Select-Object Name,AccountExpirationDate
```

**Expected finding:** The account has no expiration date configured.

**Root cause (if confirmed):** The contractor's account has no configured expiration date, creating a potential security risk after contract termination.

📸 Screenshot 02 — Account expiration settings before modification.

### **Step 4 — Configure the account expiration date**

**Goal:** Automatically prevent future authentication after the contractor's authorized employment period.

1. Remain in the **Account** tab.
2. Locate **Account expires**.
3. Select **End of**.
4. Enter `December 31, 2026`.
5. Click **Apply → OK**.

**Borey Chan — Properties**

Active Directory Users and Computers

GeneralAddress

**Account**

Profile

**Account expires**

Never

End of:

December 31, 2026

Illustration of the required configuration.

📸 Screenshot 03 — Configured account expiration date.

### **Step 5 — Verify the configuration**

**Goal:** Confirm that Active Directory saved the correct expiration date.

Reopen the contractor's account properties and verify the expiration settings.

Run the following PowerShell command:

```
Get-ADUser -Identity bchan -Properties AccountExpirationDate |
Select-Object Name,SamAccountName,Enabled,AccountExpirationDate
```

| Verification                           | Expected result   |
| -------------------------------------- | ----------------- |
| Account exists                         | Yes               |
| Username                               | bchan             |
| Account currently enabled              | True              |
| Last authorized date                   | December 31, 2026 |
| Future authentication after expiration | Denied            |

📸 Screenshot 04 — PowerShell verification.

**Important:** Account expiration prevents new authentication after the expiration time. It does not automatically delete the account or immediately terminate existing sessions.

## **5. Ticket Resolution**

| Field             | Resolution                                     |
| ----------------- | ---------------------------------------------- |
| Root cause        | Missing account expiration date, if confirmed  |
| Corrective action | Configured Active Directory account expiration |
| Security control  | Time-based account access                      |
| Verification      | Account properties and PowerShell              |
| Ticket status     | Closed after configuration verification        |

**Resolution notes:**

> Verified the contractor's employment information with HR, configured the Active Directory account to expire at the end of December 31, 2026, and verified the saved configuration.

## **6. Technical Skills Demonstrated**

- Active Directory administration
- Identity and Access Management (IAM)
- Account lifecycle management
- Contractor access management
- Security policy enforcement
- PowerShell verification
- Help Desk ticket resolution

## **7. Business Impact**

This solution reduces the risk of unauthorized network access after a contractor's employment ends and eliminates the need to manually expire the account on the termination date.

**Temporary employment should never result in permanent network access.**

