# Domestic Travel Policy — RuleGroups, RuleType & ValueDataType

## Trip Envelope

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 1 | Minimum Lead Time | MinLimit | IN (integer, days) |
| 2 | Maximum Trip Duration | MaxLimit | Duration (unit: Days) |
| 3 | Open Return Date | Presence | — |

---

## Booking Arrangement

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 4 | Self Booking | Presence | — |

---

## Travel Mode & Class

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 5 | Permitted Travel Mode | Options | — (permission level per option) |
| 6 | Permitted Travel Class | Options | — (permission level per option) |

---

## Accommodation

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 7 | Preferred Properties | Options | — (permission level per option) |
| 8 | Permitted Star Rating | Options | — (permission level per option) |
| 9 | Maximum Stay Rate | MaxLimit | Amount (DE) |

---

## Food

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 10 | Daily Food Entitlement | MaxLimit | Amount (DE) |
| 11 | Meal Caps | MaxLimit | Amounts — Breakfast (DE), Lunch (DE), Dinner (DE) |
| 12 | Hosted Meal Deduction Rate | Multiplicity | DE (decimal; default 0.33) |

---

## Local Commute

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 13 | Permitted Commute Mode | Options | — (permission level per option) |
| 14 | Commute Ride Cap | MaxLimit | Amount (DE) |
| 15 | Mileage Rate | MaxLimit | Amount (DE) |

---

## Incidentals

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 16 | Incidental Expenses | Options | — (permission level per option) |
| 17 | Daily Incidentals Cap | MaxLimit | Amount (DE) |

---

## Allowances

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 18 | Day Trip Allowance | MaxLimit | Amount (DE) |
| 19 | Stay Day Allowance | MaxLimit | Amount (DE) |
| 20 | Transit Day Allowance | MaxLimit | Amount (DE) |

---

## Travel Advance

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 21 | Travel Advances | Presence | — |
| 22 | Maximum Advance Amount | MaxLimit | Amount (DE) |
| 23 | Daily Advance Amount | MaxLimit | Amount (DE) |

---

## Ground Transport

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 24 | Permitted Car Type | Options | — (permission level per option) |

---

## Cost Allocation — Travel Request

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 25 | Cost Allocation on Travel Request | Presence | — |
| 26 | Cost Centre Allocation (on Request) | Presence | — |
| 27 | Project Allocation (on Request) | Presence | — |

---

## Cost Allocation — Expense Claim

| # | RuleGroup | RuleType | ValueDataType |
|---|-----------|----------|---------------|
| 28 | Allocation Level | Options | TX (Claim / LineItem / Disabled) |
| 29 | Allocation by Account | Presence | — |
| 30 | Account Allocation Fractional | Presence | — |
| 31 | Allocation by Cost Centre | Presence | — |
| 32 | Cost Centre Allocation Fractional | Presence | — |
| 33 | Allocation by Business Unit | Presence | — |
| 34 | Business Unit Allocation Fractional | Presence | — |
| 35 | Allocation by Location | Presence | — |
| 36 | Location Allocation Fractional | Presence | — |
| 37 | Allocation by Function | Presence | — |
| 38 | Function Allocation Fractional | Presence | — |

---

# Summary by RuleType

| RuleType | Count | Description |
|-----------|-------|-------------|
| Presence | 17 | Enables or disables a field, feature, or section (Enabled / Disabled) |
| MaxLimit | 12 | Defines a maximum value that must not be exceeded |
| Options | 8 | Assigns a permission level (Permitted / Exception / Disallowed) to each option |
| Multiplicity | 1 | Supplies a decimal multiplier (Hosted Meal Deduction Rate only) |

---

# Summary by ValueDataType

| ValueDataType | RuleGroups |
|---------------|------------|
| Amount (DE) | Maximum Stay Rate, Daily Food Entitlement, Meal Caps (Breakfast/Lunch/Dinner), Commute Ride Cap, Mileage Rate, Daily Incidentals Cap, Day Trip Allowance, Stay Day Allowance, Transit Day Allowance, Maximum Advance Amount, Daily Advance Amount |
| IN (Integer) | Minimum Lead Time |
| Duration | Maximum Trip Duration |
| DE (Decimal) | Hosted Meal Deduction Rate |
| TX (Text/Select) | Allocation Level |
| — (No Scalar Value) | All Presence and Options RuleGroups |

---

# Totals

| Category | Count |
|-----------|-------|
| Total RuleGroups | 38 |
| Presence | 17 |
| MaxLimit | 12 |
| Options | 8 |
| Multiplicity | 1 |
