# Custom Accrual - Notes

---

## What is Custom Accrual?

When an employee joins or leaves mid-month, the system needs to decide how many leaves to credit for that partial month. Options are:

- Full Accrual – credit full month's leaves regardless of when they joined/left
- Partial/Pro-rata – credit proportionally based on days present
- No Accrual – credit nothing for the partial period
- Custom Accrual – Admin manually defines slab rules like "if someone joins between Day X and Day Y, give them Z days"

Custom Accrual gives HR granular control via day-range slabs, independently for joining month and separation month.

---

## How 24 Annual Leave Days + Accrual Method Works

24 days/year ÷ 12 months = 2 days per month (for a full month)

Accrual Method controls how this 2 days per full month is calculated:

- Calendar Period – every month = full 2 days, regardless of weekends/holidays
- Workdays – 2 days is proportioned based on actual working days in that month

Custom Accrual and Accrual Method serve different purposes:
- Custom Accrual = what to give for the broken joining/exit month (admin hardcodes it via slabs)
- Accrual Method = how to calculate every normal complete month after that

Example:
Employee joins Day 18 of January. Custom slab rule: Day 16–31 = 1 day.

- January (joined Day 18) → Custom Accrual slab fires → 1 day credited
- February onwards → Regular accrual (Calendar/Workdays) → 2 days/month

---

## Joining vs Eligibility - Difference

Joining = brand new employee entering the company mid-month

Eligibility Change = existing employee who just became eligible for a leave type mid-month

Example for Eligibility:
Ravi joined 6 months ago. Company policy says Sick Leave starts only after completing 6 months probation. Ravi completes probation on 15th April. So from 15th April he becomes newly eligible for Sick Leave. What does he get for April? That is an eligibility change scenario, not a joining scenario.

---

## DOJ Based vs Prorated - What Were They?

These were the two options under "The first accrual for a newly eligible employee will be."

Example: Ravi becomes newly eligible for Sick Leave on Day 5 of April. Policy = 2 days/month. Custom slabs: Day 1–15 = 2 days, Day 16–31 = 1 day.

DOJ Based (Removed):
- Ignore when Ravi became eligible
- Look at his original date of joining the company – say he joined Day 28
- Day 28 falls in slab Day 16–31 = 1 day credited
- Problem: his eligibility date has nothing to do with when he originally joined, so this gave wrong results

Prorated (Kept as default):
- Look at when Ravi actually became eligible – Day 5 of April
- Calculate proportionally based on days he was eligible in that month
- More logical and fair

DOJ Based was removed because it gave incorrect results in many scenarios. Prorated is now the only and default behavior.

---

## Team Update - What Backend Has and Has Not Done

- DOJ Based option removed from UI – done
- Custom Accrual slabs for joining and separation – working, can be tested
- Eligibility change in TRF/Transfer settings – backend not ready yet, defaults to Prorated regardless of selection
- Test custom accrual slab scenarios but skip eligibility change scenarios for now
