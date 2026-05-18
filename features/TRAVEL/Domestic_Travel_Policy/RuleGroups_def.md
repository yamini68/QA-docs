I’ll make this as practical notes you can later use during testing, discussions, and debugging.

# DOMESTIC / INTERNATIONAL TRAVEL POLICY — RULEGROUP NOTES

---

# 1. Minimum Lead Time

## What it means

Minimum number of days before trip start that employee must apply.

## Why company uses it

To avoid expensive last-minute bookings.

## Example

Trip Date:
20 May

Policy:
Minimum Lead Time = 5 days

Employee applies:
18 May

Lead Time = 2 days → violation.

---

# 2. Maximum Trip Duration

## What it means

Maximum number of days employee can stay on official travel.

## Why company uses it

To prevent misuse or very long unofficial stays.

## Example

Policy:
Max Trip Duration = 10 days

Employee requests:
15-day trip

→ violation.

---

# 3. Open Return Date

## What it means

Whether employee can submit travel without fixed return date.

## Why used

Some field/site work has uncertain return.

## Example

Service engineer goes onsite but return date unknown.

If enabled:
Return date optional.

---

# 4. Self Booking

## What it means

Whether employee can book tickets/hotels themselves.

## Example

### Enabled

Employee books directly.

### Disabled

Travel desk/team books on behalf of employee.

---

# 5. Permitted Travel Mode

## What it means

Allowed transport types.

## Example

Allowed:

* Air
* Rail
* Road

Not allowed:

* Sea

---

# 6. Permitted Travel Class

## What it means

Allowed class inside travel mode.

## Example

### Flight

* Economy
* Business

### Train

* Sleeper
* AC First Class

Example:
Junior employee → Economy only.

---

# 7. Permitted Property Type

## What it means

Allowed accommodation type.

## Example

Allowed:

* Hotel
* Guest House
* Service Apartment

---

# 8. Permitted Star Rating

## What it means

Allowed hotel quality/star level.

## Example

G3 Employee:
3-star only.

Director:
5-star allowed.

---

# 9. Maximum Stay Rate

## What it means

Maximum hotel amount allowed per night.

## Example

Bangalore:
₹5000/night cap.

Employee books:
₹8000 hotel.

→ violation.

---

# 10. Daily Food Entitlement

## What it means

Total food amount allowed per day.

## Example

₹1500/day total.

Employee spends:

* Breakfast ₹200
* Lunch ₹400
* Dinner ₹700

Total = ₹1300 → allowed.

---

# 11. Meal Caps

## What it means

Separate limit for each meal.

## Example

* Breakfast = ₹200
* Lunch = ₹500
* Dinner = ₹700

Dinner ₹1200
→ violation.

---

# 12. Hosted Meal Deduction Rate

## What it means

Deduct allowance if client/company already provided meal.

## Example

Daily Food Allowance = ₹1500

Lunch provided by client.

Deduction Rate = 0.33

System deducts:
33% from allowance.

---

# 13. Permitted Commute Mode

## What it means

Allowed local travel methods.

## Example

Allowed:

* Taxi
* Metro
* Own vehicle

Not allowed:

* Luxury rental car

---

# 14. Commute Ride Cap

## What it means

Maximum amount allowed per local ride.

## Example

Cab ride cap:
₹500

Employee claims:
₹900 ride.

→ violation.

---

# 15. Mileage Rate

## What it means

Reimbursement amount per kilometer for own vehicle usage.

## Example

Rate:
₹10/km

Employee traveled:
20 km

Reimbursement:
₹200

---

# 16. Incidental Expenses

## What it means

Miscellaneous expenses allowed during trip.

## Example

Allowed:

* Laundry
* Tips
* Internet
* Porter charges

---

# 17. Daily Incidentals Cap

## What it means

Maximum miscellaneous expense allowed per day.

## Example

Daily cap:
₹1000

Employee claims:
₹1400

→ violation.

---

# 18. Day Trip Allowance

## What it means

Allowance for same-day travel without stay.

## Example

Hyderabad → Bangalore → return same day.

Allowance:
₹1000

---

# 19. Stay Day Allowance

## What it means

Allowance for days employee stays overnight.

## Example

Employee stays 3 days onsite.

Allowance:
₹2000/day

---

# 20. Transit Day Allowance

## What it means

Allowance for travel/transit days.

Usually lower than stay allowance.

## Example

Travel day:
₹800

Full stay day:
₹2000

---

# 21. Travel Advances

## What it means

Whether advance money can be requested before trip.

## Example

Employee requests:
₹10,000 before travel.

---

# 22. Maximum Advance Amount

## What it means

Maximum total advance employee can request.

## Example

Max advance:
₹5000

Employee requests:
₹9000

→ violation.

---

# 23. Daily Advance Amount

## What it means

Advance calculated per trip day.

## Example

₹2000/day

5-day trip:
₹10,000 advance allowed.

---

# 24. Allocation Level

## What it means

Defines at which business level expense allocation happens.

## Example

Allocation based on:

* Cost Center
* Account
* BU
* Function

---

# 25. Allocation by Account

## What it means

Expense charged to financial account.

## Example

Travel Expense Account:
“Client Travel Expenses”

---

# 26. Account Allocation Fractional

## What it means

Split expense across multiple accounts.

## Example

70% → Client Account
30% → Internal Account

---

# 27. Allocation by Cost Centre

## What it means

Expense assigned to department/team budget.

## Example

Sales Team Cost Center.

---

# 28. Cost Centre Allocation Fractional

## What it means

Split expense across departments.

## Example

60% Sales
40% Marketing

---

# 29. Allocation by Business Unit

## What it means

Expense assigned to business unit.

## Example

Assigned to:

* India BU
* APAC BU

---

# 30. Business Unit Allocation Fractional

## What it means

Split across multiple business units.

---

# 31. Allocation by Location

## What it means

Expense charged to specific office/location.

## Example

Hyderabad Office Budget.

---

# 32. Location Allocation Fractional

## What it means

Split expense across multiple locations.

---

# 33. Allocation by Function

## What it means

Expense assigned to business function.

## Example

* HR
* Engineering
* Finance

---

# 34. Function Allocation Fractional

## What it means

Split expense across multiple functions.

## Example

50% HR
50% Operations

---

# MOST IMPORTANT BIG PICTURE

These RuleGroups together control:

| Area                      | Controlled By            |
| ------------------------- | ------------------------ |
| Who can travel            | Travel entitlement rules |
| How they travel           | Mode/class/property      |
| How much they spend       | Caps/allowances          |
| What gets reimbursed      | Expense rules            |
| What gets blocked         | Deviation logic          |
| Which finance bucket pays | Allocation rules         |

---

# Simplest Overall Understanding

The policy engine answers:

```text id="udkphs"
Can employee travel?
How?
For how long?
What can they spend?
What will company reimburse?
Which budget/account pays for it?
```
