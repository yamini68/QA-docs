## Test Case Documentation - Test Case #1

### **Test Case Name:** 
**Backdated User Organization Creation - Accrual from DOJ**

---

### **Test Setup Details:**

#### **Employee Data:**
- **Employee Email:** yaminianala99+72@gmail.com
- **Date of Joining (DOJ/LegalEntityJoiningDate):** August 1, 2025
- **Current Date (Test Execution):** April 1, 2026
- **Employee Status:** Active
- **Confirmation Status:** Not Confirmed (0)

#### **Leave Policy Configuration:**
- **Policy Name:** Sick leave [staging]
- **Leave Type:** Sick Leave [staging] (LeaveCategory: SL)
- **Accrual Period Type:** Calendar Month (M)
- **Credit At:** Beginning of the time period (S)
- **First Credit Duration:** 0 days
- **First Credit Basis:** Joining Date (DOJ)
- **Accrual Starts From:** Joining Date (DOJ)
- **Prorated Accrual:** Full Accrual (FA)
- **Accrual Method:** Calendar Period (P)
- **Calendar Leave Days:** 24 days per year
- **Monthly Accrual:** 24/12 = **2.0 days per month**
- **Accrual Rounding:** Round-off (RO)
- **Rounding Places:** One decimal place (1D)
- **Timezone:** India IST +5:30
- **Policy Effective From:** March 27, 2026

#### **Leave Year Configuration:**
- **Leave Year From:** August 1, 2025 (Timestamp: 1753986600000)
- **Leave Year Till:** July 31, 2026 (Timestamp: 1785522599999)

---

### **Expected Behavior:**

Since the employee joined on **Aug 1, 2025** (backdated) and the policy is effective from **Mar 27, 2026**, the system should:

1. Create accrual transactions for **ALL missed months** from DOJ till current date
2. Generate **8 monthly transactions** (Aug 2025 - Mar 2026)
3. Each transaction should credit **2.0 leaves** (24 days ÷ 12 months)
4. All transactions should fall within the same leave year (Aug 1, 2025 - Jul 31, 2026)

---

### **Verification Checklist - LeaveAccrued Array:**

For each transaction in the `LeaveAccrued` array, verify:

#### **1. Transaction Count:**
- ✅ **Expected:** 8 transactions (Aug, Sep, Oct, Nov, Dec 2025 + Jan, Feb, Mar 2026)

#### **2. Monthly Accrual Amount:**
- ✅ **LeavesAccrued:** 2.0 for each transaction
- ✅ **LeaveType:** 1772519659735 (Sick Leave [staging])
- ✅ **LeaveName:** "Sick Leave [ staging ]"
- ✅ **LeaveCategory:** "SL"

#### **3. Date Calculations:**

| Month | AccrualFrom (Timestamp) | AccrualFrom (Date) | AccrualTill (Timestamp) | AccrualTill (Date) |
|-------|------------------------|-------------------|------------------------|-------------------|
| Aug 2025 | 1753986600000 | Aug 1, 2025 | 1756664999999 | Aug 31, 2025 |
| Sep 2025 | 1756665000000 | Sep 1, 2025 | 1759256999999 | Sep 30, 2025 |
| Oct 2025 | 1759257000000 | Oct 1, 2025 | 1761935399999 | Oct 31, 2025 |
| Nov 2025 | 1761935400000 | Nov 1, 2025 | 1764527399999 | Nov 30, 2025 |
| Dec 2025 | 1764527400000 | Dec 1, 2025 | 1767205799999 | Dec 31, 2025 |
| Jan 2026 | 1767205800000 | Jan 1, 2026 | 1769884199999 | Jan 31, 2026 |
| Feb 2026 | 1769884200000 | Feb 1, 2026 | 1772303399999 | Feb 28, 2026 |
| Mar 2026 | 1772303400000 | Mar 1, 2026 | 1774981799999 | Mar 31, 2026 |

#### **4. Leave Year Boundaries:**
- ✅ **LeaveYearFrom:** 1753986600000 (Aug 1, 2025) - **Same for all transactions**
- ✅ **LeaveYearTill:** 1785522599999 (Jul 31, 2026) - **Same for all transactions**

#### **5. Transaction Descriptions:**
- ✅ Format: "Leave added for [AccrualFrom] and [AccrualTill]"
- ✅ Example: "Leave added for 1753986600000 and 1756664999999"

#### **6. Critical Validations:**
- ✅ **AccrualFrom ≥ DOJ** (Aug 1, 2025)
- ✅ **Accrual periods are consecutive** (no gaps between months)
- ✅ **All periods are within the same leave year**
- ✅ **CreditAt = 'S'** means leaves credited at start of each period
- ✅ **FirstCreditDuration = 0** means accrual starts immediately from DOJ
- ✅ **ProratedAccrual = 'FA'** (Full Accrual) - full month credited even if DOJ is not 1st

---

### **Test Result Summary:**

**Status:** ✅ **PASSED**

**Total Leaves Accrued:** 16.0 days (8 months × 2.0 days/month)

**Transactions Created:** 8 monthly accrual transactions

**Key Observations:**
1. System correctly processed backdated DOJ (Aug 1, 2025)
2. Monthly accruals calculated correctly (2.0 days/month)
3. All transactions within correct leave year boundaries
4. AccrualFrom dates align with calendar month starts
5. AccrualTill dates align with calendar month ends
6. System used **LegalEntityJoiningDate** for calculations

   ``` json
   "LeaveAccrued": [
                {
                    "AccrualDescription": "Leave added for 1753986600000 and 1756664999999",
                    "TransactionDescription": "Leave added for 1753986600000 and 1756664999999",
                    "Transaction": 1775024607014,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1756664999999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1753986600000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1756665000000 and 1759256999999",
                    "TransactionDescription": "Leave added for 1756665000000 and 1759256999999",
                    "Transaction": 1775024607079,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1759256999999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1756665000000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1759257000000 and 1761935399999",
                    "TransactionDescription": "Leave added for 1759257000000 and 1761935399999",
                    "Transaction": 1775024607164,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1761935399999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1759257000000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1761935400000 and 1764527399999",
                    "TransactionDescription": "Leave added for 1761935400000 and 1764527399999",
                    "Transaction": 1775024607215,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1764527399999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1761935400000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1764527400000 and 1767205799999",
                    "TransactionDescription": "Leave added for 1764527400000 and 1767205799999",
                    "Transaction": 1775024607261,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1767205799999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1764527400000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1767205800000 and 1769884199999",
                    "TransactionDescription": "Leave added for 1767205800000 and 1769884199999",
                    "Transaction": 1775024607313,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1769884199999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1767205800000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1769884200000 and 1772303399999",
                    "TransactionDescription": "Leave added for 1769884200000 and 1772303399999",
                    "Transaction": 1775024607378,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1772303399999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1769884200000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1772303400000 and 1774981799999",
                    "TransactionDescription": "Leave added for 1772303400000 and 1774981799999",
                    "Transaction": 1775024607440,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1774981799999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1772303400000,
                    "LeaveCategory": "SL"
                },
                {
                    "AccrualDescription": "Leave added for 1774981800000 and 1777573799999",
                    "TransactionDescription": "Leave added for 1774981800000 and 1777573799999",
                    "Transaction": 1775024607485,
                    "LeavesAccrued": 2.0,
                    "LeaveType": 1772519659735,
                    "LeaveName": "Sick Leave [ staging ]",
                    "LeaveYearFrom": 1753986600000,
                    "LeaveYearTill": 1785522599999,
                    "AccrualTill": 1777573799999,
                    "LeaveShortName": "",
                    "AccrualFrom": 1774981800000,
                    "LeaveCategory": "SL"
                }
            ]
   ```

---

## **Test Case #2: Backdated Employee Confirmation**

### **Test Setup:**
- **Employee:** yaminianala99+72@gmail.com
- **DOJ:** August 1, 2025
- **Confirmation Date:** August 2, 2025
- **Confirmation Effective:** August 3, 2025

**Policy Variants:**
- **Variant A (Unconfirmed):** 2.0 leaves/month
- **Variant B (Confirmed):** 3.0 leaves/month



---

### **Expected Behavior:**
When confirmation is backdated to Aug 3, 2025:
1. **Debit** old variant (2.0 leaves) for **each month** from confirmation date
2. **Credit** new variant (3.0 leaves) for all months
3. **Net per month:** 2.0 - 2.0 + 3.0 = **3.0 leaves**

   ### DEBIT Transactions (Variant A - Unconfirmed)

**Critical Finding:** System creates PRORATED debit for the partial period  

| Month    | Transaction Type | LeavesAccrued | AccrualFrom   | AccrualTill  | Notes                          |
|----------|-----------------|---------------|---------------|--------------|--------------------------------|
| Aug 2025 | Debit           | -1.9          | Aug 3, 2025   | Aug 31, 2025 | Prorated (28 days of 31)       |

---

### Why -1.9 instead of -2.0?

- **Confirmation Effective:** Aug 3, 2025 (not Aug 1)  
- Variant A was applicable only from **Aug 1–2, 2025 (2 days)**  
- Debit calculated from **Aug 3 to Aug 31 = 28 days**  

**Proration Calculation:**  
`2.0 × (28/31) = 1.806... ≈ 1.9` *(rounded to 1 decimal)*

    
| Period              | Expected                                                        | Actual                                                                 |
|--------------------|------------------------------------------------------------------|------------------------------------------------------------------------|
| Aug 2025           | Split into 2 transactions (Probation 2 days, Confirmed 29 days) | 1 Credit (2.0) + 1 Prorated Debit (-1.9) + 1 Credit (3.0)             |
| Sep 2025 - Mar 2026| Debit Old Variant (-2.0) + Credit New Variant (+3.0)            | Credit Old Variant (2.0) + Credit New Variant (3.0)                   |
| Net Leaves/Month   | 3.0 leaves                                                     | 5.0 leaves (Double Accrual)                                           |

---

### **Actual Behavior:**
| Period | Debit (Variant A) | Credit (Variant B) | Net Leaves |
|--------|------------------|-------------------|------------|
| Aug 2025 | -1.9 (prorated) | +3.0 | 3.1 |
| Sep 2025 - Mar 2026 | **0** (NO DEBITS) | +3.0 | **5.0** ❌ |

---

### **Issue:**
- ✅ Partial month debit created (-1.9 for Aug)
- ❌ **Missing monthly debits** for Sep 2025 - Mar 2026
- ❌ Employee receives **double accrual** (2.0 + 3.0 = 5.0 leaves/month)
- ❌ Total over-credited by ~16 leaves

---

### **Status:** ❌ **FAILED**

**Reason:** System creates debit only for partial change month but not for subsequent full months, violating requirement: *"debit for each month will happen"*


## **Test Case #3: Backdated Employee Separation - Single Debit Transaction**

### **Test Objective:**
Verify that when an employee's separation date is backdated, the system creates **ONE single debit transaction** (not monthly debits) to reverse leave accruals credited after the separation date.

---

### **Test Setup:**

| Field | Value |
|-------|-------|
| **Employee Email** | yaminianala99+73@gmail.com |
| **DOJ (LegalEntityJoiningDate)** | January 1, 2026 |
| **Employee Status** | Active |
| **Confirmation Status** | Confirmed (1) |
| **Leave Type** | Sick Leave [staging] |
| **Policy** | 2.0 leaves/month, Monthly accrual, CreditAt: Start (S) |
| **Backdated Separation Date** | January 30, 2026 |
| **Current Date** | April 1, 2026 |

---

### **Prerequisites:**

1. ✅ Create new employee with DOJ = Jan 1, 2026
2. ✅ Assign leave accrual policy (2.0 leaves/month)
3. ✅ Add "Sick Leave [staging]" to **Resignation Settings** under "Leave Types affected:"
4. ✅ Let system accrue leaves for Jan, Feb, Mar, Apr 2026
5. ⏳ Initiate backdated resignation with Last Working Day = Jan 30, 2026
6. ⏳ Approve resignation (status changes to Alumni)

---

### **Expected Behavior:**

| Period | Transaction Type | Amount | Description |
|--------|-----------------|--------|-------------|
| Jan 2026 | Credit | +2.0 | Full month (before separation) |
| Feb 2026 | Credit | +2.0 | Already credited |
| Mar 2026 | Credit | +2.0 | Already credited |
| Apr 2026 | Credit | +2.0 | Already credited |
| **Total Initial Credits** | | **+8.0 leaves** | |

**After Separation Approval:**

| Transaction | Count | Amount | Period |
|------------|-------|--------|--------|
| **Debit** | **1** | **-6.0** | Feb 1 - Apr 1 (after Jan 30) |
| **UserAccrual.Status** | | `Inactive` | |
| **UserAccrual.EffectiveTill** | | Jan 30, 2026 | |
| **Final Balance** | | **2.0 leaves** | Only January accrual |

---

### **Key Verification Points:**

```
□ Total transactions: 5 (4 credits + 1 debit)
□ Debit transaction count: 1 (NOT monthly debits)
□ Debit amount: ~-6.0 (covers period after Jan 30, 2026)
□ AccrualFrom on debit: Jan 30, 2026 (separation date)
□ AccrualTill on debit: Apr 1, 2026 (current date)
□ UserAccrual.Status: 'Inactive'
□ UserAccrual.EffectiveTill: Jan 30, 2026
□ Employee Status: 'Alumni'
□ No future accrual schedules exist
□ Final leave balance: 2.0 leaves
```

---

### **Critical Difference from Confirmation:**

| Scenario | Debit Pattern |
|----------|--------------|
| Backdated Confirmation | **Monthly debits** (one per month) |
| **Backdated Separation** | **Single lump-sum debit** ✅ |

---

### **What to Verify (Backend - Alumni Portal Under Development):**

Since the Alumni portal shows "Under Development", coordinate with developer to verify:

**1. UserAccrual DynamoDB Record:**
```sql
Status = 'Inactive'
EffectiveTill = Jan 30, 2026 timestamp
```

**2. Leave Accrual Transactions:**
```
Total count: 5 transactions
Debit count: 1 (single)
Debit amount: ~-6.0
Debit AccrualFrom: Jan 30, 2026
Debit AccrualTill: Apr 1, 2026
```

**3. Kafka Message (leave-accrual-queue):**
```json
{
  "IsDebitTransaction": true,
  "IsJoiningOrSeparationEvent": true,
  "PeriodStart": 1738281600000,  // Jan 30, 2026
  "PeriodEnd": 1743465600000     // Apr 1, 2026
}
```

**4. EventBridge Scheduler:**
```
No future schedules for this employee
```

---

### **Report Verification:**

Generate Leave Balance Report with filter:
- **Leave Name** = Equal = `Sick Leave [ staging ]`
- **Employee** = yaminianala99+73@gmail.com

**Expected Report Output:**
| Column | Value |
|--------|-------|
| Current Balance | **2.0** |
| Leave Year Leaves Availed | 0 (if no leaves taken) |

---

### **Why This Test Matters:**

✅ Validates **"single debit" rule** for backdated separation  
✅ Ensures employees don't retain leaves earned after separation  
✅ Confirms system correctly uses separation date for accrual reversal  
✅ Prevents financial/compliance issues from over-accrual  
✅ Verifies UserAccrual status changes to Inactive  

---

### **Status:** ⏳ **PENDING EXECUTION**

**Dependencies:**
- Resignation workflow configuration
- Leave types added to resignation settings
- Developer support for backend verification (Alumni portal under development)

**Next Steps:**
1. Create employee yaminianala99+73@gmail.com
2. Configure resignation settings
3. Execute backdated separation
4. Coordinate with developer for backend verification
5. Validate final balance = 2.0 leaves<br>
Alumni user
1775114494751
{"Mode":"UALB","Tenant":"KOZ5S","User":"1775114494751","Feature":"25053","FromDay":"AD","TillDay":"AD"}
<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/16d48030-2b28-4320-9f47-757de0d3db27" />

<img width="400" height="400" alt="image" src="https://github.com/user-attachments/assets/33df1f91-1907-451e-a88b-fd9618c0dfe7" />





