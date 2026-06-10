# Domestic Travel Policy RuleGroups

## Trip Envelope (3)

1. **Minimum Lead Time**
   - Minimum days advance notice before trip start.

2. **Maximum Trip Duration**
   - Maximum days a trip may run.

3. **Open Return Date**
   - Whether a fixed return date is mandatory.

---

## Booking Arrangement (1)

1. **Self Booking**
   - Whether employee may book independently or must use the travel desk.

---

## Travel Mode & Class (2)

1. **Permitted Travel Mode**
   - Air / Rail / Road / Sea.
   - Each can be configured as:
     - Permitted
     - Exception
     - Disallowed

2. **Permitted Travel Class**
   - Economy, Business, Sleeper, etc., per travel mode.

---

## Accommodation (3)

1. **Preferred Properties**
   - Permitted property types.
   - Sourced from tenant's Preferred Properties ETYs.

2. **Permitted Star Rating**
   - Hotel star rating entitlement.

3. **Maximum Stay Rate**
   - Nightly rate ceiling per stay record.

---

## Food (3)

1. **Daily Food Entitlement**
   - Flat daily food cap.
   - Alternative to Meal Caps.

2. **Meal Caps**
   - Separate caps for:
     - Breakfast
     - Lunch
     - Dinner
   - Alternative to Daily Food Entitlement.

3. **Hosted Meal Deduction Rate**
   - Fraction deducted per provided meal.
   - Default: `0.33`.

---

## Local Commute (3)

1. **Permitted Commute Mode**
   - App Cab, Taxi, Own Car, etc.

2. **Commute Ride Cap**
   - Per-ride ceiling for ride-based modes.

3. **Mileage Rate**
   - Per-km / per-mile reimbursement rate for own-vehicle modes.

---

## Incidentals (2)

1. **Incidental Expenses**
   - Permission level per incidental category:
     - Tips
     - Porterage
     - Laundry
     - Etc.

2. **Daily Incidentals Cap**
   - Aggregate daily ceiling across all incidentals.

---

## Allowances (3)

1. **Day Trip Allowance**
   - Allowance for same-day return trips.

2. **Stay Day Allowance**
   - Allowance for full days at destination.

3. **Transit Day Allowance**
   - Allowance for departure and return days.

---

## Travel Advance (3)

1. **Travel Advances**
   - Gate: Enabled / Disabled.
   - Deviation fixed as **Disallow** (no exceptions).

2. **Maximum Advance Amount**
   - Fixed ceiling.
   - Alternative to Daily Advance Amount.

3. **Daily Advance Amount**
   - Per-day × duration ceiling.
   - Alternative to Maximum Advance Amount.

---

## Ground Transport (1)

1. **Permitted Car Type**
   - Sedan
   - SUV
   - Luxury
   - Van
   - Minibus
   - Etc.

---

## Cost Allocation — Travel Request (3)

1. **Cost Allocation on Travel Request**
   - Gate controlling whether `CostAllocations[]` appears on the request form.

2. **Cost Centre Allocation (on Request)**
   - Whether Cost Centre sub-field is active per allocation line.

3. **Project Allocation (on Request)**
   - Whether Project sub-field is active per allocation line.

---

## Cost Allocation — Expense Claim (12)

1. **Allocation Level**
   - Claim / LineItem / Disabled

2. **Allocation by Account**
   - Whether Account allocation is required.

3. **Account Allocation Fractional**
   - Whether allocation may be split across multiple accounts.

4. **Allocation by Cost Centre**
   - Whether Cost Centre allocation is required.

5. **Cost Centre Allocation Fractional**
   - Whether allocation may be split across multiple cost centres.

6. **Allocation by Business Unit**
   - Whether Business Unit allocation is required.

7. **Business Unit Allocation Fractional**
   - Whether allocation may be split across multiple business units.

8. **Allocation by Location**
   - Whether Location allocation is required.

9. **Location Allocation Fractional**
   - Whether allocation may be split across multiple locations.

10. **Allocation by Function**
    - Whether Function allocation is required.

11. **Function Allocation Fractional**
    - Whether allocation may be split across multiple functions.

12. **Additional Fractional Allocation Controls**
    - Included in total count calculation for allocation configurations.

---

# Total RuleGroups

**Total RuleGroups: 38**

> Note: Initial count was 34. Including all allocation fractional RuleGroups brings the total to **38**.

---

# Authoring Constraints

## Food Configuration

- **Daily Food Entitlement** and **Meal Caps** are mutually exclusive.
- Configure only one of them per employee group.

## Travel Advance Configuration

- **Maximum Advance Amount** and **Daily Advance Amount** are mutually exclusive.
- Configure only one of them.
- **Travel Advances** must be **Enabled** for either advance ceiling RuleGroup to take effect.

## Cost Allocation Configuration

- **Allocation Level** must not be set to **Disabled** if any Allocation By RuleGroups are configured.

---

# Single-Rule (MultipleRules = NotPermitted) RuleGroups

The following RuleGroups accept only **one Rule**. Any variation must be implemented through a **CheckWhen** scenario on that single Rule.

- Open Return Date
- Self Booking
- Hosted Meal Deduction Rate
- Travel Advances
- Maximum Advance Amount
- Daily Advance Amount
- Cost Allocation on Travel Request
- Cost Centre Allocation (on Request)
- Project Allocation (on Request)
