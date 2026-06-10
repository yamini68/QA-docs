# International Travel Policy — RuleGroups

## Trip Envelope

| # | RuleGroup Name        | RuleType Label | ValueDataType      | RuleGroupID   |
| - | --------------------- | -------------- | ------------------ | ------------- |
| 1 | Minimum Lead Time     | MinLimit       | IN (integer, days) | 1746085200001 |
| 2 | Maximum Trip Duration | MaxLimit       | Duration (Days)    | 1746085200002 |
| 3 | Open Return Date      | Presence       | —                  | 1746085200003 |

---

## Booking Arrangement

| # | RuleGroup Name | RuleType Label | ValueDataType | RuleGroupID   |
| - | -------------- | -------------- | ------------- | ------------- |
| 4 | Self Booking   | Presence       | —             | 1746085200004 |

---

## Travel Mode & Class

| # | RuleGroup Name         | RuleType Label | ValueDataType | RuleGroupID   |
| - | ---------------------- | -------------- | ------------- | ------------- |
| 5 | Permitted Travel Mode  | Options        | —             | 1746085200005 |
| 6 | Permitted Travel Class | Options        | —             | 1746085200006 |

---

## Accommodation

| # | RuleGroup Name             | RuleType Label | ValueDataType | RuleGroupID   |
| - | -------------------------- | -------------- | ------------- | ------------- |
| 7 | Preferred Properties       | Options        | —             | 1746085200007 |
| 8 | Permitted Star Rating      | Options        | —             | 1746085200008 |
| 9 | Maximum Nightly Hotel Rate | MaxLimit       | Amount        | 1746085200009 |

---

## Food

| #  | RuleGroup Name             | RuleType Label | ValueDataType                                   | RuleGroupID   |
| -- | -------------------------- | -------------- | ----------------------------------------------- | ------------- |
| 10 | Daily Food Entitlement     | MaxLimit       | Amount                                          | 1746085200010 |
| 11 | Meal Caps                  | MaxLimit       | Amounts (Breakfast / Lunch / Dinner sub-fields) | 1746085200011 |
| 12 | Hosted Meal Deduction Rate | Multiplicity   | DE (decimal)                                    | 1746085200015 |

> **Note:** Daily Food Entitlement and Meal Caps are alternative models. Configure one or the other per employee group, not both.

---

## Local Commute

| #  | RuleGroup Name         | RuleType Label | ValueDataType | RuleGroupID   |
| -- | ---------------------- | -------------- | ------------- | ------------- |
| 13 | Permitted Commute Mode | Options        | —             | 1746085200016 |
| 14 | Commute Ride Cap       | MaxLimit       | Amount        | 1746085200017 |
| 15 | Mileage Rate           | MaxLimit       | Amount        | 1746085200018 |
| 16 | Permitted Car Type     | Options        | —             | 1748476800025 |

> **Note:** Commute Ride Cap (ride-based modes) and Mileage Rate (own vehicle) write to the same transaction field. Scope each using a CheckWhen condition on commute mode.

---

## Incidentals

| #  | RuleGroup Name                     | RuleType Label | ValueDataType | RuleGroupID   |
| -- | ---------------------------------- | -------------- | ------------- | ------------- |
| 17 | Permitted Incidental Expense Types | Options        | —             | 1746085200019 |
| 18 | Daily Incidentals Cap              | MaxLimit       | Amount        | 1746085200020 |

---

## Allowances

| #  | RuleGroup Name        | RuleType Label | ValueDataType | RuleGroupID   |
| -- | --------------------- | -------------- | ------------- | ------------- |
| 19 | Day Trip Allowance    | MaxLimit       | Amount        | 1746085200021 |
| 20 | Stay Day Allowance    | MaxLimit       | Amount        | 1746085200022 |
| 21 | Transit Day Allowance | MaxLimit       | Amount        | 1746085200023 |

> **Note:** All three allowance RuleGroups write to the same `Allowances.Amount` field. Each must be scoped using CheckWhen on `AllowanceType` (DayTrip / StayDay / TransitDay).

---

## Travel Advances

| #  | RuleGroup Name           | RuleType Label | ValueDataType | RuleGroupID   |
| -- | ------------------------ | -------------- | ------------- | ------------- |
| 22 | Travel Advance Permitted | Presence       | —             | 1746085200024 |
| 23 | Maximum Advance Amount   | MaxLimit       | Amount        | 1746085200025 |
| 24 | Daily Advance Amount     | MaxLimit       | Amount        | 1746085200026 |

> **Note:** Maximum Advance Amount and Daily Advance Amount are alternative ceiling models. Configure one or the other, not both.

---

## Expense Claim Cost Allocation

| #  | RuleGroup Name                      | RuleType Label | ValueDataType | RuleGroupID   |
| -- | ----------------------------------- | -------------- | ------------- | ------------- |
| 25 | Allocation Level                    | Options        | TX            | 1746720100001 |
| 26 | Allocation by Account               | Presence       | —             | 1746720100002 |
| 27 | Account Allocation Fractional       | Presence       | —             | 1746720100003 |
| 28 | Allocation by Cost Centre           | Presence       | —             | 1746720100004 |
| 29 | Cost Centre Allocation Fractional   | Presence       | —             | 1746720100005 |
| 30 | Allocation by Business Unit         | Presence       | —             | 1746720100006 |
| 31 | Business Unit Allocation Fractional | Presence       | —             | 1746720100007 |
| 32 | Allocation by Location              | Presence       | —             | 1746720100008 |
| 33 | Location Allocation Fractional      | Presence       | —             | 1746720100009 |
| 34 | Allocation by Function              | Presence       | —             | 1746720100010 |
| 35 | Function Allocation Fractional      | Presence       | —             | 1746720100011 |

---

## Travel Request Cost Allocation (Added in v004.a)

| #  | RuleGroup Name                    | RuleType Label | ValueDataType | RuleGroupID   |
| -- | --------------------------------- | -------------- | ------------- | ------------- |
| 36 | Cost Allocation on Travel Request | Presence       | —             | 1748908800201 |
| 37 | Cost Centre Allocation            | Presence       | —             | 1748908800202 |
| 38 | Project Allocation                | Presence       | —             | 1748908800203 |

---

## Summary

* **Total RuleGroups:** 38
* **Logical Groupings:** 9

### Notes

* RuleGroups **36–38** were introduced in **v004.a (May 2026)**.
* These govern the **CostAllocations[]** array on the **Travel Request** form.
* They are distinct from RuleGroups **25–35**, which govern **Expense Claim** allocation behavior.
