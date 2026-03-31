
## EDGE CASE SCNERIO -1
Verify that if a leave is **approved before year-end** but the **leave dates are in the next year**, it is counted in the **current year's availed balance** and **not deducted again** from next year's carryover.

---

## 📋 Prerequisites
1.  **Employee:** Use the same test employee (cleaned up).
2.  **Leave Year End:** July 31, 2026 (Staging) or March 31, 2026 (SMBC). *We will use July 31 for staging.*
3.  **Carryover Policy:** Configured with 45-day limit (FullCarryover = D).

---

## 🛠️ Step-by-Step Testing Process

### **Step 1: Setup Employee Balance (Quickly)**
Instead of waiting for accruals, use **HR Leave Adjustment**.

1.  **Add Leaves:**
    *   Go to **HR Leave Adjustment**.
    *   Employee: Test Employee.
    *   Leave Type: Sick Leave [staging].
    *   Days: **+50 leaves** (Enough to test the 45-day limit).
    *   Status: **Approved**.
2.  **Verify Balance:**
    *   Check Leave Balance Card → Should show **50 leaves** (plus any natural accruals).

---

### **Step 2: Create Future-Dated Leave Request**
This is the core of the edge case.

1.  **Apply Leave:**
    *   Go to **Leave Request**.
    *   Leave Type: Sick Leave.
    *   **Leave From:** August 1, 2026 (First day of NEXT leave year).
    *   **Leave To:** August 5, 2026 (5 days in NEXT leave year).
    *   **Apply Date:** July 30, 2026 (BEFORE year-end).
2.  **Approve Leave:**
    *   Manager approves the request.
    *   **Approval Date:** July 31, 2026 (BEFORE midnight year-end trigger).
    *   **Status:** Completed ('C').
    *   **LastAction:** Approve ('AP').

> ⚠️ **Critical:** Ensure the **Transaction Date/Approval Date** is **before** July 31, 2026 midnight.

---

### **Step 3: Trigger Year-End Process**
Simulate the year-end closing.

1.  **Run Kafka Trigger:**
    ```bash
    curl --location 'https://r6flmoooy9.execute-api.ap-south-1.amazonaws.com/Stage/kafkaproducer' \
    --header 'Content-Type: application/json' \
    --data '{
        "topic": "leave-year-end",
        "message": "{\"tenantId\":\"KOZ5S\",\"entityId\":1755166851528,\"documentId\":\"KOZ5S:ETY:25005:1755166851528:004\",\"timezone\":\"Asia/Calcutta\",\"scheduleExpression\":\"2026-07-31T23:59:59\",\"startYear\":2025}"
    }'
    ```
2.  **Wait:** 2-5 minutes for processing.

---

### **Step 4: Verify Leave Carryover Transaction (Backend)**
Check if the future-dated leave was counted in the current year.

1.  **Run getData Query:**
    ```json
    {
      "function": "getData",
      "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Header.DocumentID^Fields.LeaveYearLeavesAvailed^Fields.UnusedBalance^Fields.CarryoverBalance^Fields.LeaveYearLeavesLapsed], [0^50]"
    }
    ```
2.  **Check Values:**
    *   **LeaveYearLeavesAvailed:** Should **INCLUDE** the 5 future-dated leaves.
        *   *Example:* If you took 0 leaves earlier + 5 future leaves = **5**.
    *   **UnusedBalance:** Should be reduced by these 5 leaves.
        *   *Calculation:* 50 (Total) - 5 (Availed) = **45**.
    *   **CarryoverBalance:** Should respect the 45-day limit.
        *   *Expected:* **45** (Since 45 ≤ 45 limit, 0 lapse).
    *   **LeaveYearLeavesLapsed:** **0**.

---

### **Step 5: Verify Next Year's Balance (Critical Check)**
Ensure the leave isn't deducted **twice**.

1.  **Check Leave Balance Card (After Year-End):**
    *   **Year Start Balance:** Should be **45** (The CarryoverBalance).
    *   **Availed So Far:** Should show **0** for the new year (because the 5 days were counted in the *previous* year).
    *   **Total Balance:** Should be **45**.

2.  **Wait for Leave Date (August 1, 2026):**
    *   When the employee actually takes the leave (Aug 1-5), the system should **NOT deduct** from the 45 carried leaves again.
    *   *Note:* You might not be able to wait until August, but you can ask the dev: *"When this leave period starts, will the system deduct these 5 days again from the 45 carried balance?"*

---

## 📊 Expected Results Table

| Field | Expected Value | Why? |
|-------|---------------|------|
| **Leave Request Transaction Date** | Before July 31, 2026 | Approved in current year |
| **Leave Request Leave Dates** | After July 31, 2026 | Future-dated leave |
| **LeaveYearLeavesAvailed** | Includes 5 days | Counted in current year (per spec) |
| **UnusedBalance** | 45 (50 - 5) | Reduced by future leave |
| **CarryoverBalance** | 45 | Within 45-day limit |
| **LeaveYearLeavesLapsed** | 0 | No excess over limit |
| **Next Year Start Balance** | 45 | Carried forward correctly |
| **Double Deduction?** | **NO** | Should not deduct again in Aug |

---

```json
{
    "ReturnData": [
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Sick Leave [ staging ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 45,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1772519659735,
                "LeaveYearLeavesAdded": 48,
                "LeaveYearLeavesAvailed": 5,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 0,
                "UnusedBalance": 45,
                "LeaveName": "Sick Leave [ staging ]",
                "LeaveYearFrom": 1753986600000,
                "LeaveYearTill": 1785522599999,
                "LeaveYearLeavesAccrued": 2,
                "BroughtOverBalance": 0,
                "BroughtOverLeavesLapsed": 0,
                "CarryoverDate": 1785522599999
            },
            "Header": {
                "Status": "A",
                "FeatureVariantID": 1774598707856,
                "SubjectUser": 1774854441827,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774871363921:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774871363921,
                "InitiatedOn": 1774871363921,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774871363921
            }
        }
    ]
}
```

## 🚨 Potential Bug to Watch For

**Double Deduction Risk:**
*   **Bug:** System counts the 5 days in current year's `LeaveYearLeavesAvailed` (reducing carryover) **AND** deducts them again from next year's balance when August 1st arrives.
*   **Result:** Employee loses 5 days twice!
*   **Verification:** Ask the dev: *"Is there a flag on the leave request transaction that marks it as 'Counted in Previous Year' so it doesn't deduct again?"*

---

## ✅ Summary Checklist

```
□ Balance setup via HR Adjustment (50 leaves)
□ Future-dated leave applied (Aug 1-5)
□ Leave approved BEFORE July 31 midnight
□ Year-end triggered via Kafka
□ getData shows LeaveYearLeavesAvailed includes 5 days
□ CarryoverBalance is 45 (not 40)
□ Next year start balance is 45
□ Confirm no double deduction logic exists
```

You are absolutely right! Leave management is one of the **most complex modules** in HRMS because it involves time, money, policies, and employee lifecycle events.

Based on the **Leave Yearend Manager** and **Leave Carryover** specs, here are the **critical real-world scenarios** you should test beyond the basic carryover limits.

### EDGE CASE - 2 **Test Scenario: Rejection After Year-End**

```
STEP 1: Verify current state
├─ Carryover Transaction Created: July 31
├─ LeaveYearLeavesAvailed: 5
├─ CarryoverBalance: 9

STEP 2: Reject the leave request
├─ Change Status: 'P' → 'C' (Completed)
├─ LastAction: 'AP' → 'RP' (Rejected)
├─ Date: August 1, 2026 (after year-end)

STEP 3: Check employee's leave balance
├─ Does balance increase by 5 leaves?
├─ Or does it stay at 9 leaves?

STEP 4: Check for adjustment transaction
├─ Is there a NEW transaction that adds back 5 leaves?
├─ Transaction Type: 'A' (Addition)
├─ Description: Something like "Leave reversal - Request rejected"
```

---

## 📋 Expected System Behavior

### **Option A: System Creates Adjustment Transaction** ✅ (Ideal)

```
When leave is rejected after year-end:
1. System detects LastAction changed to 'RP'
2. System creates NEW Leave Adjustment transaction
3. Adds back the 5 leaves to employee's balance
4. Next year balance = 9 + 5 = 14 leaves

Transaction Created:
├─ TransactionType: 'A'
├─ LeaveDays: 5
├─ Description: "Reversal of availed leaves - Request rejected post year-end"
├─ Transaction Date: August 1, 2026
```

### **Option B: System Does Nothing** ❌ (Bug)

```
When leave is rejected after year-end:
1. No adjustment transaction created
2. Employee's balance stays at 9 leaves
3. Employee lost 5 leaves unfairly!

This is a DATA INTEGRITY BUG!
```

### **Option C: Manual HR Adjustment Required** ⚠️ (Workaround)

```
When leave is rejected after year-end:
1. HR must manually add back 5 leaves
2. Via HR Leave Adjustment feature
3. System doesn't automate this

This is a USABILITY ISSUE (extra work for HR)
```

---

## 🐛 Bug to Raise (If Option B or C)

```
BUG: Leave Rejection After Year-End Does Not Restore Balance

Issue:
When a leave request is:
1. Pending ('P') at year-end → Counted as "availed" in carryover
2. Rejected ('RP') AFTER year-end → Balance NOT restored

Result:
- Employee loses leaves unfairly
- CarryoverBalance is incorrect
- No automatic adjustment transaction created

Steps to Reproduce:
1. Apply leave before year-end (Status: Pending)
2. Leave dates are in NEXT leave year
3. Trigger year-end process (leave counted as availed)
4. Reject leave AFTER year-end (LastAction: 'RP')
5. Check employee's next year balance

Expected:
- System should create adjustment transaction
- Rejected leaves should be added back to balance
- Next year balance should reflect the reversal

Actual:
- No adjustment transaction
- Balance remains reduced
- Employee loses leaves

Impact:
- Financial loss to employees
- HR must manually fix each case
- Data integrity issue

Priority: HIGH (Employee compensation issue)
```

---

## 🧪 Test Matrix for This Scenario

| Test Case | Leave Status at Year-End | Action After Year-End | Expected Result |
|-----------|-------------------------|----------------------|-----------------|
| **TC-REJ-01** | Pending ('P') | Rejected ('RP') | 5 leaves added back |
| **TC-REJ-02** | Pending ('P') | Cancelled ('CN') | 5 leaves added back |
| **TC-REJ-03** | Pending ('P') | Approved ('AP') | No change (correct) |
| **TC-REJ-04** | Completed ('C') | Cancelled ('CN') | 5 leaves added back |
| **TC-REJ-05** | Completed ('C') | Rejected ('RP') | 5 leaves added back |

---

## 💡 Questions to Ask Your Dev

```
1. "When a leave request is rejected AFTER year-end, does the system 
   automatically create an adjustment transaction to restore the balance?"

2. "Is there a background job that monitors LastAction changes and 
   triggers balance adjustments?"

3. "How does the system handle the gap between year-end snapshot and 
   subsequent leave status changes?"

4. "Should HR manually adjust this, or is it automated?"

5. "Can you show me the code that handles post-year-end leave rejections?"
```

---

## ✅ Quick Verification Steps

**After rejecting the leave, check:**

```
STEP 1: Query leave balance card
├─ Has balance increased from 9 to 14?

STEP 2: Query transactions via getData
├─ Expression: [KOZ5S^TRN^25053^*^A^*^*^EMPLOYEE_ID]
├─ Look for NEW adjustment transaction after August 1
├─ Check if LeaveDays = 5 (the rejected amount)

STEP 3: Check leave request transaction
├─ LastAction should be 'RP'
├─ Workflow.Status should be 'C'

STEP 4: Verify next year's BroughtOverBalance
├─ Should be 14 (not 9)
```

---

> 🎯 **Bottom Line**:  
> **This is a critical edge case!** If the system doesn't automatically restore leaves when a pending leave is rejected after year-end, it's a **HIGH priority bug** because employees lose their entitled leaves. Test this thoroughly and raise a bug if no adjustment transaction is created!


