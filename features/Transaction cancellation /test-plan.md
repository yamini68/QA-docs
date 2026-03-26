# OOW — Conditional Withdrawal Test Cases

> **Feature:** Out-On-Work (FeatureID: 25037)  
> **Section:** Configure Withdrawal → Conditional Mode  
> **Scope:** All TRN fields × All applicable operators × AND / OR / AND+OR combinations  
> **Reference Docs:** Scenario Manager, OOW Feature Template  

---

## Quick Reference — OOW TRN Fields & Their Data Types

| Field | Data Type | Allowed Operators |
|-------|-----------|-------------------|
| StartDate | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| EndDate | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| Day | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| ExpectedInTime | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| ExpectedOutTime | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| MarkInTime | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| MarkOutTime | TS (Timestamp) | Equal, GreaterThan, GreaterThanEqualTo, LessThan, LessThanEqualTo, IsBetween, IsNotBetween |
| OutOnWorkType | TX (Text) from list [IL, OE, IO, AD] | IsAnyOf, IsNotAnyOf, IsExact, Include, NotInclude, StartWith, EndWith, InThisTransaction, NotInThisTransaction |
| DayType | TX (Text) from list [W, O, H] | IsAnyOf, IsNotAnyOf, IsExact, Include, NotInclude, StartWith, EndWith, InThisTransaction, NotInThisTransaction |
| Reason | TX (Text) | IsAnyOf, IsNotAnyOf, IsExact, Include, NotInclude, StartWith, EndWith |
| Workshift | TX (Text) | IsAnyOf, IsNotAnyOf, IsExact, Include, NotInclude, StartWith, EndWith |
| Entity ID | Header field | Equal, NotEqual |
| Feature Variant ID | Header field | Equal, NotEqual, InThisTransaction |
| Subject User | Header field | Equal, NotEqual, InThisTransaction |
| Transaction ID | Header field | Equal, NotEqual, InThisTransaction |
| Transaction User | Header field | Equal, NotEqual, InThisTransaction |

---

## SECTION 1 — StartDate Field (TS Type)

---

### TC-SD-001 | StartDate — Equal | AND | Positive

**Pre-conditions:**
- OOW request exists with StartDate = today's date (e.g., 2025-01-15)
- Conditional withdrawal configured: `All must be true: StartDate Equal <today>`

**Steps:**
1. Configure withdrawal condition: Field = `StartDate`, Operator = `Equal`, Value = `<today's epoch timestamp>`
2. Submit an OOW request with StartDate = today
3. Navigate to TRN page → My Request
4. Attempt to Withdraw the request

**Expected Result:** Condition evaluates TRUE → Withdraw option is visible and functional

**Test Type:** Positive

---

### TC-SD-002 | StartDate — Equal | AND | Negative

**Pre-conditions:**
- OOW request exists with StartDate = tomorrow
- Condition: `StartDate Equal <today>`

**Steps:**
1. Same condition as TC-SD-001
2. Submit OOW with StartDate = tomorrow
3. Attempt to Withdraw

**Expected Result:** Condition evaluates FALSE → Withdraw option NOT visible or blocked

**Test Type:** Negative

---

### TC-SD-003 | StartDate — GreaterThan | AND | Positive

**Pre-conditions:**
- Condition: `StartDate GreaterThan <yesterday's epoch>`
- OOW StartDate = today

**Steps:**
1. Set condition: Field = `StartDate`, Operator = `GreaterThan`, Value = `<yesterday's epoch>`
2. Submit OOW with StartDate = today
3. Attempt Withdraw

**Expected Result:** today > yesterday → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-SD-004 | StartDate — GreaterThan | AND | Negative

**Pre-conditions:**
- Condition: `StartDate GreaterThan <tomorrow>`
- OOW StartDate = today

**Steps:**
1. Set condition: `StartDate GreaterThan <tomorrow>`
2. Submit OOW with StartDate = today
3. Attempt Withdraw

**Expected Result:** today > tomorrow is FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-SD-005 | StartDate — GreaterThanEqualTo | AND | Positive (Boundary)

**Pre-conditions:**
- Condition: `StartDate GreaterThanEqualTo <today>`
- OOW StartDate = today (exact match)

**Expected Result:** today >= today → TRUE → Withdraw visible

**Test Type:** Positive (Boundary)

---

### TC-SD-006 | StartDate — LessThan | AND | Positive

**Pre-conditions:**
- Condition: `StartDate LessThan <tomorrow>`
- OOW StartDate = today

**Expected Result:** today < tomorrow → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-SD-007 | StartDate — LessThanEqualTo | AND | Boundary

**Pre-conditions:**
- Condition: `StartDate LessThanEqualTo <today>`
- OOW StartDate = today

**Expected Result:** today <= today → TRUE → Withdraw visible

**Test Type:** Positive (Boundary)

---

### TC-SD-008 | StartDate — IsBetween | AND | Positive

**Pre-conditions:**
- Condition: `StartDate IsBetween <first day of month> AND <last day of month>`
- OOW StartDate = any day within current month

**Steps:**
1. Set condition with MinValue = month start epoch, MaxValue = month end epoch
2. Submit OOW with StartDate = mid-month
3. Attempt Withdraw

**Expected Result:** Date falls in range → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-SD-009 | StartDate — IsBetween | AND | Negative (Outside Range)

**Pre-conditions:**
- Same IsBetween condition as TC-SD-008
- OOW StartDate = next month

**Expected Result:** Date outside range → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-SD-010 | StartDate — IsBetween | AND | Boundary (Min Edge)

**Pre-conditions:**
- OOW StartDate = exactly the MinValue (first day of month)

**Expected Result:** Boundary is inclusive → TRUE → Withdraw visible

**Test Type:** Positive (Boundary)

---

### TC-SD-011 | StartDate — IsBetween | AND | Boundary (Max Edge)

**Pre-conditions:**
- OOW StartDate = exactly the MaxValue (last day of month)

**Expected Result:** Boundary is inclusive → TRUE → Withdraw visible

**Test Type:** Positive (Boundary)

---

### TC-SD-012 | StartDate — IsNotBetween | AND | Positive

**Pre-conditions:**
- Condition: `StartDate IsNotBetween <next month start> AND <next month end>`
- OOW StartDate = today (current month)

**Expected Result:** today is NOT in next month → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-SD-013 | StartDate — IsNotBetween | AND | Negative

**Pre-conditions:**
- Same IsNotBetween condition (next month range)
- OOW StartDate = a date inside the excluded range (next month)

**Expected Result:** date IS in the excluded range → FALSE → Withdraw blocked

**Test Type:** Negative

---

## SECTION 2 — EndDate Field (TS Type)

> Same operator set as StartDate. Run parallel test cases with EndDate field.

### TC-ED-001 | EndDate — Equal | AND | Positive
**Condition:** `EndDate Equal <today>` | OOW EndDate = today
**Expected:** TRUE → Withdraw visible

### TC-ED-002 | EndDate — GreaterThan | AND | Positive
**Condition:** `EndDate GreaterThan <yesterday>` | OOW EndDate = today
**Expected:** TRUE → Withdraw visible

### TC-ED-003 | EndDate — LessThan | AND | Negative
**Condition:** `EndDate LessThan <yesterday>` | OOW EndDate = today
**Expected:** FALSE → Withdraw blocked

### TC-ED-004 | EndDate — IsBetween | AND | Positive
**Condition:** `EndDate IsBetween <month start> AND <month end>` | OOW EndDate = mid-month
**Expected:** TRUE → Withdraw visible

### TC-ED-005 | EndDate — IsNotBetween | AND | Negative
**Condition:** `EndDate IsNotBetween <month start> AND <month end>` | OOW EndDate = mid-month
**Expected:** FALSE → Withdraw blocked

---

## SECTION 3 — OutOnWorkType Field (TX Type from List)

OutOnWorkType values: `IL` (In Late), `OE` (Out Early), `IO` (In Late + Out Early), `AD` (All Day)

---

### TC-OWT-001 | OutOnWorkType — IsAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType IsAnyOf [IL, OE]`
- OOW submitted with OutOnWorkType = IL

**Steps:**
1. Configure: Field = `OutOnWorkType`, Operator = `IsAnyOf`, Values = `[IL, OE]`
2. Submit OOW with type = IL
3. Attempt Withdraw

**Expected Result:** IL is in [IL, OE] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-002 | OutOnWorkType — IsAnyOf | AND | Negative

**Pre-conditions:**
- Same condition: `IsAnyOf [IL, OE]`
- OOW type = AD

**Expected Result:** AD not in [IL, OE] → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-003 | OutOnWorkType — IsNotAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType IsNotAnyOf [AD]`
- OOW type = IL

**Expected Result:** IL is NOT in [AD] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-004 | OutOnWorkType — IsNotAnyOf | AND | Negative

**Pre-conditions:**
- Condition: `OutOnWorkType IsNotAnyOf [AD]`
- OOW type = AD

**Expected Result:** AD IS in [AD] → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-005 | OutOnWorkType — IsExact | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType IsExact IO`
- OOW type = IO

**Expected Result:** Exact match → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-006 | OutOnWorkType — IsExact | AND | Negative

**Pre-conditions:**
- Condition: `OutOnWorkType IsExact IO`
- OOW type = IL

**Expected Result:** Not exact match → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-007 | OutOnWorkType — Include | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType Include I` (substring)
- OOW type = IL (contains "I")

**Expected Result:** "IL" includes "I" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-008 | OutOnWorkType — Include | AND | Negative

**Pre-conditions:**
- Condition: `OutOnWorkType Include I`
- OOW type = OE (does NOT contain "I")

**Expected Result:** "OE" does not include "I" → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-009 | OutOnWorkType — NotInclude | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType NotInclude A`
- OOW type = IL

**Expected Result:** "IL" does not include "A" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-010 | OutOnWorkType — StartWith | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType StartWith O`
- OOW type = OE

**Expected Result:** "OE" starts with "O" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-011 | OutOnWorkType — StartWith | AND | Negative

**Pre-conditions:**
- Condition: `OutOnWorkType StartWith O`
- OOW type = IL

**Expected Result:** "IL" does not start with "O" → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-012 | OutOnWorkType — EndWith | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType EndWith L`
- OOW type = IL

**Expected Result:** "IL" ends with "L" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-013 | OutOnWorkType — EndWith | AND | Negative

**Pre-conditions:**
- Condition: `OutOnWorkType EndWith L`
- OOW type = OE

**Expected Result:** "OE" does not end with "L" → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OWT-014 | OutOnWorkType — InThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: `OutOnWorkType InThisTransaction <NotInThis operator>` using StartDate field
- Field: StartDate, Operator: NotInThisTransaction, Value: <blank/any>

**Note:** InThisTransaction checks if field value is present in the current transaction's header fields (FeatureVariantID, TransactionID, SubjectUser, TransactionUser)

**Steps:**
1. Set Field = `StartDate`, Operator = `InThisTransaction`
2. Submit OOW transaction
3. Attempt Withdraw

**Expected Result:** StartDate exists in this transaction → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OWT-015 | OutOnWorkType — NotInThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: Field = `ExpectedInTime`, Operator = `NotInThisTransaction`
- OOW submitted with OutOnWorkType = OE (where ExpectedInTime is blank per spec)

**Expected Result:** ExpectedInTime is blank/absent for OE type → TRUE → Withdraw visible

**Test Type:** Positive (Edge — blank field)

---

## SECTION 4 — DayType Field (TX Type)

DayType values: `W` (Working), `O` (Weekly Off), `H` (Holiday)  
**Note:** DayType is system-set, not user input

---

### TC-DT-001 | DayType — IsAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `DayType IsAnyOf [W]`
- OOW submitted on a Working day → DayType = W

**Expected Result:** W is in [W] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-DT-002 | DayType — IsAnyOf [W, H] | AND | Positive

**Pre-conditions:**
- Condition: `DayType IsAnyOf [W, H]`
- OOW on a Holiday → DayType = H

**Expected Result:** H is in [W, H] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-DT-003 | DayType — IsAnyOf | AND | Negative

**Pre-conditions:**
- Condition: `DayType IsAnyOf [W]`
- OOW on a Weekly Off day → DayType = O

**Expected Result:** O is NOT in [W] → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-DT-004 | DayType — IsNotAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `DayType IsNotAnyOf [H]`
- OOW on Working day → DayType = W

**Expected Result:** W is NOT in [H] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-DT-005 | DayType — IsExact | AND | Positive

**Pre-conditions:**
- Condition: `DayType IsExact W`
- OOW on Working day

**Expected Result:** Exact match W = W → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-DT-006 | DayType — IsExact | AND | Negative (Holiday)

**Pre-conditions:**
- Condition: `DayType IsExact W`
- OOW on Holiday

**Expected Result:** H ≠ W → FALSE → Withdraw blocked

**Test Type:** Negative

---

## SECTION 5 — Reason Field (TX Type)

---

### TC-RN-001 | Reason — IsExact | AND | Positive

**Pre-conditions:**
- Condition: `Reason IsExact Client Visit`
- OOW submitted with Reason = "Client Visit"

**Expected Result:** Exact match → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-002 | Reason — Include | AND | Positive

**Pre-conditions:**
- Condition: `Reason Include Visit`
- OOW Reason = "Client Visit"

**Expected Result:** "Client Visit" contains "Visit" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-003 | Reason — Include | AND | Negative

**Pre-conditions:**
- Condition: `Reason Include Visit`
- OOW Reason = "Field Work"

**Expected Result:** "Field Work" does not contain "Visit" → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-RN-004 | Reason — StartWith | AND | Positive

**Pre-conditions:**
- Condition: `Reason StartWith Client`
- OOW Reason = "Client Visit"

**Expected Result:** Starts with "Client" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-005 | Reason — EndWith | AND | Positive

**Pre-conditions:**
- Condition: `Reason EndWith Work`
- OOW Reason = "Field Work"

**Expected Result:** Ends with "Work" → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-006 | Reason — IsAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `Reason IsAnyOf [Client Visit, Field Work, Training]`
- OOW Reason = "Training"

**Expected Result:** "Training" is in list → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-007 | Reason — IsNotAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `Reason IsNotAnyOf [Personal Work]`
- OOW Reason = "Client Visit"

**Expected Result:** "Client Visit" not in [Personal Work] → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-RN-008 | Reason — NotInclude | AND | Positive

**Pre-conditions:**
- Condition: `Reason NotInclude Personal`
- OOW Reason = "Client Visit"

**Expected Result:** "Client Visit" does not include "Personal" → TRUE → Withdraw visible

**Test Type:** Positive

---

## SECTION 6 — Workshift Field (TX Type)

---

### TC-WS-001 | Workshift — IsAnyOf | AND | Positive

**Pre-conditions:**
- Condition: `Workshift IsAnyOf [Morning Shift, General Shift]`
- OOW Workshift = "Morning Shift"

**Expected Result:** "Morning Shift" is in list → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-WS-002 | Workshift — IsExact | AND | Positive

**Pre-conditions:**
- Condition: `Workshift IsExact Night Shift`
- OOW Workshift = "Night Shift"

**Expected Result:** Exact match → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-WS-003 | Workshift — IsNotAnyOf | AND | Negative

**Pre-conditions:**
- Condition: `Workshift IsNotAnyOf [Night Shift]`
- OOW Workshift = "Night Shift"

**Expected Result:** "Night Shift" IS in excluded list → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-WS-004 | Workshift — Include | AND | Positive

**Pre-conditions:**
- Condition: `Workshift Include Shift`
- OOW Workshift = "Morning Shift"

**Expected Result:** "Morning Shift" contains "Shift" → TRUE → Withdraw visible

**Test Type:** Positive

---

## SECTION 7 — Transaction Header Fields

---

### TC-TH-001 | Subject User — InThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: Field = `Subject User`, Operator = `InThisTransaction`
- EMP applies OOW for themselves → SubjectUser = TransactionUser

**Expected Result:** SubjectUser present in this transaction → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-TH-002 | Transaction User — InThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: Field = `Transaction User`, Operator = `InThisTransaction`
- Any OOW transaction (TransactionUser always present)

**Expected Result:** TransactionUser always populated → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-TH-003 | Feature Variant ID — InThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: Field = `Feature Variant ID`, Operator = `InThisTransaction`
- OOW TRN transaction (DocumentType = TRN, FeatureVariantID present)

**Expected Result:** FeatureVariantID present → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-TH-004 | Feature Variant ID — NotInThisTransaction | AND | Positive

**Pre-conditions:**
- Condition: Field = `Feature Variant ID`, Operator = `NotInThisTransaction`
- OOW submitted with no FeatureVariantID (blank)

**Expected Result:** FeatureVariantID absent → TRUE → Withdraw visible

**Test Type:** Positive (Edge)

---

### TC-TH-005 | Entity ID — Equal | AND | Positive

**Pre-conditions:**
- Condition: `Entity ID Equal <specific EntityID>`
- OOW raised for entity matching the specified ID

**Expected Result:** EntityID matches → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-TH-006 | Entity ID — Equal | AND | Negative

**Pre-conditions:**
- Same condition with specific EntityID
- OOW raised for different entity

**Expected Result:** EntityID does not match → FALSE → Withdraw blocked

**Test Type:** Negative

---

## SECTION 8 — AND Conditions (All Must Be True)

---

### TC-AND-001 | StartDate + OutOnWorkType | Both TRUE

**Pre-conditions:**
- AND Condition 1: `StartDate GreaterThanEqualTo <today>`
- AND Condition 2: `OutOnWorkType IsAnyOf [IL, OE]`
- OOW: StartDate = today, Type = IL

**Expected Result:** Both TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-AND-002 | StartDate + OutOnWorkType | First TRUE, Second FALSE

**Pre-conditions:**
- Same AND conditions as TC-AND-001
- OOW: StartDate = today ✅, Type = AD ❌

**Expected Result:** One condition fails → Overall FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-AND-003 | StartDate + OutOnWorkType | First FALSE, Second TRUE

**Pre-conditions:**
- Same AND conditions as TC-AND-001
- OOW: StartDate = yesterday ❌, Type = IL ✅

**Expected Result:** One condition fails → Overall FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-AND-004 | StartDate + OutOnWorkType | Both FALSE

**Pre-conditions:**
- Same AND conditions
- OOW: StartDate = yesterday ❌, Type = AD ❌

**Expected Result:** Both FALSE → Overall FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-AND-005 | 3 Conditions — StartDate + DayType + OutOnWorkType | All TRUE

**Pre-conditions:**
- AND Condition 1: `StartDate GreaterThanEqualTo <today>`
- AND Condition 2: `DayType IsAnyOf [W]`
- AND Condition 3: `OutOnWorkType IsExact IL`
- OOW: StartDate = today ✅, DayType = W ✅, Type = IL ✅

**Expected Result:** All 3 TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-AND-006 | 3 Conditions — One Fails in Middle

**Pre-conditions:**
- Same 3 conditions as TC-AND-005
- OOW: StartDate = today ✅, DayType = H ❌, Type = IL ✅

**Expected Result:** Middle condition fails → Overall FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-AND-007 | StartDate IsBetween + Reason Include | Both TRUE

**Pre-conditions:**
- AND 1: `StartDate IsBetween <month start> AND <month end>`
- AND 2: `Reason Include Visit`
- OOW: StartDate = mid-month ✅, Reason = "Client Visit" ✅

**Expected Result:** Both TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-AND-008 | EndDate + StartDate Range Validation

**Pre-conditions:**
- AND 1: `StartDate GreaterThanEqualTo <week start>`
- AND 2: `EndDate LessThanEqualTo <week end>`
- OOW: StartDate = Monday ✅, EndDate = Friday ✅

**Expected Result:** Both within week → Withdraw visible

**Test Type:** Positive

---

### TC-AND-009 | EndDate + StartDate | EndDate Exceeds Week

**Pre-conditions:**
- Same conditions as TC-AND-008
- OOW: StartDate = Monday ✅, EndDate = next Monday ❌

**Expected Result:** EndDate outside range → FALSE → Withdraw blocked

**Test Type:** Negative

---

## SECTION 9 — OR Conditions (At Least One Must Be True)

---

### TC-OR-001 | OutOnWorkType OR DayType | First TRUE

**Pre-conditions:**
- OR Condition 1: `OutOnWorkType IsAnyOf [IL]`
- OR Condition 2: `DayType IsExact H`
- OOW: Type = IL ✅, DayType = W ❌

**Expected Result:** OR — at least one TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OR-002 | OutOnWorkType OR DayType | Second TRUE

**Pre-conditions:**
- Same OR conditions
- OOW: Type = AD ❌, DayType = H ✅

**Expected Result:** Second TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OR-003 | OutOnWorkType OR DayType | Both TRUE

**Pre-conditions:**
- Same OR conditions
- OOW: Type = IL ✅, DayType = H ✅

**Expected Result:** Both TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OR-004 | OutOnWorkType OR DayType | Both FALSE

**Pre-conditions:**
- Same OR conditions
- OOW: Type = AD ❌, DayType = W ❌

**Expected Result:** Both FALSE → Overall FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OR-005 | StartDate OR Reason | First TRUE

**Pre-conditions:**
- OR 1: `StartDate GreaterThan <yesterday>`
- OR 2: `Reason Include Client`
- OOW: StartDate = today ✅, Reason = "Field Work" ❌

**Expected Result:** First TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OR-006 | StartDate OR Reason | Both FALSE

**Pre-conditions:**
- Same OR conditions
- OOW: StartDate = last week ❌, Reason = "Field Work" ❌

**Expected Result:** Both FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-OR-007 | 3 OR Conditions | Only Last TRUE

**Pre-conditions:**
- OR 1: `OutOnWorkType IsExact IL` ❌ (OOW type = AD)
- OR 2: `DayType IsExact H` ❌ (DayType = W)
- OR 3: `Reason Include Visit` ✅ (Reason = "Client Visit")

**Expected Result:** Last OR condition TRUE → Overall TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-OR-008 | 3 OR Conditions | All FALSE

**Pre-conditions:**
- OR 1: `OutOnWorkType IsExact IL` ❌
- OR 2: `DayType IsExact H` ❌
- OR 3: `Reason Include Visit` ❌

**Expected Result:** All FALSE → Withdraw blocked

**Test Type:** Negative

---

## SECTION 10 — AND + OR Combined (Mixed Conditions)

> Note: Per Scenario Manager spec — there is an assumed AND relationship between the AndConditions array and OrConditions array.  
> Logic: `(All AND conditions TRUE) AND (At least one OR condition TRUE)`

---

### TC-ANDOR-001 | AND passes + OR passes → Withdraw allowed

**Pre-conditions:**
- AND: `StartDate GreaterThanEqualTo <today>`
- OR: `OutOnWorkType IsAnyOf [IL, OE]` OR `DayType IsExact H`
- OOW: StartDate = today ✅ AND (Type = IL ✅)

**Expected Result:** AND passes, OR passes → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-ANDOR-002 | AND passes + OR fails → Withdraw blocked

**Pre-conditions:**
- Same conditions as TC-ANDOR-001
- OOW: StartDate = today ✅ AND (Type = AD ❌, DayType = W ❌)

**Expected Result:** AND passes but OR fails → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-ANDOR-003 | AND fails + OR passes → Withdraw blocked

**Pre-conditions:**
- Same conditions
- OOW: StartDate = yesterday ❌ AND (Type = IL ✅)

**Expected Result:** AND fails regardless of OR → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-ANDOR-004 | AND fails + OR fails → Withdraw blocked

**Pre-conditions:**
- Same conditions
- OOW: StartDate = yesterday ❌ AND (Type = AD ❌, DayType = W ❌)

**Expected Result:** Both fail → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-ANDOR-005 | Complex: 2 AND + 2 OR | All Pass

**Pre-conditions:**
- AND 1: `StartDate IsBetween <month start> AND <month end>`
- AND 2: `DayType IsAnyOf [W]`
- OR 1: `OutOnWorkType IsAnyOf [IL, OE]`
- OR 2: `Reason Include Visit`
- OOW: StartDate = mid-month ✅, DayType = W ✅, Type = IL ✅, Reason = "Meeting"

**Expected Result:** Both AND true + first OR true → Withdraw visible

**Test Type:** Positive

---

### TC-ANDOR-006 | Complex: 2 AND + 2 OR | AND passes, both OR fail

**Pre-conditions:**
- Same conditions as TC-ANDOR-005
- OOW: StartDate = mid-month ✅, DayType = W ✅, Type = AD ❌, Reason = "Field Work" ❌

**Expected Result:** AND passes but both OR conditions fail → FALSE → Withdraw blocked

**Test Type:** Negative

---

### TC-ANDOR-007 | Complex: 2 AND + 2 OR | One AND fails, one OR passes

**Pre-conditions:**
- Same conditions
- OOW: StartDate = mid-month ✅, DayType = H ❌, Type = IL ✅

**Expected Result:** Second AND fails → Overall FALSE regardless of OR → Withdraw blocked

**Test Type:** Negative

---

### TC-ANDOR-008 | StartDate + Workshift AND | Reason OR DayType OR

**Pre-conditions:**
- AND 1: `StartDate LessThanEqualTo <today>`
- AND 2: `Workshift IsAnyOf [Morning Shift, General Shift]`
- OR 1: `Reason Include Client`
- OR 2: `DayType IsExact W`
- OOW: StartDate = today ✅, Workshift = Morning Shift ✅, Reason = "Office Visit" ❌, DayType = W ✅

**Expected Result:** AND passes + second OR passes → Withdraw visible

**Test Type:** Positive

---

## SECTION 11 — Edge Cases

---

### TC-EDGE-001 | No Condition Set (Always mode comparison)

**Pre-conditions:**
- Withdrawal toggle ON
- Mode = Always (no conditions)

**Expected Result:** Withdraw always available regardless of any field values

**Test Type:** Positive (Baseline)

---

### TC-EDGE-002 | Conditional mode — Zero conditions added

**Pre-conditions:**
- Withdrawal toggle ON
- Mode = Conditional
- No conditions added in AND or OR sections

**Expected Result:** System should handle gracefully — either block withdraw or show error/validation

**Test Type:** Edge / Negative

---

### TC-EDGE-003 | ExpectedInTime blank for OutOnWorkType = AD

**Pre-conditions:**
- Condition: `ExpectedInTime NotInThisTransaction`
- OOW Type = AD (ExpectedInTime is blank per spec)

**Expected Result:** Field is blank/absent → NotInThisTransaction = TRUE → Withdraw visible

**Test Type:** Positive (Edge — field dependency)

---

### TC-EDGE-004 | ExpectedOutTime blank for OutOnWorkType = IL

**Pre-conditions:**
- Condition: `ExpectedOutTime NotInThisTransaction`
- OOW Type = IL (ExpectedOutTime is blank per spec)

**Expected Result:** Field blank → NotInThisTransaction = TRUE → Withdraw visible

**Test Type:** Positive (Edge)

---

### TC-EDGE-005 | MarkInTime present vs absent

**Pre-conditions:**
- Condition: `MarkInTime InThisTransaction`
- OOW where employee has marked attendance (MarkInTime populated)

**Expected Result:** MarkInTime populated → TRUE → Withdraw visible

**Test Type:** Positive

---

### TC-EDGE-006 | MarkInTime absent (not yet marked)

**Pre-conditions:**
- Same condition: `MarkInTime InThisTransaction`
- OOW where employee has NOT yet marked attendance (MarkInTime blank)

**Expected Result:** MarkInTime blank → FALSE → Withdraw blocked

**Test Type:** Negative (Edge)

---

### TC-EDGE-007 | IsNotBetween Boundary (exactly at Min)

**Pre-conditions:**
- Condition: `StartDate IsNotBetween <month start> AND <month end>`
- OOW StartDate = exactly month start (boundary)

**Expected Result:** Boundary is inclusive in IsBetween, so IsNotBetween at boundary = FALSE → Withdraw blocked

**Test Type:** Negative (Boundary)

---

### TC-EDGE-008 | Large IsAnyOf List — OutOnWorkType all 4 values

**Pre-conditions:**
- Condition: `OutOnWorkType IsAnyOf [IL, OE, IO, AD]`
- OOW Type = any value

**Expected Result:** Any type matches → always TRUE → Withdraw visible for all types

**Test Type:** Positive (All values)

---

### TC-EDGE-009 | IsNotAnyOf with all values — OutOnWorkType

**Pre-conditions:**
- Condition: `OutOnWorkType IsNotAnyOf [IL, OE, IO, AD]`
- OOW Type = IL

**Expected Result:** IL IS in the excluded list → FALSE → Withdraw always blocked (impossible to withdraw)

**Test Type:** Negative (Impossible condition scenario)

---

### TC-EDGE-010 | DayType = O (Weekly Off) — No withdrawal allowed condition

**Pre-conditions:**
- Condition: `DayType IsAnyOf [W]` (only working days can withdraw)
- OOW submitted on Weekly Off (DayType = O)

**Expected Result:** O not in [W] → FALSE → Withdraw blocked on holidays/off days

**Test Type:** Negative (Real-world business scenario)

---

## SECTION 12 — Operator Validation (System-level)

---

### TC-SYS-001 | Wrong operator for TS field — System prevents

**Pre-conditions:**
- Attempt to configure condition: Field = `StartDate` (TS type), Operator = `Include`

**Expected Result:** System should not allow text operators for timestamp fields — either hide them or show validation error

**Test Type:** Negative (System validation)

---

### TC-SYS-002 | IsBetween without MaxValue

**Pre-conditions:**
- Configure: `StartDate IsBetween <min value>` — leave MaxValue blank

**Expected Result:** System should show validation error — MaxValue required for IsBetween

**Test Type:** Negative (System validation)

---

### TC-SYS-003 | IsAnyOf with empty list

**Pre-conditions:**
- Configure: `OutOnWorkType IsAnyOf []` — empty list

**Expected Result:** System should show validation error — at least one value required

**Test Type:** Negative (System validation)

---

### TC-SYS-004 | Condition saved and persists after refresh

**Pre-conditions:**
- Configure any condition (e.g., `OutOnWorkType IsExact IL`)
- Save the configuration
- Refresh the page / re-open Configure Withdrawal

**Expected Result:** Condition persists correctly — field, operator, and value retained

**Test Type:** Positive (Persistence)

---

### TC-SYS-005 | Switch from Conditional to Always mode

**Pre-conditions:**
- Conditions configured in Conditional mode
- Switch mode to Always

**Expected Result:** Conditions are ignored/cleared — withdrawal available without condition evaluation

**Test Type:** Positive (Mode switch)

---

### TC-SYS-006 | Switch from Always to Conditional mode

**Pre-conditions:**
- Mode was Always
- Switch to Conditional
- No conditions added yet

**Expected Result:** System requires at least one condition OR defaults to block — verify behavior

**Test Type:** Edge

---

## Summary Table

| Section | Field | Operators Covered | Test Count |
|---------|-------|-------------------|------------|
| 1 | StartDate (TS) | Equal, GT, GTE, LT, LTE, IsBetween, IsNotBetween | 13 |
| 2 | EndDate (TS) | Equal, GT, LT, IsBetween, IsNotBetween | 5 |
| 3 | OutOnWorkType (TX) | IsAnyOf, IsNotAnyOf, IsExact, Include, NotInclude, StartWith, EndWith, InThisTransaction, NotInThisTransaction | 15 |
| 4 | DayType (TX) | IsAnyOf, IsNotAnyOf, IsExact | 6 |
| 5 | Reason (TX) | IsExact, Include, NotInclude, StartWith, EndWith, IsAnyOf, IsNotAnyOf | 8 |
| 6 | Workshift (TX) | IsAnyOf, IsExact, IsNotAnyOf, Include | 4 |
| 7 | Header Fields | InThisTransaction, NotInThisTransaction, Equal | 6 |
| 8 | AND Combinations | Multiple fields + AND logic | 9 |
| 9 | OR Combinations | Multiple fields + OR logic | 8 |
| 10 | AND + OR Mixed | Complex combined conditions | 8 |
| 11 | Edge Cases | Blank fields, boundary values, impossible conditions | 10 |
| 12 | System Validation | Operator restrictions, persistence, mode switch | 6 |
| **Total** | | | **98** |

