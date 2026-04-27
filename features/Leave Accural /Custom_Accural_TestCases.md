## What is Custom Accrual?

In leave accrual systems, when an employee joins or leaves **mid-period** (not on day 1), the system needs to decide how many leaves to credit for that partial period. Typically you get:

- **Full Accrual** – credit the full period's leaves regardless of when they joined/left
- **Partial/Pro-rata Accrual** – credit proportionally based on days present
- **No Accrual** – credit nothing for the partial period
- **Custom Accrual** – YOU define the exact rules: *"if someone joins between day X and day Y of the month, give them Z days"* — it's a manual rule-based slab system

So Custom Accrual gives admins granular control via day-range slabs, independently for **joining** and **separation** months.

---

## Section-wise Test Cases for Custom Accrual

---

### Section 1 — Enabling Custom Accrual

| # | Test | Steps | Expected Result |
|---|------|--------|-----------------|
| 1.1 | Dropdown selection | In "If the user is present only partly for the period" dropdown, select **Custom Accrual** | Custom accrual on joining and separation sections appear below |
| 1.2 | Sections hidden for other options | Switch dropdown to Full/Partial/No Accrual | Custom joining & separation rule sections should disappear |
| 1.3 | Re-selecting Custom Accrual | Switch away and come back to Custom Accrual | Sections reappear; previously entered rules should either persist or reset (verify expected behavior) |

---

### Section 2 — Custom Accrual on Joining (Rule Configuration)

#### 2A — Field Validations

| # | Test | Input | Expected Result |
|---|------|-------|-----------------|
| 2.1 | Empty fields on save | Leave all three fields (from day, to day, accrue days) blank and save | Validation error on all required fields |
| 2.2 | From day = 0 | Enter `0` in "joins between day" | Should reject — valid days are 1–31 |
| 2.3 | From day > 31 | Enter `32` | Should reject |
| 2.4 | To day < From day | From day = `20`, To day = `5` | Should reject — to day must be ≥ from day |
| 2.5 | From day = To day | From = `15`, To = `15` | Should accept (single day slab) |
| 2.6 | Accrue = 0 | Enter `0` in accrue field | Verify if 0 is a valid value (some systems allow it to mean "no accrual for this range") |
| 2.7 | Accrue > monthly entitlement | Enter value higher than the normal monthly accrual | Verify if there's a cap or it's allowed |
| 2.8 | Decimal in accrue days | Enter `1.5` | Verify if decimals are allowed based on rounding settings |

#### 2B — Slab Logic

| # | Test | Input | Expected Result |
|---|------|-------|-----------------|
| 2.9 | Single slab covers full month | Day 1 to Day 31, accrue 2 days | Employee joining any day gets 2 days |
| 2.10 | Two non-overlapping slabs | Slab 1: Day 1–15 = 2 days; Slab 2: Day 16–31 = 1 day | Employee joining day 10 gets 2 days; day 20 gets 1 day |
| 2.11 | Overlapping slabs | Slab 1: Day 1–15; Slab 2: Day 10–20 | Should either block overlap or define which rule takes priority |
| 2.12 | Gap in slabs | Slab 1: Day 1–10; Slab 2: Day 20–31 (gap on 11–19) | Employee joining on day 15 (in gap) — what happens? No accrual? Error? |
| 2.13 | Add multiple rules | Click "+ Add Rule" multiple times | Each click adds a new row; all rows are independently configurable |
| 2.14 | Delete a rule | Click delete (🗑) on a slab | That slab row is removed; remaining slabs unaffected |

#### 2C — Actual Accrual Verification

| # | Test | Setup | Expected Result |
|---|------|-------|-----------------|
| 2.15 | Employee joins within slab range | Rule: Day 1–10 = 2 days. Employee joins on Day 5 | Employee gets exactly 2 days for joining month |
| 2.16 | Employee joins on slab boundary | Rule: Day 1–15 = 2 days. Employee joins on Day 15 | Employee gets 2 days (boundary is inclusive) |
| 2.17 | Employee joins outside all slabs | No slab covers Day 25. Employee joins Day 25 | Verify behavior — 0 days or error in accrual run |
| 2.18 | Joining on Day 1 (full month) | Employee joins Day 1 | Should match whatever the Day 1 slab says |

---

### Section 3 — Custom Accrual on Separation (Rule Configuration)

Same validations as Section 2, but applied to employees **leaving** mid-period.

| # | Test | Input | Expected Result |
|---|------|-------|-----------------|
| 3.1 | Single separation slab | Day 1–15 = 1 day, Day 16–31 = 2 days. Employee leaves Day 20 | Gets 2 days for exit month |
| 3.2 | Employee leaves on last day of month | Rule: Day 16–31 = 2 days. Employee leaves Day 31 | Gets 2 days |
| 3.3 | Employee leaves Day 1 | Rule: Day 1–5 = 0 days | Gets 0 days for that month |
| 3.4 | Gap in separation slabs | Employee leaves on day not covered by any slab | Verify — 0 accrual or fallback behavior |
| 3.5 | Joining + Separation same month | Employee joins Day 5 and leaves Day 20 in the same month | Which rule fires — joining rule, separation rule, or both? Only one should apply |

---

### Section 4 — First Accrual for Newly Eligible Employee

| # | Test | Option | Expected Result |
|---|------|--------|-----------------|
| 4.1 | "Calculated using same rule as date of joining" | Select this option | System uses the joining custom accrual slab for the first eligible period |
| 4.2 | "Credited only for leave earned in partial cycle" | Select this option | System pro-rates based on partial period, ignoring custom slabs |
| 4.3 | Newly eligible employee with custom joining rule | Employee becomes eligible mid-month under option 4.1 | Accrual matches the slab their eligibility date falls into |

---

### Section 5 — Accrual Method (Workdays vs Calendar Period)

| # | Test | Expected Result |
|---|------|-----------------|
| 5.1 | Workdays selected | Custom slab accrual counts only working days in the slab range when calculating eligibility |
| 5.2 | Calendar Period selected | Custom slab uses calendar days (including weekends/holidays) |
| 5.3 | Month with public holidays | Compare accrual under both modes for a month with holidays in the joining/separation range — values should differ |

---

### Section 6 — Rounding & Decimal Places

| # | Test | Expected Result |
|---|------|-----------------|
| 6.1 | Rounding = Round Up, 0 decimal places | Accrued value 1.2 → rounds to 2 |
| 6.2 | Rounding = Round Down, 0 decimal places | 1.8 → rounds to 1 |
| 6.3 | Rounding = Round Off (nearest), 1 decimal place | 1.25 → 1.3 |
| 6.4 | Custom slab gives whole number | Enter accrue = 2 days — no rounding should be needed, verify clean value in balance |

---

### Section 7 — Save & Edge Cases

| # | Test | Expected Result |
|---|------|-----------------|
| 7.1 | Save with no rules added | Clicking save with Custom Accrual selected but no slabs defined | Should warn/block — at least one rule is expected |
| 7.2 | Save valid config and reload | All slabs should persist correctly after page reload |
| 7.3 | Edit existing rule | Modify a saved slab's day range or accrue value | Change reflects in accrual run going forward |
| 7.4 | Switching away from Custom Accrual post-save | Change dropdown to Full Accrual and save | Custom rules should be ignored; only re-applied if Custom Accrual is re-selected |
| 7.5 | Timezone impact | Change timezone and run accrual near month boundary | Verify joining/separation day is calculated in the selected timezone, not server time |

---

**Priority order for testing:** Section 2C (actual accrual correctness) → Section 3 (separation) → Section 1 (toggle behavior) → Section 7 (save/edge cases) → Sections 4–6 (supporting settings).




## Test Combinations with DOJ, Confirmation Date and Probation Period

Slab 1 = Day 1–15 → 1.75 days (rounds to 1.8)
Slab 2 = Day 16–31 → 1 day

---

| # | Start Accruing After | Accrued Leaves Start From | DOJ | Probation Period | Confirmation Date | Slab That Should Fire | Expected Accrual |
|---|---------------------|--------------------------|-----|-----------------|-------------------|----------------------|-----------------|
| 1 | 0 Days after Joining Date | Joining Date | 2nd April | — | — | Slab 1 (Day 2) | **1.8 days** ✅ Already tested |
| 2 | 0 Days after Joining Date | Joining Date | 20th April | — | — | Slab 2 (Day 20) | **1.0 day** ✅ Already tested |
| 3 | 0 Days after Joining Date | Date of Confirmation | 2nd April | 15 days | 17th April | Slab 2 (Day 17) | **1.0 day** |
| 4 | 0 Days after Joining Date | Date of Confirmation | 2nd April | 10 days | 12th April | Slab 1 (Day 12) | **1.8 days** |
| 5 | 0 Days after Date of Confirmation | Date of Confirmation | 2nd April | 15 days | 17th April | Slab 2 (Day 17) | **1.0 day** |
| 6 | 0 Days after Date of Confirmation | Date of Confirmation | 5th April | 8 days | 13th April | Slab 1 (Day 13) | **1.8 days** |
| 7 | 30 Days after Joining Date | Joining Date | 2nd April | — | — | Slab 1 (Day 2 of May) | **1.8 days** (fires in May not April) |
| 8 | 30 Days after Date of Confirmation | Date of Confirmation | 2nd April | 15 days | 17th April | Slab 2 (Day 17 of May) | **1.0 day** (fires in May) |

---

## Key Combination to Focus On (Most Critical)

**Test 3 and Test 4** — DOJ and Confirmation fall in **different slabs:**

> Test 3: DOJ Day 2 (Slab 1) but Confirmation Day 17 (Slab 2)
> Since "Accrued Leaves start from = Confirmation Date"
> System should fire Slab 2 → **1.0 day**
> If it gives 1.8 days → system is wrongly using DOJ instead of Confirmation Date 🐛

---

## Probation Period to Set

| Test # | Probation Days to Set in Policy |
|--------|--------------------------------|
| 3 | **15 days** |
| 4 | **10 days** |
| 5 | **15 days** |
| 6 | **8 days** |
| 7 | No probation needed |
| 8 | **15 days** |

---

Want me to also add the UALB expected values and JSON verification points for each of these?
