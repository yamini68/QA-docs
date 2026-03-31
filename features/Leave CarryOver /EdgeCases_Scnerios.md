## 🎯 Objective
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

---

# 🎯 Leave Carryover Policy - Edge Case Configurations

Based on the **Leave Yearend Manager Spec (Page 4-5)**, here's what happens in each scenario:

---

## 📋 Key Distinction: Blank vs Zero (0)

| Value | Meaning |
|-------|---------|
| **Blank** | Rule does **NOT apply** (ignore this rule completely) |
| **Zero (0)** | Rule **DOES apply** and limit is 0 → **ALL leaves lapse/encash, nothing carries** |

> ⚠️ **This is critical!** Blank ≠ 0

---

## 🧪 Scenario 1: Both Fields BLANK + Toggle OFF

```
Configuration:
├─ FullCarryover Toggle: OFF (Disabled)
├─ Total Leave Balance: [BLANK]
├─ Current Year Leaves: [BLANK]
```

### **What Happens:**
```
Per Spec (Page 4-5):
→ "A blank indicates that this rule does not apply"
→ Both rules are IGNORED
→ No carryover limits defined
```

### **Result:**
| Employee Balance | Lapsed | Encashed | Carryover |
|-----------------|--------|----------|-----------|
| 10 leaves | 0 | 0 | 10 |
| 50 leaves | 0 | 0 | 50 |
| 100 leaves | 0 | 0 | 100 |

### **⚠️ Issue:**
```
This is a MISCONFIGURATION!
- Toggle is OFF (meaning limits should apply)
- But both limits are BLANK (meaning no limits apply)
- System behavior is UNCLEAR/UNDEFINED
```

### **Expected System Behavior:**
```
Option A: System treats as FullCarryover = E (all carry)
Option B: System throws validation error (should not allow save)
Option C: System defaults to some hardcoded limit
```
```json
{
    "ReturnData": [
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Full Carryover of excess Sick Leave [ staging ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 2,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1772519659735,
                "LeaveYearLeavesAdded": 0,
                "LeaveYearLeavesAvailed": 0,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 0,
                "UnusedBalance": 2,
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
                "SubjectUser": 1774874204989,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774875144151:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774875144151,
                "InitiatedOn": 1774875144151,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774875144151
            }
        }
    ]
}
```

### **✅ Test Case:**
```
TC-CONFIG-01: Both Fields Blank + Toggle OFF
Setup:
- FullCarryover: D
- TotalBalance: [blank]
- CurrentYearCarryover: [blank]
- Employee Unused: 50 leaves

Expected:
⚠️ System should either:
  1. Show validation error (prevent save)
  2. OR treat as FullCarryover = E (all 50 carry)

Verify:
□ Does system allow saving this config?
□ What happens at year-end?
□ Is there a default behavior?
```

---

## 🧪 Scenario 2: Both Fields = 0

```
Configuration:
├─ FullCarryover Toggle: OFF (Disabled)
├─ Total Leave Balance: 0
├─ Current Year Leaves: 0
├─ Action: Lapsed (for both)
```

### **What Happens:**
```
Per Spec (Page 5):
→ "A zero indicates that the leave balance will either lapse or get encashed"
→ BOTH rules apply with limit = 0
→ ALL leaves should lapse/encash, NOTHING carries forward
```

### **Result:**
| Employee Balance | Lapsed | Encashed | Carryover |
|-----------------|--------|----------|-----------|
| 10 leaves | 10 | 0 | **0** |
| 50 leaves | 50 | 0 | **0** |
| 100 leaves | 100 | 0 | **0** |

### **✅ Test Case:**
```
TC-CONFIG-02: Both Fields = 0
Setup:
- FullCarryover: D
- TotalBalance: 0, Action: L
- CurrentYearCarryover: 0, Action: L
- Employee Unused: 50 leaves

Expected:
✓ LeaveYearLeavesLapsed: 50
✓ CarryoverBalance: 0
✓ Nothing carries to next year

Use Case:
- Leave types that should NOT carry forward (e.g., Compensatory Off)
- "Use it or lose it" policy
```
```json
{
    "ReturnData": [
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Sick Leave [ staging ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 0,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1772519659735,
                "LeaveYearLeavesAdded": 0,
                "LeaveYearLeavesAvailed": 0,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 2,
                "UnusedBalance": 2,
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
                "SubjectUser": 1774877949593,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774879423891:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774879423891,
                "InitiatedOn": 1774879423891,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774879423891
            }
        }
    ]
}

```
---

## 🧪 Scenario 3: Total Balance = 0, Current Year = BLANK

```
Configuration:
├─ FullCarryover Toggle: OFF (Disabled)
├─ Total Leave Balance: 0
├─ Current Year Leaves: [BLANK]
├─ Action: Lapsed
```

### **What Happens:**
```
Per Spec:
→ TotalBalance = 0 → Rule APPLIES, limit is 0
→ CurrentYearCarryover = BLANK → Rule does NOT apply
→ Only TotalBalance rule is evaluated
```

### **Result:**
| Employee Balance | Lapsed | Encashed | Carryover |
|-----------------|--------|----------|-----------|
| 10 leaves | 10 | 0 | **0** |
| 50 leaves | 50 | 0 | **0** |
| 100 leaves | 100 | 0 | **0** |

### **✅ Test Case:**
```
TC-CONFIG-03: TotalBalance = 0, CurrentYear = Blank
Setup:
- FullCarryover: D
- TotalBalance: 0, Action: L
- CurrentYearCarryover: [blank]
- Employee Unused: 50 leaves

Expected:
✓ LeaveYearLeavesLapsed: 50
✓ CarryoverBalance: 0
✓ CurrentYear rule ignored (blank)
```

---

## 🧪 Scenario 4: Total Balance = BLANK, Current Year = 0

```
Configuration:
├─ FullCarryover Toggle: OFF (Disabled)
├─ Total Leave Balance: [BLANK]
├─ Current Year Leaves: 0
├─ Action: Lapsed
```

### **What Happens:**
```
Per Spec:
→ TotalBalance = BLANK → Rule does NOT apply
→ CurrentYearCarryover = 0 → Rule APPLIES, limit is 0
→ Only CurrentYear rule is evaluated
```

### **Result:**
| Employee Balance | Lapsed | Encashed | Carryover |
|-----------------|--------|----------|-----------|
| 10 leaves | 10 | 0 | **0** |
| 50 leaves | 50 | 0 | **0** |
| 100 leaves | 100 | 0 | **0** |

### **✅ Test Case:**
```
TC-CONFIG-04: TotalBalance = Blank, CurrentYear = 0
Setup:
- FullCarryover: D
- TotalBalance: [blank]
- CurrentYearCarryover: 0, Action: L
- Employee Unused: 50 leaves

Expected:
✓ LeaveYearLeavesLapsed: 50
✓ CarryoverBalance: 0
✓ TotalBalance rule ignored (blank)
```

---

## 🧪 Scenario 5: Total Balance = 0, Current Year = 0, Action = Encash

```
Configuration:
├─ FullCarryover Toggle: OFF (Disabled)
├─ Total Leave Balance: 0
├─ Current Year Leaves: 0
├─ Action: Encashed (for both)
```

### **What Happens:**
```
Per Spec:
→ Both rules apply with limit = 0
→ ALL leaves should be ENCASHED (paid), nothing carries
→ Employee gets salary credit for all unused leaves
```

### **Result:**
| Employee Balance | Lapsed | Encashed | Carryover |
|-----------------|--------|----------|-----------|
| 10 leaves | 0 | 10 | **0** |
| 50 leaves | 0 | 50 | **0** |
| 100 leaves | 0 | 100 | **0** |

### **✅ Test Case:**
```
TC-CONFIG-05: Both Fields = 0, Action = Encash
Setup:
- FullCarryover: D
- TotalBalance: 0, Action: E
- CurrentYearCarryover: 0, Action: E
- Employee Unused: 50 leaves

Expected:
✓ LeaveYearLeavesEncashed: 50
✓ LeaveYearLeavesLapsed: 0
✓ CarryoverBalance: 0
✓ Payment transaction created for 50 leaves

Use Case:
- Leave encashment policy (e.g., PL encashment at year-end)
```

---

## 📊 Summary Table

| Scenario | Total Balance | Current Year | Rule Applied | Result |
|----------|--------------|--------------|--------------|--------|
| **1** | Blank | Blank | **NONE** | ⚠️ Undefined (all carry or error) |
| **2** | 0 | 0 | **BOTH** | All lapse/encash, 0 carry |
| **3** | 0 | Blank | **Total Only** | All lapse/encash, 0 carry |
| **4** | Blank | 0 | **Current Only** | All lapse/encash, 0 carry |
| **5** | 0 (E) | 0 (E) | **BOTH** | All encashed, 0 carry |
| **6** | 45 | Blank | **Total Only** | Max 45 carry, excess lapse |
| **7** | Blank | 10 | **Current Only** | Max 10 from current year carry |

---

## ⚠️ Critical Testing Points

### **1. Validation Rules**
```
Question for Dev:
"Does the system prevent saving when:
- FullCarryover = D (OFF)
- Both TotalBalance AND CurrentYearCarryover are BLANK?

This is a logical contradiction:
- Toggle OFF means 'apply limits'
- But both limits are blank means 'no limits'

Should system show validation error?"
```

### **2. Priority When Both Rules Apply**
```
Per Spec (Page 5):
When BOTH rules have values:
1. First apply CurrentYearCarryover rule → Calculate LeaveYearLeavesLapsed
2. Then apply TotalBalance rule → Calculate BroughtOverLeavesLapsed

Example:
- CurrentYearCarryover: 10
- TotalBalance: 15
- UnusedBalance: 20 (15 brought + 5 current)

Step 1: Current year excess = 5 - 10 = -5 (no lapse)
Step 2: Total excess = 20 - 15 = 5 (5 lapse from brought over)
```

### **3. Zero vs Blank in Database**
```
Question for Dev:
"How is BLANK stored in database?
- NULL?
- Empty string ""?
- -1?

How is 0 stored?
- Number 0?

This matters for the condition check:
if (field == null || field == "") → Rule does not apply
if (field == 0) → Rule applies with limit 0
```

---

## 🎯 Recommended Test Matrix

| TC ID | Total Balance | Current Year | Action | Unused | Expected Lapse | Expected Carry |
|-------|--------------|--------------|--------|--------|----------------|----------------|
| TC-CFG-01 | Blank | Blank | N/A | 50 | 0 (or error) | 50 (or error) |
| TC-CFG-02 | 0 | 0 | L | 50 | 50 | 0 |
| TC-CFG-03 | 0 | 0 | E | 50 | 0 | 0 (50 encashed) |
| TC-CFG-04 | 0 | Blank | L | 50 | 50 | 0 |
| TC-CFG-05 | Blank | 0 | L | 50 | 50 | 0 |
| TC-CFG-06 | 45 | Blank | L | 50 | 5 | 45 |
| TC-CFG-07 | Blank | 10 | L | 50 | 40 | 10 |
| TC-CFG-08 | 10 | 10 | L | 50 | 40 | 10 |

---

> 🎯 **Bottom Line**:  
> **Blank = Rule ignored**, **Zero (0) = Rule applies with 0 limit (all leaves lapse/encash)**. This is the most important distinction to test! Also verify if system validates the contradictory scenario (Toggle OFF + Both Blank).

**Want me to help you create the exact getData queries to verify each scenario?** 😊

## 📅 1. Joining Date Scenarios (Employee Lifecycle)

### **TC-JOIN-01: Mid-Year Joiner (Pro-Rata Accrual)**
```
Setup:
- Leave Year: Aug 1, 2025 → July 31, 2026
- Employee DOJ: Jan 1, 2026 (Mid-year)
- Accrual Policy: Pro-rata (2 leaves/month from DOJ)
- Balance at Year-End: 14 leaves (7 months × 2)
- Carryover Limit: 10 leaves

Expected:
✓ LeaveYearLeavesAccrued = 14 (only from Jan-Jul)
✓ BroughtOverBalance = 0 (First year)
✓ CarryoverBalance = 10 (4 lapse)
✓ System should NOT accrue for Aug-Dec 2025 (before joining)
```

### **TC-JOIN-02: Joined Exactly on Leave Year Start**
```
Setup:
- Employee DOJ: Aug 1, 2025 (Same as Leave Year Start)
- Accrual: Full year (24 leaves)

Expected:
✓ Eligible for full year accrual
✓ No pro-rata calculation issues
```

### **TC-JOIN-03: Joined After Leave Year End**
```
Setup:
- Leave Year End: July 31, 2026
- Employee DOJ: Aug 15, 2026

Expected:
✓ Employee should NOT appear in year-end processing
✓ No carryover transaction created for this employee
```

---

## 🔄 2. Leave Request Workflow Scenarios

### **TC-WF-01: Leave Pending Approval at Year-End**
```
Setup:
- Apply Leave: July 30, 2026 (for dates Aug 1-5)
- Status: Pending ('P') at year-end trigger (July 31 midnight)
- Approved: Aug 2, 2026 (After year-end)

Expected (Per Spec):
✓ Status 'P' is considered IF LastAction is not CN/DE/RP/RI
✓ LeaveYearLeavesAvailed SHOULD include these 5 days (Transaction Date = July 30)
✓ Balance reduced in current year
✓ When approved in Aug, system should NOT deduct again
```
```json
{
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Sick Leave [ staging ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 9,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1772519659735,
                "LeaveYearLeavesAdded": 12,
                "LeaveYearLeavesAvailed": 5,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 0,
                "UnusedBalance": 9,
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
                "SubjectUser": 1774877949593,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774937098458:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774937098458,
                "InitiatedOn": 1774937098459,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774937098458
            }
        }
```

### **TC-WF-02: Leave Cancelled After Year-End Trigger**
```
Setup:
- Apply & Approve Leave: July 30, 2026
- Year-End Trigger: July 31 midnight (Counts leave as availed)
- Cancel Leave: Aug 5, 2026 (LastAction = 'CN')

Expected:
✓ Year-end transaction already created (frozen)
✓ Cancelled leaves should be added back to NEXT year's balance
✓ Check if system creates an adjustment transaction for reversal
```

### **TC-WF-03: Leave Rejected After Year-End Trigger**
```
Setup:
- Apply Leave: July 30, 2026
- Year-End Trigger: July 31 midnight
- Reject Leave: Aug 2, 2026 (LastAction = 'RP')

Expected:
✓ Spec says LastAction 'RP' is EXCLUDED from availed calculation
✓ BUT transaction was already created on July 31
✓ Verify if system corrects the balance in next year
```

---

## 🚪 3. Resignation & Exit Scenarios

### **TC-EXIT-01: Employee Resigns Before Year-End**
```
Setup:
- Employee Resigns: June 30, 2026
- Last Working Day: July 15, 2026
- Year-End: July 31, 2026
- Unused Leaves: 10 days

Expected:
✓ Should these 10 days carry forward? OR
✓ Should they be encashed in Full & Final (FnF)?
✓ Verify if carryover policy applies to resigned employees
✓ Check Applicability: "Active Employees only" might exclude them
```

### **TC-EXIT-02: Employee Resigns After Year-End**
```
Setup:
- Year-End Process: July 31, 2026 (Carryover created)
- Employee Resigns: Aug 15, 2026
- Carryover Balance: 10 leaves

Expected:
✓ 10 leaves carried to next year
✓ Upon resignation, these 10 leaves should be encashed in FnF
✓ Verify next year's carryover doesn't happen for resigned emp
```

---

## ⚙️ 4. Policy & Configuration Scenarios

### **TC-POL-01: Carryover Policy Changed Mid-Year**
```
Setup:
- Jan 1, 2026: Policy says "Full Carryover = E"
- June 1, 2026: Policy changed to "Full Carryover = D, Limit = 10"
- Year-End: July 31, 2026

Expected:
✓ Which policy applies? The one active on July 31?
✓ Verify system picks the latest active policy variant
```

### **TC-POL-02: Employee Moves Between Policy Groups**
```
Setup:
- Jan-Jun 2026: Employee in Dept A (Limit 10 days)
- Jul 2026: Employee transferred to Dept B (Limit 20 days)
- Year-End: July 31, 2026

Expected:
✓ Verify Applicability function picks correct policy based on July 31 status
✓ Check if tenure/age slabs are recalculated on July 31
```

### **TC-POL-03: Multiple Leave Types with Different Rules**
```
Setup:
- Sick Leave: Full Carryover = E (Unlimited)
- Privilege Leave: Full Carryover = D (Limit 45)
- Compensatory Off: Lapse all (Limit 0)

Expected:
✓ Year-end job runs separately for EACH leave type
✓ Verify 3 separate carryover transactions created
✓ Each follows its own policy rules
```

---

## 🛠️ 5. System & Technical Scenarios

### **TC-SYS-01: Re-Running Year-End Job (Idempotency)**
```
Setup:
- Trigger Year-End: July 31 midnight → Transaction Created
- Trigger Year-End AGAIN: Aug 1 morning

Expected:
✓ Should it create a DUPLICATE transaction? (Bug)
✓ OR should it skip/update existing? (Expected)
✓ Spec says: "Get CarryoverBalance from LAST Leave Carryover Transaction"
✓ Verify system checks for existing transaction before creating new one
```

### **TC-SYS-02: Adjustment Made After Year-End Trigger**
```
Setup:
- Year-End Trigger: July 31 midnight
- HR Adjustment: Aug 2, 2026 (+5 leaves)

Expected:
✓ Adjustment should NOT be included in previous year's calculation
✓ Adjustment should appear in NEXT year's balance
✓ Verify Transaction Date logic (Aug 2 is outside leave year)
```

### **TC-SYS-03: Timezone Differences**
```
Setup:
- Tenant Timezone: Asia/Calcutta (IST)
- Employee Location: US (EST)
- Year-End: July 31 23:59:59 IST

Expected:
✓ Spec says: "End of day in the Tenant Time zone"
✓ Verify calculation uses IST, not employee's local time
✓ Leave requests made on July 31 EST (which is Aug 1 IST) should NOT count
```

### **TC-SYS-04: Leap Year (Feb 29)**
```
Setup:
- Leave Year: Jan 1, 2024 → Dec 31, 2024 (Leap Year)
- Accrual: 2 leaves/month

Expected:
✓ February should have 29 days, but accrual is usually per month
✓ Verify no calculation errors due to 366 days vs 365 days
```

---

## 📋 Master Testing Checklist

| Category | Scenario | Priority |
|----------|----------|----------|
| **Joining** | Mid-year joiner (Pro-rata) | 🔴 High |
| **Joining** | Joined after year-end (Exclude) | 🟡 Medium |
| **Workflow** | Leave pending at year-end | 🔴 High |
| **Workflow** | Leave cancelled after year-end | 🟡 Medium |
| **Exit** | Resigned employee (Active check) | 🔴 High |
| **Policy** | Policy changed mid-year | 🟡 Medium |
| **Policy** | Multiple leave types (SL, PL, CO) | 🔴 High |
| **System** | Re-running job (Duplicate check) | 🔴 High |
| **System** | Adjustment after year-end | 🟡 Medium |
| **System** | Timezone validation | 🟢 Low |

---

## 💡 Pro Tips for You

1.  **Focus on "Transaction Date"**: The spec repeatedly says **"where the transaction date is between the start and end of the leave year"**. This is the root of most bugs (vs. Leave Date).
2.  **Check "LastAction"**: Verify that cancelled ('CN'), rejected ('RP'), deleted ('DE') leaves are EXCLUDED from `LeaveYearLeavesAvailed`.
3.  **Test "Active Employees"**: The applicability often says "Active Employees only". Resigned employees should typically be excluded from carryover (they get FnF encashment instead).
4.  **Verify Transaction Type**: Your screenshot showed `'A'`, but Yearend Spec says `'SU'`. Clarify this with dev—it might be a spec mismatch.
5.  **One Transaction Per Year**: Ensure the system doesn't create multiple carryover transactions for the same employee + leave type + year.

---

> 🎯 **Bottom Line**:  
> You've tested the **happy path** (basic carryover). Now focus on **lifecycle events** (joining, resigning), **workflow states** (pending, cancelled), and **system behavior** (re-runs, adjustments). These are where real-world bugs hide!

# 🚨 Critical Edge Case: Leave Rejected AFTER Year-End

This is an **excellent test scenario**! You've identified a real-world gap in the system. Let me break down what happens and what should happen:

---

## 📊 Your Current Situation

```
Timeline:
├─ March 31: Leave Applied (5 days, Aug 11-15)
├─ March 31: Status = Pending ('P')
├─ July 31: Year-End Runs → Counts 5 leaves as "Availed"
├─ August 1: Manager REJECTS the leave (LastAction = 'RP')
└─ Question: What happens to those 5 leaves?
```

**Your Carryover Transaction Shows:**
```
LeaveYearLeavesAvailed: 5 ✅ (Counted the pending leave)
UnusedBalance: 9
CarryoverBalance: 9
```

---

## ⚠️ The Problem

### **What Actually Happens (Current Behavior):**

| Event | Balance Impact |
|-------|---------------|
| Year-End (July 31) | 5 leaves counted as "availed" → Carryover = 9 |
| Leave Rejected (Aug 1) | **NO automatic adjustment** |
| Next Year Start Balance | **9 leaves** (employee lost 5 leaves unfairly!) |

### **What SHOULD Happen (Expected Behavior):**

| Event | Balance Impact |
|-------|---------------|
| Year-End (July 31) | 5 leaves counted as "availed" → Carryover = 9 |
| Leave Rejected (Aug 1) | **System should ADD BACK 5 leaves** |
| Next Year Start Balance | **14 leaves** (9 + 5 rejected leaves returned) |

---

## 🔍 According to the Spec

From **Leave Yearend Manager (Page 3)**:

> "Transactions completed or in progress (Workflow.TransactionStatus= 'C' or 'P') are considered **if the LastAction is not 'CN', 'DE', 'RP', or 'RI'**."

**Key Point:**
- At year-end (July 31): Status = 'P', LastAction ≠ 'RP' → **Counted as availed** ✅
- After rejection (Aug 1): LastAction = 'RP' → **Should NOT have been counted** ❌

**But the carryover transaction is already created and "frozen"! It doesn't auto-update.**

---

## 🎯 What You Need to Test

### **Test Scenario: Rejection After Year-End**

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


