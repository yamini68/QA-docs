## Great Question — Let me break this down clearly -i wanna ask a question how many days to accure we are giving int the custom rule then what is the use of the accural method like if i selct calnedar priod and annual leave i have given 24 then like how

These are **two separate things** that work at **different stages** of accrual. They don't conflict, they serve different purposes.

---

## How 24 Annual Leave Days Works in the System

```
24 days/year ÷ 12 months = 2 days accrued per month (full month)
```

**Accrual Method** (Workdays vs Calendar Period) controls how this **2 days per full month** is calculated:

| Method | What it means |
|--------|--------------|
| **Calendar Period** | Every month = full 2 days, regardless of weekends/holidays |
| **Workdays** | 2 days is proportioned based on actual working days in that month |

So for a **complete month** where the employee was present the whole time — both methods give 2 days. The difference shows up when there are **absences or unpaid leaves** within a full month.

---

## Where Custom Accrual Fits In

Custom Accrual **does NOT touch full months at all.**

It **only activates** for the **partial month** when someone **joins or leaves mid-month.**

```
Normal months → Accrual Method handles it (2 days/month)
Joining/Exit month (partial) → Custom Accrual slab takes over
```

### Example with 24 days annual leave:

Employee joins on **Day 18** of January. Rule: Day 16–31 = **1 day**

| Month | Accrual Type | Days Credited |
|-------|-------------|---------------|
| January (joined Day 18) | **Custom Accrual slab** | 1 day (your slab rule) |
| February onwards | **Regular accrual** (Calendar/Workdays) | 2 days/month |

---

## So What Does Accrual Method Actually Control Here?

In the context of Custom Accrual, the Accrual Method mainly matters for:

1. **Identifying which "day" the join/exit falls on** — especially near weekends/holidays (does Day 18 being a Sunday shift to Day 19 in workday mode?)
2. **Full months after joining** — all subsequent months still use the accrual method normally
3. **If an employee has partial presence within a regular month** (mid-leave, unpaid days) — that's still governed by the accrual method

---

## Simply Put

> **Custom Accrual = What to give for the broken joining/exit month (you hardcode it)**
>
> **Accrual Method = How to calculate every normal complete month after that**

They run **independently** and handle **different scenarios.** The 24 days/year setting drives the regular monthly math; custom accrual just overrides that math for the first and last partial month only.
