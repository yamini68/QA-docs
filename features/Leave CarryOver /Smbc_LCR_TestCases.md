## 📊 Current State (March 2026)

```
Employee: Joined Jan 1, 2026
Accrued So Far: 2 leaves (March only)
Yet To Accrue: 8 leaves (April-July)
Expected Year End: 10 leaves (if you wait)

Leave Year: Aug 1, 2025 → July 31, 2026
```

---

## ✅ Simplified Testing Strategy

### **Instead of waiting for accruals, use HR Leave Adjustment:**

**Total leaves needed for full year simulation:**
```
Natural accrual (Jan-Jul): 14 leaves (7 months × 2)
Already accrued: 2 leaves (March)
Remaining to add via HR Adjustment: 12 leaves
```

---

## 🎯 Updated Test Cases with HR Adjustment

### **TC1: Under 45-Day Limit (All Carry Forward)**
```
Setup:
- Natural Accrual: 2 leaves (March - already there)
- HR Adjustment: +12 leaves (simulates Apr-Jul accrual)
- Additional HR Adjustment: +30 leaves (to test various scenarios)
- Total Balance: 44 leaves
- Apply Leaves: 0

Expected at Year-End:
✓ UnusedBalance: 44
✓ CarryoverBalance: 44 (all carry, under 45 limit)
✓ LeaveYearLeavesLapsed: 0
```

---

### **TC2: At Exact 45-Day Limit**
```
Setup:
- Natural Accrual: 2 leaves
- HR Adjustment: +12 leaves (Apr-Jul simulation)
- Additional HR Adjustment: +31 leaves
- Total Balance: 45 leaves
- Apply Leaves: 0

Expected at Year-End:
✓ UnusedBalance: 45
✓ CarryoverBalance: 45 (exactly at limit)
✓ LeaveYearLeavesLapsed: 0
```
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
                "LeaveYearLeavesAdded": 43,
                "LeaveYearLeavesAvailed": 0,
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
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774855227108:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774855227108,
                "InitiatedOn": 1774855227108,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774855227108
            }
        }
    ]
}
```
---

### **TC3: Over 45-Day Limit (Excess Lapses)**
```
Setup:
- Natural Accrual: 2 leaves
- HR Adjustment: +12 leaves (Apr-Jul simulation)
- Additional HR Adjustment: +36 leaves
- Total Balance: 50 leaves
- Apply Leaves: 0

Expected at Year-End:
✓ UnusedBalance: 50
✓ CarryoverBalance: 45
✓ LeaveYearLeavesLapsed: 5 (50 - 45)
```

---

### **TC4: Much Over Limit**
```
Setup:
- Natural Accrual: 2 leaves
- HR Adjustment: +12 leaves (Apr-Jul simulation)
- Additional HR Adjustment: +56 leaves
- Total Balance: 70 leaves
- Apply Leaves: 0

Expected at Year-End:
✓ UnusedBalance: 70
✓ CarryoverBalance: 45
✓ LeaveYearLeavesLapsed: 25 (70 - 45)
```

---

### **TC5: With Leaves Availed - Under Limit**
```
Setup:
- Natural Accrual: 2 leaves
- HR Adjustment: +12 leaves
- Additional HR Adjustment: +40 leaves
- Total Before Availed: 54 leaves
- Apply Leaves: 15 leaves

Expected at Year-End:
✓ UnusedBalance: 39 (54 - 15)
✓ CarryoverBalance: 39 (under 45)
✓ LeaveYearLeavesLapsed: 0
```

---

### **TC6: With Leaves Availed - Over Limit**
```
Setup:
- Natural Accrual: 2 leaves
- HR Adjustment: +12 leaves
- Additional HR Adjustment: +50 leaves
- Total Before Availed: 64 leaves
- Apply Leaves: 10 leaves

Expected at Year-End:
✓ UnusedBalance: 54 (64 - 10)
✓ CarryoverBalance: 45
✓ LeaveYearLeavesLapsed: 9 (54 - 45)
```

---


## 📋 Quick Test Data Table

| Test Case | Natural Accrual | HR Adjustment | Total Added | Availed | Unused | Expected Lapse | Expected Carry |
|-----------|----------------|---------------|-------------|---------|--------|----------------|----------------|
| **TC1** | 2 | +42 | 44 | 0 | 44 | 0 | 44 |
| **TC2** | 2 | +43 | 45 | 0 | 45 | 0 | 45 |
| **TC3** | 2 | +48 | 50 | 0 | 50 | 5 | 45 |
| **TC4** | 2 | +68 | 70 | 0 | 70 | 25 | 45 |
| **TC5** | 2 | +52 | 54 | 15 | 39 | 0 | 39 |
| **TC6** | 2 | +62 | 64 | 10 | 54 | 9 | 45 |

TC1 → DOJ: Jan 1, 2026 | Code: +68  
TC2 → DOJ: Jan 1, 2026 | Code: +67  
TC3 → DOJ: Jan 1, 2026 | Code: +69  
TC4 → DOJ: Jan 1, 2026 | Code: +70  
TC5 → DOJ: Jan 1, 2026 | Code: +71  
TC6 → DOJ: Jan 1, 2026 | Code: +72

---

## 🛠️ Step-by-Step Execution

### **For Each Test Case:**

**Step 1: Create/Reset Employee**
```
- Use same employee or create new one
- DOJ: Jan 1, 2026
- Clear previous test data if reusing
```

**Step 2: Verify Current Balance**
```
- Check leave balance card shows:
  ✓ Added So Far: 2 days (March accrual)
  ✓ Yet To Accrue: 8 days
```

**Step 3: Add HR Leave Adjustment**
```
Navigation: Leave Management → HR Leave Adjustment

Fields:
- Employee: Test employee
- Leave Type: Sick Leave [staging]
- Transaction Type: 'A' (Addition)
- Number of Days: [As per test case from table above]
- Reason: "Testing carryover - [TC#]"
- Effective Date: Any date between Jan-Mar 2026
- Status: Approved

Click: Save/Submit
```

**Step 4: Apply Leave Requests (If Applicable)**
```
- Apply leave requests for specified days
- Status: Approved
- Ensure TransactionStatus = 'C' (Completed)
```

**Step 5: Verify Balance Before Year-End**
```
Check leave balance card shows:
- Total Balance Today: [Expected total]
- Adjustments: [+X days]
- Availed So Far: [-X days if applicable]
```

**Step 6: Configure Carryover Policy**
```
Policy Settings:
✓ FullCarryover: D (Disabled)
✓ Total Leave Balance: 45
✓ Action: Lapsed
✓ Current Year Leaves: (blank)
```

**Step 7: Trigger Year-End via Kafka**
```bash
curl --location 'https://r6flmoooy9.execute-api.ap-south-1.amazonaws.com/Stage/kafkaproducer' \
--header 'Content-Type: application/json' \
--data '{
    "topic": "leave-year-end",
    "message": "{\"tenantId\":\"KOZ5S\",\"entityId\":1755166851528,\"documentId\":\"KOZ5S:ETY:25005:1755166851528:004\",\"timezone\":\"Asia/Calcutta\",\"scheduleExpression\":\"2026-07-31T23:59:59\",\"startYear\":2025}"
}'
```

**Step 8: Wait 2-5 Minutes**

**Step 9: Verify Transaction Created**
```bash
# Use getData query
{
  "function": "getData",
  "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Header.DocumentID^Fields.UnusedBalance^Fields.LeaveYearLeavesLapsed^Fields.CarryoverBalance], [0^50]"
}
```

**Step 10: Validate Results**
```
✓ UnusedBalance matches expected
✓ LeaveYearLeavesLapsed matches expected
✓ CarryoverBalance matches expected
```

---

## 💡 Pro Tips

1. **Use Different Employees** for each test case to avoid data conflicts
2. **Document HR Adjustment Transaction IDs** for debugging
3. **Take Screenshots** of balance card before and after year-end
4. **Start Simple**: Test TC1 first (under limit), then boundary cases (TC2, TC3)
5. **Keep a Tracker**: Note which employee was used for which test case

---

## 📊 Example: Executing TC3

```
1. Employee: TestEmp_003
2. Current Balance: 2 leaves (March accrual)
3. HR Adjustment: +48 leaves
   - Reason: "Testing 45-day carryover limit - TC3"
   - Status: Approved
4. Verify Balance: 50 leaves total
5. Trigger Year-End
6. Expected Result:
   - UnusedBalance: 50
   - LeaveYearLeavesLapsed: 5
   - CarryoverBalance: 45
```

---

# 📋 SMBC Leave Carryover Test Cases

## 🎯 Policy Configuration Summary

| Leave Type | Total Leaves | Carryover Limit | Action on Excess |
|------------|-------------|-----------------|------------------|
| **Casual Leave (CL)** | 14 | **1 leave** | Lapse |
| **Sick Leave (SL)** | 14 | **0 leaves** | Lapse |
| **Privilege Leave (PL)** | 14 | **45 leaves** | Lapse |

---

## ✅ Test Case Matrix

### **TC-CL-01: Casual Leave - 1 Leave Carryover**

```
Setup:
├─ Leave Type: Casual Leave
├─ Policy: FullCarryover = D, TotalBalance = 1, Action = L
├─ Current Balance: 14 leaves
├─ Availed: 0 leaves
└─ Unused Balance: 14 leaves

Expected Carryover Transaction:
├─ UnusedBalance: 14
├─ LeaveYearLeavesLapsed: 13 (14 - 1)
├─ CarryoverBalance: 1 ✅
└─ Next Year Start Balance: 1

Verification Query:
{
  "function": "getData",
  "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Fields.LeaveName^Fields.UnusedBalance^Fields.LeaveYearLeavesLapsed^Fields.CarryoverBalance], [0^50]"
}

Status: [ ] PASS / [ ] FAIL
```

---

### **TC-SL-01: Sick Leave - No Carryover (0 Limit)**

```
Setup:
├─ Leave Type: Sick Leave
├─ Policy: FullCarryover = D, TotalBalance = 0, Action = L
├─ Current Balance: 14 leaves
├─ Availed: 0 leaves
└─ Unused Balance: 14 leaves

Expected Carryover Transaction:
├─ UnusedBalance: 14
├─ LeaveYearLeavesLapsed: 14 (14 - 0)
├─ CarryoverBalance: 0 ✅
└─ Next Year Start Balance: 0

Verification Query:
{
  "function": "getData",
  "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Fields.LeaveName^Fields.UnusedBalance^Fields.LeaveYearLeavesLapsed^Fields.CarryoverBalance], [0^50]"
}

Status: [ ] PASS / [ ] FAIL
```

---

### **TC-PL-01: Privilege Leave - 45 Leave Carryover**

```
Setup:
├─ Leave Type: Privilege Leave
├─ Policy: FullCarryover = D, TotalBalance = 45, Action = L
├─ Current Balance: 14 leaves
├─ Availed: 0 leaves
└─ Unused Balance: 14 leaves

Expected Carryover Transaction:
├─ UnusedBalance: 14
├─ LeaveYearLeavesLapsed: 0 (14 < 45, no excess)
├─ CarryoverBalance: 14 ✅
└─ Next Year Start Balance: 14

Verification Query:
{
  "function": "getData",
  "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Fields.LeaveName^Fields.UnusedBalance^Fields.LeaveYearLeavesLapsed^Fields.CarryoverBalance], [0^50]"
}

Status: [ ] PASS / [ ] FAIL
```

---

## 🧪 Additional Edge Cases

### **TC-CL-02: Casual Leave - At Exact Limit**

```
Setup:
├─ Leave Type: Casual Leave
├─ Policy: TotalBalance = 1
├─ Current Balance: 1 leave (after availing 13)
├─ Unused Balance: 1 leave

Expected:
├─ LeaveYearLeavesLapsed: 0
├─ CarryoverBalance: 1 ✅
└─ All leaves carry (at exact limit)

Status: [ ] PASS / [ ] FAIL
```

---

### **TC-SL-02: Sick Leave - Partial Availed**

```
Setup:
├─ Leave Type: Sick Leave
├─ Policy: TotalBalance = 0
├─ Current Balance: 14 leaves
├─ Availed: 5 leaves
└─ Unused Balance: 9 leaves

Expected:
├─ LeaveYearLeavesLapsed: 9 (9 - 0)
├─ CarryoverBalance: 0 ✅
└─ All unused leaves lapse

Status: [ ] PASS / [ ] FAIL
```

---

### **TC-PL-02: Privilege Leave - Over 45 Limit**

```
Setup:
├─ Leave Type: Privilege Leave
├─ Policy: TotalBalance = 45
├─ Current Balance: 60 leaves (via HR Adjustment)
├─ Availed: 0 leaves
└─ Unused Balance: 60 leaves

Expected:
├─ LeaveYearLeavesLapsed: 15 (60 - 45)
├─ CarryoverBalance: 45 ✅
└─ Excess 15 leaves lapse

Status: [ ] PASS / [ ] FAIL
```

---

## 📊 Combined Test (All 3 Leave Types for Same Employee)

### **TC-ALL-01: Multi Leave Type Carryover**

```
Setup:
├─ Employee: Single test employee
├─ Casual Leave: 14 leaves → Carryover: 1, Lapse: 13
├─ Sick Leave: 14 leaves → Carryover: 0, Lapse: 14
└─ Privilege Leave: 14 leaves → Carryover: 14, Lapse: 0

Expected Summary:
├─ Total Unused: 42 leaves (14+14+14)
├─ Total Lapsed: 27 leaves (13+14+0)
└─ Total Carryover: 15 leaves (1+0+14)

Verification:
Query each leave type separately via getData
Check 3 separate Leave Carryover transactions created

Status: [ ] PASS / [ ] FAIL
```

---

## 🛠️ Execution Steps

### **Step 1: Configure Carryover Policies**

| Leave Type | FullCarryover | TotalBalance | Action |
|------------|--------------|--------------|--------|
| Casual Leave | D | 1 | L |
| Sick Leave | D | 0 | L |
| Privilege Leave | D | 45 | L |

---

### **Step 2: Add Leaves via HR Adjustment**

```
For Each Leave Type:
├─ Go to: HR Leave Adjustment
├─ Employee: Test Employee
├─ Leave Type: [CL/SL/PL]
├─ Transaction Type: 'A' (Addition)
├─ Days: 14
├─ Reason: "SMBC Carryover Testing"
└─ Status: Approved
```

---

### **Step 3: Trigger Year-End**

```bash
curl --location 'https://r6flmoooy9.execute-api.ap-south-1.amazonaws.com/Stage/kafkaproducer' \
--header 'Content-Type: application/json' \
--data '{
    "topic": "leave-year-end",
    "message": "{\"tenantId\":\"KOZ5S\",\"entityId\":1755166851528,\"documentId\":\"KOZ5S:ETY:25005:1755166851528:004\",\"timezone\":\"Asia/Calcutta\",\"scheduleExpression\":\"2026-07-31T23:59:59\",\"startYear\":2025}"
}'
```

---

### **Step 4: Verify via getData**

```json
{
  "function": "getData",
  "expression": "[KOZ5S^TRN^25064^*^A^*^*^EMPLOYEE_ID],[],[Header.DocumentID^Fields.LeaveName^Fields.UnusedBalance^Fields.LeaveYearLeavesLapsed^Fields.CarryoverBalance], [0^50]"
}
```

---

### **Step 5: Check Leave Balance Card**

```
For Each Leave Type, Verify:
├─ Year Start Balance = CarryoverBalance from transaction
├─ Lapsed field shows correct value
└─ Total Balance reflects carryover
```

---

## 📈 Expected Results Summary

| Leave Type | Unused | Limit | Lapsed | Carryover | Next Year Start |
|------------|--------|-------|--------|-----------|-----------------|
| **Casual** | 14 | 1 | **13** | **1** | 1 |
| **Sick** | 14 | 0 | **14** | **0** | 0 |
| **Privilege** | 14 | 45 | **0** | **14** | 14 |

---

```json
{
    "ReturnData": [
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Casual leave LC [ Smbc ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 1,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1774941072901,
                "LeaveYearLeavesAdded": 14,
                "LeaveYearLeavesAvailed": 0,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 13,
                "UnusedBalance": 14,
                "LeaveName": "Casual leave LC [ Smbc ]",
                "LeaveYearFrom": 1753986600000,
                "LeaveYearTill": 1785522599999,
                "LeaveYearLeavesAccrued": 0,
                "BroughtOverBalance": 0,
                "BroughtOverLeavesLapsed": 0,
                "CarryoverDate": 1785522599999
            },
            "Header": {
                "Status": "A",
                "FeatureVariantID": 1774941633189,
                "SubjectUser": 1774940863871,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774941633189:1774942193590:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774942193590,
                "InitiatedOn": 1774942193591,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774942193590
            }
        },
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Sick leave - LC [ smbc ] at the end of the 2025 Leave Year",
                "LeaveYearLeavesPenalised": 0,
                "CarryoverBalance": 0,
                "BroughtOverLeavesEncashed": 0,
                "LeaveYearLeavesEncashed": 0,
                "LeaveType": 1774941045346,
                "LeaveYearLeavesAdded": 14,
                "LeaveYearLeavesAvailed": 0,
                "LeaveYearLeavesDeducted": 0,
                "LeaveYearLeavesLapsed": 14,
                "UnusedBalance": 14,
                "LeaveName": "Sick leave - LC [ smbc ]",
                "LeaveYearFrom": 1753986600000,
                "LeaveYearTill": 1785522599999,
                "LeaveYearLeavesAccrued": 0,
                "BroughtOverBalance": 0,
                "BroughtOverLeavesLapsed": 0,
                "CarryoverDate": 1785522599999
            },
            "Header": {
                "Status": "A",
                "FeatureVariantID": 1774941600663,
                "SubjectUser": 1774940863871,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774941600663:1774942206981:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774942206981,
                "InitiatedOn": 1774942206981,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774942206981
            }
        },
        {
            "Fields": {
                "TransactionType": "A",
                "Description": "Lapsing of excess Sick Leave [ staging ] at the end of the 2025 Leave Year",
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
                "SubjectUser": 1774940863871,
                "ModuleName": "LnA",
                "DocumentID": "KOZ5S:TRN:25064:1774598707856:1774942219181:001",
                "DocumentType": "TRN",
                "FeatureName": "Leave Carryover",
                "TransactionID": 1774942219181,
                "InitiatedOn": 1774942219181,
                "FeatureID": "25064",
                "ModuleID": "25",
                "TenantID": "KOZ5S",
                "TransactionUser": null,
                "MenuOption": "25064",
                "Version": "001",
                "UpdatedOn": null,
                "CreatedOn": 1774942219181
            }
        }
    ]
}

```

## ✅ Pass Criteria

```
□ All 3 Leave Carryover transactions created
□ TransactionType = 'A' for all
□ Description mentions correct leave type and year
□ LeaveYearLeavesLapsed matches expected values
□ CarryoverBalance matches expected values
□ Next year's Year Start Balance = CarryoverBalance
□ No duplicate transactions created
□ UI balance card reflects correct values
```

---

## 🐛 Common Issues to Watch

| Issue | Symptom | Fix |
|-------|---------|-----|
| Wrong policy applied | Carryover doesn't match limit | Check policy applicability |
| No transaction created | Year-end didn't run | Re-trigger Kafka |
| All leaves carry | Policy not saved/active | Verify policy status |
| Balance not updated | UI caching issue | Refresh or wait 5 mins |

---

> 🎯 **Bottom Line**:  
> Test all 3 leave types separately first, then test combined scenario. Verify via **getData** (backend) AND **Leave Balance Card** (UI). Document any mismatches as bugs!

**Ready to execute?** Start with TC-SL-01 (simplest - all lapse), then TC-CL-01 (1 carry), then TC-PL-01 (all carry)! 😊
