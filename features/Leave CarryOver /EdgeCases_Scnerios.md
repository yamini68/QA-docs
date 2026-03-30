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

**Ready to execute?** Start with Step 1! 😊
