 **ORDER OF CALCULATION** when BOTH rules are configured:

## 📊 Calculation Sequence

**Initial Situation:**
- Brought from last year: **5 leaves**
- Unused from this year: **10 leaves**
- **Total balance: 15 leaves**

**Configuration:**
```
CurrentLeaveYearCarryover: 8 (Action: L = Lapse)
TotalBalance: 12 (Action: E = Encash)
```

---

## ✅ Step-by-Step Calculation

### **STEP 1: Apply Current Year Rule FIRST**
```
Rule: CurrentLeaveYearCarryover = 8

Unused leaves from THIS year: 10
Limit: 8
Excess: 10 - 8 = 2 leaves
Action: LAPSE ❌

Result after Step 1:
- Leaves from this year: 8 (reduced from 10)
- Leaves from last year: 5 (unchanged)
- NEW Total: 8 + 5 = 13 leaves
- Lapsed so far: 2 leaves
```

### **STEP 2: Apply Total Balance Rule SECOND**
```
Rule: TotalBalance = 12

Current total balance: 13
Limit: 12
Excess: 13 - 12 = 1 leaf
Action: ENCASH 💰

Result after Step 2:
- Final carryover: 12 leaves
- Encashed: 1 leaf
```

---

## 📋 Final Summary

| Action | Leaves |
|--------|--------|
| **Initial total** | 15 |
| **After Step 1 (Current Year Rule)** | 13 (2 lapsed) |
| **After Step 2 (Total Balance Rule)** | 12 (1 encashed) |
| **Final Carryover** | **12 leaves** |
| **Total Lapsed** | **2 leaves** |
| **Total Encashed** | **1 leaf** |

---

## 🎯 Key Point

**Order matters!** The system applies:
1. **CurrentLeaveYearCarryover rule FIRST** (targets only this year's leaves)
2. **TotalBalance rule SECOND** (targets the overall balance)

This makes sense because:
- You first limit what can be carried from THIS year
- Then you apply the overall cap on the total balance

# What Happens When Limit > Actual Balance? 🎯

## Short Answer: **Nothing special happens - employee keeps ALL their leaves!** ✅

When the carryover limit is **higher** than what the employee actually has, the rule simply **doesn't apply** (no action needed).

---

## 📊 Simple Examples

### Example 1: Current Year Leave Limit
```
Employee's unused leaves THIS year: 10
Rule: CurrentLeaveYearCarryover: 15 (Action: L)

Result: 10 < 15 ✅ (within limit)
→ All 10 leaves carry forward
→ 0 leaves lapse
→ 0 leaves encashed
```

### Example 2: Total Balance Limit
```
Employee's TOTAL balance: 12 leaves
Rule: TotalBalance: 20 (Action: E)

Result: 12 < 20 ✅ (within limit)
→ All 12 leaves carry forward
→ 0 leaves encashed
→ No payment created
```

### Example 3: Both Rules (But Both Limits Higher)
```
Employee Situation:
- Unused this year: 8 leaves
- Brought from last year: 5 leaves
- Total: 13 leaves

Rules:
- CurrentLeaveYearCarryover: 12 (Action: L)
- TotalBalance: 20 (Action: E)

Step 1: Current Year Check
→ 8 < 12 ✅ → All 8 carry, 0 lapse

Step 2: Total Balance Check  
→ 13 < 20 ✅ → All 13 carry, 0 encash

Final Result: 13 leaves carry to next year ✅
```

---

## 🧪 Tester's Checklist for This Scenario

### ✅ What to Verify:

| Field | Expected Value | Why |
|-------|---------------|-----|
| `LeaveYearLeavesLapsed` | 0 | No excess to lapse |
| `LeaveYearLeavesEncashed` | 0 | No excess to encash |
| `BroughtOverLeavesLapsed` | 0 | No excess to lapse |
| `BroughtOverLeavesEncashed` | 0 | No excess to encash |
| `CarryoverBalance` | = Total unused balance | All leaves carried |

### ✅ Sample JSON Response:
```json
{
  "UnusedBalance": 10,
  "BroughtOverBalance": 5,
  "TotalBalance": 15,
  "LeaveYearLeavesLapsed": 0,
  "LeaveYearLeavesEncashed": 0,
  "BroughtOverLeavesLapsed": 0,
  "BroughtOverLeavesEncashed": 0,
  "CarryoverBalance": 15
}
```

---

## 💡 Simple Rule to Remember:

> **"Limits only apply when you EXCEED them. If you're under the limit, you keep everything."**

Think of it like a luggage allowance:
- Airline allows 25kg ✅
- Your bag weighs 15kg ✅
- Result: No extra fees, you just board! ✈️

---

## 🎯 Edge Cases to Test:

| Test Case | Setup | Expected Result |
|-----------|-------|----------------|
| **TC1: Limit much higher** | Unused: 5, Limit: 50 | All 5 carry, 0 lapse/encash |
| **TC2: Limit exactly equal** | Unused: 10, Limit: 10 | All 10 carry, 0 lapse/encash |
| **TC3: Zero balance** | Unused: 0, Limit: 10 | 0 carry, 0 lapse/encash (no crash) |
| **TC4: Both rules, both higher** | Current: 8/12, Total: 13/20 | All carry, no actions |
| **TC5: One rule applies, one doesn't** | Current: 15/10 (exceeds), Total: 13/20 (under) | Current year rule applies (5 lapse/encash), total rule ignored |

---

## 🔄 Visual Flow:

```
Employee Balance: 10 leaves
        │
        ▼
Rule Limit: 15 leaves
        │
        ▼
Is 10 > 15? → NO ❌
        │
        ▼
✅ No action needed
✅ All 10 leaves carry forward
✅ Lapsed = 0, Encashed = 0
```

---

> 🎯 **Bottom Line for Testers**:  
> When limits are higher than actual balance, the system should do **nothing special** - just carry forward all leaves. This is actually the **easiest test case** because there's no calculation, no lapse, no encashment - just a clean pass-through.

Need me to create test data or API payloads for this "limit higher" scenario? 😊
