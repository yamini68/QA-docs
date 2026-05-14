## What is "Permitted Travel Mode" RuleGroup About?

This RuleGroup **defines which travel modes employees can select for domestic trips** and at what permission level:

- **Permitted** = Standard choice (no justification needed)
- **Exception** = Available but requires mandatory justification  
- **Disallowed** = Completely suppressed/hidden from the form

**Example**: 
- Junior employees: Rail & Road = Permitted, Air = Exception
- Senior employees: Air = Permitted, Rail = Permitted

---

## Is the Layout Correct According to Spec?

**✅ Yes, the layout structure is CORRECT:**

The spec says:
- **RuleType**: `Options` 
- **OptionsLookupList**: `Travel Modes service database`

The modal shows:
1. **"Preferred Mode" dropdown** ← Should list modes from Travel Modes service database (Air, Rail, Road, Sea)
2. **"Permission" dropdown** ← Should show Permitted/Exception/Disallowed

This matches the spec perfectly.

---

## Are the Disabled Dropdowns Correct?

**❌ NO — This is a DATA CONFIGURATION ISSUE**

The error message says:
> *"No values available from lookup list 'Travel Modes service database'"*

**What this means:**
- The dropdowns are disabled/empty because the **Travel Modes service database hasn't been populated**
- This is a **platform-level setup issue**, NOT a UI bug
- The Travel Modes service database is a platform-maintained catalogue that must contain entries like:
  - Air (with classes: Economy, Business, First)
  - Rail (with classes: AC Chair Car, Sleeper, etc.)
  - Road (Bus, Car)
  - Sea

**What needs to happen:**
1. Platform admin must populate the Travel Modes service database
2. Once populated, the dropdown will show available modes
3. Then you can configure rules like:
   - Preferred Mode: `Air` → Permission: `Exception` (for Grade 1-3)
   - Preferred Mode: `Rail` → Permission: `Permitted` (for Grade 1-3)

---

**Bottom line**: Layout ✅ Correct | Dropdowns ❌ Need platform data setup

Great question! Let me explain what **Permitted**, **Exception**, and **Disallowed** actually look like in the user interface:

---

## 🎯 Visual Behavior in the Frontend

### 1️⃣ **Permitted** (Standard Choice)
**What the employee sees:**
- ✅ Option appears **normally** in the dropdown
- ✅ No special marking or warning
- ✅ Can select it directly
- ✅ **No justification field appears**
- ✅ No workflow flag raised

**Example:**
```
Travel Mode dropdown:
┌─────────────────────────┐
│ Select Travel Mode      │
├─────────────────────────┤
│ ✈️ Air                  │ ← Shows normally
│ 🚆 Rail                 │ ← Shows normally  
│ 🚗 Road                 │ ← Shows normally
│ 🚢 Sea                  │
└─────────────────────────┘
```
*Employee clicks "Air" → System accepts it → No questions asked*

---

### 2️⃣ **Exception** (Requires Justification)
**What the employee sees:**
- ⚠️ Option appears in dropdown **with visual indicator** (warning icon, yellow highlight, asterisk)
- ⚠️ When selected → **Mandatory justification text box appears**
- ⚠️ Textbox shows: "Please provide business justification for this selection"
- ⚠️ Cannot submit without filling justification
- ⚠️ Justification is shown to approver in workflow

**Example:**
```
Travel Mode dropdown:
┌─────────────────────────┐
│ Select Travel Mode      │
├─────────────────────────┤
│ ⚠️ Air                  │ ← Yellow/warning icon
│ 🚆 Rail                 │ ← Normal
│ 🚗 Road                 │ ← Normal
└─────────────────────────┘

When employee selects "⚠️ Air":
┌─────────────────────────────────┐
│ ⚠️ Business Justification Required │
│                                   │
│ Please explain why Air travel    │
│ is necessary for this trip:      │
│ ┌─────────────────────────────┐ │
│ │ [Text area - Mandatory *]   │ │
│ └─────────────────────────────┘ │
│                                   │
│ [Cancel]  [Submit]                │
└─────────────────────────────────┘
```

---

### 3️⃣ **Disallowed** (Blocked/Suppressed)
**What the employee sees:**
- ❌ Option is **completely hidden** from dropdown
- ❌ Employee **cannot see it or select it**
- ❌ If somehow submitted via API → System blocks with error

**Example:**
```
Travel Mode dropdown for Junior Employee:
┌─────────────────────────┐
│ Select Travel Mode      │
├─────────────────────────┤
│ 🚆 Rail                 │ ← Only these show
│ 🚗 Road                 │
│ 🚌 Bus                  │
│                         │
│ (Air is NOT visible)    │ ← Completely hidden
└─────────────────────────┘
```

---

## 📊 Real-World Example by Grade

**Scenario:** Grade 1 (Junior) vs Grade 5 (Senior) employee booking travel

### **Grade 1 Employee (Junior):**
```
Travel Mode:
✈️ Air      → ⚠️ Exception (shows with warning + justification)
🚆 Rail     → ✅ Permitted (normal)
🚗 Road     → ✅ Permitted (normal)

Travel Class (if Air selected):
Business    → ❌ Disallowed (hidden)
Economy     → ✅ Permitted (normal)

Hotel Star Rating:
5-Star      → ❌ Disallowed (hidden)
4-Star      → ⚠️ Exception (warning + justification)
3-Star      → ✅ Permitted (normal)
```

### **Grade 5 Employee (Senior):**
```
Travel Mode:
✈️ Air      → ✅ Permitted (normal)
🚆 Rail     → ✅ Permitted (normal)
🚗 Road     → ✅ Permitted (normal)

Travel Class:
Business    → ✅ Permitted (normal)
Economy     → ✅ Permitted (normal)

Hotel Star Rating:
5-Star      → ✅ Permitted (normal)
4-Star      → ✅ Permitted (normal)
3-Star      → ✅ Permitted (normal)
```

---

## 🔄 What Happens at Each Stage?

| Stage | Permitted | Exception | Disallowed |
|-------|-----------|-----------|------------|
| **Form Rendering** | Shows normally | Shows with ⚠️ icon | Hidden completely |
| **Selection** | Click & select | Click → Justification box appears | Cannot select |
| **Submission** | Allowed | Allowed IF justification filled | Blocked |
| **Approver View** | Shows as normal | Shows with justification text | N/A |
| **Workflow** | Normal routing | May route to senior approver | N/A |

---

## 💡 Key Takeaway

Think of it like a **traffic light system**:

- 🟢 **Permitted** = Green light → Go ahead, no questions
- 🟡 **Exception** = Yellow light → You can go, but explain why
- 🔴 **Disallowed** = Red light → Not an option, hidden from view

This allows the same policy to serve different employee groups while keeping the UI clean and guiding employees toward compliant choices!
