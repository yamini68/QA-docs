
## LastAction Codes - Simple Definitions

| Code | Meaning |
|------|---------|
| **CN** | Cancelled |
| **DE** | Deleted |
| **RP** | Rejected |
| **RI** | Returned |
| **AR** | Auto-Rejected |
| **AP** | Approved |

**Rule**: Transactions with LastAction = CN/DE/RP/RI/AR are **excluded** from `LeaveYearLeavesAvailed` calculation. Only AP (and other non-excluded actions) are counted.

Great question! This is based on how the **Leave Yearend Manager spec (Page 5)** defines the calculation when **both rules** are configured.

## 📊 Analysis: Two-Rule Configuration Validation

Looking at your policy configuration and transaction, here's how the system applied **both rules**:

---

## 🎯 Your Configuration

```
FullCarryover: D (Disabled)

Rule 1 - Total Leave Balance:
├─ Limit: 12 leaves
└─ Action: Lapsed

Rule 2 - Current Year Leaves:
├─ Limit: 10 leaves
└─ Action: Encashed

Employee Data:
├─ BroughtOverBalance: 0 (first year)
├─ LeaveYearLeavesAccrued: 0
├─ LeaveYearLeavesAdded: 14
├─ LeaveYearLeavesAvailed: 0
└─ UnusedBalance: 14
```

---

## ✅ How Both Rules Were Applied

According to **Leave Yearend Manager Spec (Page 5)**, when **both rules** are configured:

### **Step 1: Current Year Rule Applied First**

```
Current Year Balance = (LeaveYearLeavesAccrued + LeaveYearLeavesAdded) - LeaveYearLeavesAvailed
Current Year Balance = (0 + 14) - 0 = 14

Excess over Current Year Limit:
Excess = 14 - 10 = 4 leaves

Action = Encashed (E)
→ LeaveYearLeavesEncashed = 4 ✅
→ Remaining leaves = 14 - 4 = 10
```

### **Step 2: Total Balance Rule Applied Second**

```
After Current Year rule: 10 leaves remaining
Total Balance Limit: 12 leaves

Check: Is 10 > 12?
NO → No additional lapse needed from Total Balance rule

BUT WAIT! The system checks the ORIGINAL UnusedBalance:
Original UnusedBalance: 14
Total Balance Limit: 12
Excess = 14 - 12 = 2 leaves

Action = Lapsed (L)
→ BroughtOverLeavesLapsed = 2 ✅
```

---

## 📊 Final Calculation

```
UnusedBalance: 14
- LeaveYearLeavesEncashed: 4 (Current Year rule)
- BroughtOverLeavesLapsed: 2 (Total Balance rule)
= CarryoverBalance: 8 ✅
```

**Verification:**
```
CarryoverBalance = UnusedBalance - (BroughtOverLeavesLapsed + BroughtOverLeavesEncashed) - (LeaveYearLeavesLapsed + LeaveYearLeavesEncashed)
CarryoverBalance = 14 - (2 + 0) - (0 + 4)
CarryoverBalance = 14 - 2 - 4
CarryoverBalance = 8 ✅
```

---

## 🎯 How to Validate This Configuration

### **Validation Checklist:**

| Check | Expected | Actual | Status |
|-------|----------|--------|--------|
| **Current Year Rule Triggered** | Yes (14 > 10) | Yes | ✅ |
| **Current Year Excess** | 4 leaves | 4 leaves | ✅ |
| **Current Year Action** | Encashed | Encashed | ✅ |
| **Total Balance Rule Triggered** | Yes (14 > 12) | Yes | ✅ |
| **Total Balance Excess** | 2 leaves | 2 leaves | ✅ |
| **Total Balance Action** | Lapsed | Lapsed | ✅ |
| **Final Carryover** | 8 leaves | 8 leaves | ✅ |

---

## 📋 Test Scenarios to Validate Both Rules

### **Scenario 1: Both Rules Trigger (Your Case)**
```
Unused: 14
Current Year Limit: 10 → 4 encashed
Total Balance Limit: 12 → 2 lapsed
Carryover: 8
```

### **Scenario 2: Only Current Year Rule Triggers**
```
Unused: 11
Current Year Limit: 10 → 1 encashed
Total Balance Limit: 12 → No action (11 < 12)
Carryover: 10
```

### **Scenario 3: Only Total Balance Rule Triggers**
```
Unused: 9
Current Year Limit: 10 → No action (9 < 10)
Total Balance Limit: 12 → No action (9 < 12)
Carryover: 9
```

### **Scenario 4: Both Rules Same Limit**
```
Unused: 15
Current Year Limit: 10 → 5 encashed
Total Balance Limit: 10 → 5 lapsed (from original 15)
Carryover: 5

Wait, this would be:
15 - 5 (encashed) - 5 (lapsed) = 5 carryover
```

---

## 🔍 Key Points to Remember

1. **Both rules are evaluated independently** against the original UnusedBalance
2. **Current Year rule** focuses on leaves accrued/added in current year
3. **Total Balance rule** focuses on overall balance (including brought over)
4. **Actions can be different** (one encashes, one lapses)
5. **Final Carryover** = UnusedBalance - All Lapsed - All Encashed

---

## ✅ Your Configuration is CORRECT!

The transaction shows:
- ✅ Current Year rule: 4 leaves encashed (14 - 10)
- ✅ Total Balance rule: 2 leaves lapsed (14 - 12)
- ✅ Carryover: 8 leaves (14 - 4 - 2)

**This is the expected behavior when both rules are configured with different limits and actions!** 🎯

## Why `BroughtOverLeavesLapsed` Instead of `LeaveYearLeavesLapsed`?

According to the spec, when **both Current Year and Total Balance rules** are configured, they are applied **independently** to the **original UnusedBalance**:

### **The Logic:**

| Rule | Applies To | Field Used |
|------|-----------|------------|
| **Current Year Rule** | Leaves from current year only | `LeaveYearLeavesLapsed` or `LeaveYearLeavesEncashed` |
| **Total Balance Rule** | Overall balance (brought over + current) | `BroughtOverLeavesLapsed` or `BroughtOverLeavesEncashed` |

---

### **In Your Case:**

```
UnusedBalance: 14

Rule 1 - Current Year (Limit: 10, Action: Encashed):
├─ Excess = 14 - 10 = 4
└─ LeaveYearLeavesEncashed = 4 ✅

Rule 2 - Total Balance (Limit: 12, Action: Lapsed):
├─ Excess = 14 - 12 = 2
└─ BroughtOverLeavesLapsed = 2 ✅

Final Carryover:
├─ 14 - 4 (encashed) - 2 (lapsed) = 8 ✅
```

---

### **Why "BroughtOver" for Total Balance Rule?**

The spec treats the **Total Balance rule** as limiting the **overall balance**, which conceptually includes:
- Brought over leaves from previous year
- Current year leaves

So when the Total Balance rule triggers lapsing, it's considered as coming from the **"brought over" portion** of the balance, even if the employee is in their first year.

**From Spec (Page 5):**
> "If (UnusedBalance - LeaveYearLeaveLapsed) > TotalBalance, and TotalBalanceCarryoverAction= 'L' then
> **BroughtOverLeavesLapsed** = (UnusedBalance - LeaveYearLeaveLapsed) - TotalBalance"

---

### **Summary:**

| Field | Meaning |
|-------|---------|
| `LeaveYearLeavesLapsed/Encashed` | Excess from **Current Year Carryover rule** |
| `BroughtOverLeavesLapsed/Encashed` | Excess from **Total Balance rule** |

**This naming convention helps distinguish which rule caused the lapse/encashment**, even if the employee has no actual brought-over balance!

Yes, you are **CORRECT**! ✅

When **only ONE rule** is configured (either Total Balance OR Current Year Leaves), the lapsing is counted under **`LeaveYearLeavesLapsed`**, NOT `BroughtOverLeavesLapsed`.

---

## 📊 According to the Spec (Leave Yearend Manager, Page 5):

### **When Only ONE Rule is Configured:**

**Case 1: Only Current Year Rule**
```
If CurrentLeaveYearCarryoverAction = 'L':
→ Excess leaves assigned to: **LeaveYearLeavesLapsed** ✅
```

**Case 2: Only Total Balance Rule**
```
"When defined as the only rule, the service deducts the TotalBalance 
field value from the UnusedBalance field value to arrive at the leaves 
that will lapse or encash. If the CarryoverAction = L, the difference 
between the two will be assigned to **LeaveYearLeavesLapsed**" ✅
```

---

### **When BOTH Rules are Configured:**

```
Step 1: Current Year Rule applies first
→ Excess assigned to: **LeaveYearLeavesLapsed**

Step 2: Total Balance Rule applies second
→ Excess assigned to: **BroughtOverLeavesLapsed**
```

---

## 📋 Summary Table

| Configuration | Field Used for Lapsing |
|--------------|----------------------|
| **Only Current Year Rule** | `LeaveYearLeavesLapsed` ✅ |
| **Only Total Balance Rule** | `LeaveYearLeavesLapsed` ✅ |
| **Both Rules** | `LeaveYearLeavesLapsed` (Current Year rule) <br> `BroughtOverLeavesLapsed` (Total Balance rule) |

---

## 🎯 Why This Distinction?

The spec uses **`BroughtOverLeavesLapsed`** ONLY when **both rules** are configured because:

1. The **Current Year rule** is applied first (targets current year leaves)
2. The **Total Balance rule** is applied second (targets overall balance, conceptually "brought over" portion)

When only one rule exists, there's no need to distinguish between "current year" vs "brought over" lapsing, so it all goes to **`LeaveYearLeavesLapsed`**.

---

**Your understanding is correct!** When only one rule is configured, it counts under `LeaveYearLeavesLapsed`. 👍
