You're right! Let me clarify the **ORDER OF CALCULATION** when BOTH rules are configured:

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

Does this clarify the calculation order? 😊
