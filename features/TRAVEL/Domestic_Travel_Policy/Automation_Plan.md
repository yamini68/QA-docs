# 📋 Domestic Travel Policy - Comprehensive Automation Plan

## Overview
This automation plan covers **34 RuleGroups** across the Domestic Travel Policy ETT, including UI validation, data persistence, E2E workflows, and governance lifecycle testing.

---

## 🎯 Phase 1: RuleGroup-Level UI & Functional Validation

### **Objective:** Validate each RuleGroup independently

### **Test Categories per RuleGroup:**

#### **A. UI Rendering Tests** (For each RuleGroup)
```
✅ RuleGroup section expands/collapses
✅ "Add Rule" button is visible and clickable
✅ Field labels match spec (ValueLabel property)
✅ Input field types are correct:
   - Amount fields → Decimal input + Currency dropdown
   - Duration fields → Number input + Unit dropdown
   - Options fields → Dropdown from lookup
   - Presence fields → Toggle switch
   - Decimal fields → Decimal input
✅ Deviation radio buttons (Allow/Block/Ignore) when Configurable
✅ "Apply under specific condition" toggle visibility
✅ Helper text/description displays correctly
✅ Validation messages for empty/invalid data
```

#### **B. Data Persistence Tests** (For each RuleGroup)
```
✅ Create rule with valid data → Save → Verify in list view
✅ Edit existing rule → Save → Verify changes persist
✅ Delete rule → Confirm → Verify removal
✅ Create duplicate rules (where MultipleRules=Permitted)
✅ Create rule without condition (catch-all)
✅ Create rule with CheckWhen scenario
✅ Currency validation (ISO 4217 codes)
✅ Negative value rejection (for Amount/Duration)
✅ Zero value acceptance/rejection per business logic
✅ Decimal precision validation
```

#### **C. RuleGroup-Specific Test Data Matrix**

| RuleGroup ID | RuleGroup Name | Test Data Examples | Validation Points |
|--------------|----------------|-------------------|-------------------|
| 1746316800001 | **Minimum Lead Time** | 3 days, 5 days, 7 days | IN (Integer), MinLimit |
| 1746316800002 | **Maximum Trip Duration** | 7 days, 14 days, 30 days | DD (Duration), MaxLimit |
| 1746316800003 | **Open Return Date** | Enabled, Disabled | Presence type |
| 1746316800004 | **Self Booking** | Enabled, Disabled | Presence type |
| 1746316800005 | **Permitted Travel Mode** | Air=Exception, Rail=Permitted | Options (Travel Modes lookup) ⚠️ |
| 1746316800006 | **Permitted Travel Class** | Economy=Permitted, Business=Exception | Options (Travel Modes lookup) ⚠️ |
| 1746316800007 | **Permitted Property Type** | Hotel=Permitted, Airbnb=Exception | Options (Accommodation lookup) ⚠️ |
| 1746316800008 | **Permitted Star Rating** | 3-star=Permitted, 5-star=Exception | Options (Star Rating lookup) ⚠️ |
| 1746316800009 | **Maximum Stay Rate** | ₹3500, ₹5000, ₹7500 | Amount + Currency |
| 1746316800010 | **Daily Food Entitlement** | ₹800, ₹1200, ₹1500 | Amount + Currency |
| 1746316800011 | **Meal Caps** | Breakfast₹200, Lunch₹350, Dinner₹250 | Amounts (3 fields) |
| 1746316800012 | **Hosted Meal Deduction Rate** | 0.33, 0.25, 0.50 | DE (Decimal), default 0.33 |
| 1746316800013 | **Permitted Commute Mode** | Taxi=Permitted, Own Vehicle=Exception | Options (Commute Modes lookup) ⚠️ |
| 1746316800014 | **Commute Ride Cap** | ₹300, ₹500, ₹750 | Amount + Currency |
| 1746316800015 | **Mileage Rate** | ₹8/km, ₹12/km, ₹15/km | Amount + Currency |
| 1746316800016 | **Incidental Expenses** | Porterage=Permitted, Laundry=Exception | Options (Incidental Types lookup) ⚠️ |
| 1746316800017 | **Daily Incidentals Cap** | ₹200, ₹300, ₹500 | Amount + Currency |
| 1746316800018 | **Day Trip Allowance** | ₹500, ₹750, ₹1000 | Amount + Currency |
| 1746316800019 | **Stay Day Allowance** | ₹1200, ₹1500, ₹2000 | Amount + Currency |
| 1746316800020 | **Transit Day Allowance** | ₹700, ₹900, ₹1200 | Amount + Currency |
| 1746316800021 | **Travel Advances** | Enabled, Disabled | Presence type, Deviation=Disallow |
| 1746316800022 | **Maximum Advance Amount** | ₹10000, ₹25000, ₹50000 | Amount + Currency |
| 1746316800023 | **Daily Advance Amount** | ₹2000/day, ₹3000/day | Amount + Currency |

**⚠️ Lookup-dependent RuleGroups** - Require platform data setup

---

## 🔄 Phase 2: Integration Testing (RuleGroup Interactions)

### **Objective:** Validate RuleGroups that interact with each other

#### **Test Scenario 1: Alternative Models - Food Entitlement**
```
📝 Configure Daily Food Entitlement → Save → Verify
📝 Configure Meal Caps → Save → Verify
📝 Attempt to configure BOTH for same employee group
✅ Expected: System should warn or prevent conflicting configurations
```

#### **Test Scenario 2: Alternative Models - Advance Amount**
```
📝 Configure Maximum Advance Amount → Save → Verify
📝 Configure Daily Advance Amount → Save → Verify
📝 Attempt to configure BOTH simultaneously
✅ Expected: System should warn or prevent conflicting configurations
```

#### **Test Scenario 3: Commute Mode Conditionality**
```
📝 Configure Permitted Commute Mode (Taxi, Own Vehicle)
📝 Configure Commute Ride Cap (for Taxi)
📝 Configure Mileage Rate (for Own Vehicle)
✅ Expected: Both write to CommutingExpenses.Amount but apply based on mode
```

#### **Test Scenario 4: Travel Advance Dependency**
```
📝 Set Travel Advances = Disabled
📝 Attempt to configure Maximum Advance Amount
✅ Expected: System should warn that advance rules won't apply
📝 Set Travel Advances = Enabled
📝 Configure Maximum Advance Amount
✅ Expected: Rule saves successfully
```

#### **Test Scenario 5: Allocation Level Dependency**
```
📝 Set Allocation Level = Disabled
📝 Attempt to configure Allocation by Account/Cost Centre/etc.
✅ Expected: System should warn these rules have no effect
📝 Set Allocation Level = Claim or LineItem
📝 Configure Allocation rules
✅ Expected: Rules save and apply correctly
```

---

## 🎬 Phase 3: End-to-End (E2E) Policy Configuration

### **Objective:** Complete policy configuration with realistic scenarios

### **E2E Test Case 1: Multi-Grade Policy Setup**

```
🎯 Scenario: Configure policy for 3 employee grades

Step 1: Create new Domestic Travel Policy ETY
   - Navigate to Travel → Domestic Travel Policy
   - Click "Create"
   - Enter policy name: "DTP_Regression_Test_v1"
   
Step 2: Configure Trip Envelope Rules
   - Minimum Lead Time: 
     * Rule 1: Grade 1-3 → 5 days
     * Rule 2: Grade 4-5 → 2 days
     * Rule 3: Grade 6+ → 0 days (no restriction)
   
Step 3: Configure Maximum Trip Duration
   - Rule 1: All grades → 14 days
   
Step 4: Configure Booking & Travel Mode
   - Self Booking: Disabled (all through travel desk)
   - Permitted Travel Mode:
     * Grade 1-3: Rail=Permitted, Air=Exception
     * Grade 4+: Air=Permitted, Rail=Permitted
   
Step 5: Configure Accommodation
   - Permitted Property Type: Hotel=Permitted for all
   - Permitted Star Rating:
     * Grade 1-3: Max 3-star
     * Grade 4-5: Max 4-star
     * Grade 6+: Max 5-star
   - Maximum Stay Rate:
     * Metro: ₹3500/night
     * Tier-1: ₹2500/night
     * Tier-2: ₹1500/night
   
Step 6: Configure Food
   - Daily Food Entitlement:
     * Metro: ₹1000/day
     * Tier-1: ₹800/day
     * Tier-2: ₹600/day
   - Hosted Meal Deduction Rate: 0.33
   
Step 7: Configure Commute
   - Permitted Commute Mode: All modes=Permitted
   - Commute Ride Cap: ₹400/ride
   - Mileage Rate: ₹12/km
   
Step 8: Configure Incidentals
   - Permitted Incidental Types: Porterage, Tips=Permitted
   - Daily Incidentals Cap: ₹200/day
   
Step 9: Configure Allowances
   - Day Trip Allowance: ₹500
   - Stay Day Allowance: ₹1200
   - Transit Day Allowance: ₹700
   
Step 10: Configure Advances
   - Travel Advances: Enabled
   - Maximum Advance Amount: ₹25000
   
Step 11: Save Policy
   - Click "Save"
   - Verify all RuleGroups saved correctly
   - Navigate to each RuleGroup → Confirm data persistence

✅ Validation Points:
   - All 23 RuleGroups configured successfully
   - No validation errors
   - Data persists after page refresh
   - Rule count displayed correctly in each section
```

### **E2E Test Case 2: Policy with CheckWhen Scenarios**

```
🎯 Scenario: Configure destination-based variation

Step 1: Create policy with destination category variation
   - Maximum Stay Rate:
     * Rule 1: CheckWhen = DestinationCategory = "Metro"
       Value: ₹5000
     * Rule 2: CheckWhen = DestinationCategory = "Tier-1"
       Value: ₹3500
     * Rule 3: CheckWhen = DestinationCategory = "Tier-2"
       Value: ₹2000
       
Step 2: Configure trip purpose variation
   - Minimum Lead Time:
     * Rule 1: CheckWhen = TripPurpose = "Client Meeting"
       Value: 3 days
     * Rule 2: CheckWhen = TripPurpose = "Training"
       Value: 7 days
     * Rule 3: CheckWhen = TripPurpose = "Project Deployment"
       Value: 5 days

✅ Validation Points:
   - CheckWhen scenarios save correctly
   - Multiple rules per RuleGroup work as expected
   - Rules are mutually exclusive (no overlap)
```

### **E2E Test Case 3: Policy Copy & Modification**

```
🎯 Scenario: Copy existing policy and modify

Step 1: Create base policy (Policy_A) with minimal rules
Step 2: Use "Copy Policy" function (if available)
Step 3: Create Policy_B as copy of Policy_A
Step 4: Modify 5 RuleGroups in Policy_B
Step 5: Save Policy_B
Step 6: Verify Policy_A remains unchanged
Step 7: Verify Policy_B has updated values

✅ Validation Points:
   - Copy functionality works
   - Original policy unchanged
   - Modified rules saved correctly
```

---

## 🔄 Phase 4: Governance Lifecycle Testing

### **Objective:** Test Draft → Submit → Check → Approve workflow

### **Test Scenario 1: MakerOnly Governance**

```
📋 Prerequisite: GovernanceTier = MakerOnly

Step 1: Create new policy → Status = Draft
Step 2: Configure all RuleGroups
Step 3: Click "Submit" (or "Activate")
Step 4: Verify Status changes to Active
Step 5: Verify policy appears in PEM cache

✅ Validation Points:
   - Draft → Active in single step
   - No approval workflow triggered
   - Policy immediately available for evaluation
```

### **Test Scenario 2: MakerApprover Governance**

```
📋 Prerequisite: GovernanceTier = MakerApprover

Step 1: Create policy → Status = Draft
Step 2: Configure RuleGroups
Step 3: Click "Submit" → Status = Submitted
Step 4: Verify workflow notification sent to approver
Step 5: Login as approver
Step 6: Review policy → Click "Approve"
Step 7: Verify Status = Active
Step 8: Verify policy in PEM cache

✅ Validation Points:
   - Draft → Submitted → Active flow
   - Approver can view all RuleGroups
   - Approval action activates policy
   - IFTTTEvent published on activation
```

### **Test Scenario 3: MakerCheckerApprover Governance**

```
📋 Prerequisite: GovernanceTier = MakerCheckerApprover

Step 1: Create policy → Status = Draft
Step 2: Configure RuleGroups
Step 3: Click "Submit" → Status = Submitted
Step 4: Login as checker
Step 5: Review policy → Click "Check"
Step 6: Status = Checked
Step 7: Login as approver
Step 8: Review → Click "Approve"
Step 9: Status = Active

✅ Validation Points:
   - Full 4-state workflow
   - Checker cannot approve
   - Approver sees checked status
   - Each transition publishes IFTTTEvent
```

### **Test Scenario 4: Policy Versioning & Updates**

```
🎯 Scenario: Update active policy and verify versioning

Step 1: Create Policy_v1 → Activate
Step 2: Verify Policy_v1 is Active
Step 3: Click "Edit" on active policy
Step 4: System creates Policy_v2 (Draft)
Step 5: Modify 3 RuleGroups in v2
Step 6: Submit v2 for approval
Step 7: Approve v2
Step 8: Verify:
   - Policy_v1 → Status = Superseded
   - Policy_v2 → Status = Active
   - Only v2 appears in PEM cache

✅ Validation Points:
   - Version increment works
   - Previous version superseded
   - PEM cache refreshes
   - New transactions use v2
   - Historical transactions reference v1
```

### **Test Scenario 5: Policy Deactivation**

```
🎯 Scenario: Deactivate policy

Step 1: Create and activate policy
Step 2: Click "Deactivate"
Step 3: Confirm deactivation
Step 4: Verify Status = Inactive
Step 5: Verify policy removed from PEM cache
Step 6: Attempt to create travel request
Step 7: Verify system handles missing policy gracefully

✅ Validation Points:
   - Deactivation works
   - Policy unavailable for new requests
   - Existing requests not affected
```

### **Test Scenario 6: Return/Reject Workflow**

```
🎯 Scenario: Policy returned for corrections

Step 1: Create policy → Submit
Step 2: Login as approver
Step 3: Click "Return" with comments
Step 4: Verify Status = Draft (or Returned)
Step 5: Login as maker
Step 6: View return comments
Step 7: Make corrections
Step 8: Re-submit
Step 9: Approve

✅ Validation Points:
   - Return functionality works
   - Comments visible to maker
   - Policy can be re-submitted
   - Version not incremented on return
```

---

## 🧪 Phase 5: Regression Test Suite

### **Objective:** Automated regression tests for continuous integration

### **Daily Regression Suite** (Run on every build)

```
✅ Smoke Tests (5 minutes)
   1. Navigate to Domestic Travel Policy
   2. Create new policy (Draft)
   3. Configure 3 critical RuleGroups:
      - Minimum Lead Time
      - Maximum Stay Rate
      - Travel Advances
   4. Save policy
   5. Verify data persistence
   6. Delete test policy

✅ Critical Path Tests (15 minutes)
   1. Create policy with all 23 RuleGroups
   2. Save and verify
   3. Edit policy
   4. Update 5 RuleGroups
   5. Save and verify changes
   6. Delete policy

✅ Lookup Validation (10 minutes)
   1. Verify Travel Modes lookup loads
   2. Verify Accommodation Types lookup loads
   3. Verify Commute Modes lookup loads
   4. Verify Incidental Expense Types lookup loads
   5. Verify Star Rating lookup loads
   6. Verify Currency dropdown (ISO 4217) loads
```

### **Weekly Regression Suite** (Run weekly)

```
✅ Full E2E Configuration (30 minutes)
   - Complete policy setup with all RuleGroups
   - Multiple rules per RuleGroup
   - CheckWhen scenarios
   - Save, edit, re-save
   
✅ Governance Workflows (20 minutes)
   - MakerOnly flow
   - MakerApprover flow
   - MakerCheckerApprover flow
   - Version updates
   
✅ Alternative Model Validation (15 minutes)
   - Daily Food vs Meal Caps
   - Maximum vs Daily Advance
   - Allocation Level dependencies
```

### **Pre-Release Regression Suite** (Before each release)

```
✅ Complete Functional Coverage (2 hours)
   - All RuleGroups individually
   - All integration scenarios
   - All governance tiers
   - All lookup validations
   - All validation rules
   - All error scenarios
   
✅ Performance Tests (30 minutes)
   - Policy load time with 23 RuleGroups
   - Save time for large policy
   - PEM evaluation time
   - Cache refresh time
   
✅ Cross-Browser Tests (30 minutes)
   - Chrome
   - Firefox
   - Safari
   - Edge
```

---

## 📊 Test Data Management

### **Pre-Requisite Data Setup**

```
📋 Platform Data (Required before testing)
   1. Travel Modes service database
      - Air (Economy, Business, First)
      - Rail (AC Chair Car, Sleeper, AC 2-Tier)
      - Road (Bus, Car, Taxi)
      - Sea (Ferry, Cruise)
   
   2. Accommodation Types service database
      - Hotel
      - B&B
      - Company Guest House
      - Friends and Family
      - Hosted
      - Serviced Residence
      - Apartment
      - Corporate Housing
   
   3. Commute Modes lookup
      - Public Transport
      - Auto-rickshaw
      - Taxi
      - App Cab
      - Rental Car
      - Car with Driver
      - Own Vehicle
   
   4. Incidental Expense Types lookup
      - Porterage
      - Tips and Gratuities
      - Laundry and Dry Cleaning
      - Telephone and Internet
      - Medical
      - Travel Insurance
      - Newspapers and Sundries
      - Personal Safety Equipment
   
   5. Star Rating lookup
      - 1-star to 5-star
   
   6. Grade structure
      - Grade 1 to Grade 6+
   
   7. Destination categories
      - Metro
      - Tier-1
      - Tier-2
   
   8. Trip purposes
      - Client Meeting
      - Training
      - Project Deployment
      - Internal Review
```

### **Test Data Cleanup**

```
✅ After each test run:
   - Delete test policies
   - Clear PEM cache
   - Reset governance tier to default
   - Clear workflow queues
   
✅ Automated cleanup script:
   - Run before each test suite
   - Remove all policies with "Test_" prefix
   - Reset test users to default state
```

---

## 🛠️ Automation Framework Requirements

### **Tools & Technologies**

```
✅ UI Automation:
   - Selenium WebDriver / Playwright / Cypress
   - Page Object Model (POM) design pattern
   - Data-driven testing (JSON/Excel test data)
   
✅ API Testing:
   - REST Assured / Postman / Newman
   - Validate PEM evaluation responses
   - Validate IFTTTEvent publishing
   
✅ Performance Testing:
   - JMeter / k6
   - Load testing for PEM evaluation
   
✅ Test Management:
   - TestRail / Zephyr / Xray
   - Test case versioning
   - Execution history tracking
```

### **Page Objects Required**

```
📄 DomesticTravelPolicyPage
   - navigateToPolicy()
   - createNewPolicy()
   - expandRuleGroup(ruleGroupName)
   - addRule(ruleGroupName, ruleData)
   - editRule(ruleGroupName, ruleIndex, ruleData)
   - deleteRule(ruleGroupName, ruleIndex)
   - savePolicy()
   - submitPolicy()
   - approvePolicy()
   - getRuleGroupData(ruleGroupName)
   - validateRuleGroupUI(ruleGroupName)
   
📄 RuleGroupComponents
   - AmountField (currency dropdown, amount input)
   - DurationField (unit dropdown, number input)
   - OptionsField (multi-select dropdown)
   - PresenceField (toggle switch)
   - DecimalField (decimal input)
   - DeviationRadio (Allow/Block/Ignore)
   - CheckWhenToggle
   - ScenarioBuilder
   
📄 WorkflowPage
   - viewDraftPolicies()
   - viewSubmittedPolicies()
   - approvePolicy(policyId)
   - rejectPolicy(policyId, comments)
   - returnPolicy(policyId, comments)
   - checkPolicy(policyId)
```

### **Test Data Structure**

```json
{
  "testPolicy": {
    "policyName": "DTP_Test_Automation_001",
    "ruleGroups": [
      {
        "ruleGroupId": "1746316800001",
        "ruleGroupName": "Minimum Lead Time",
        "rules": [
          {
            "ruleName": "Junior_Grades_Lead_Time",
            "value": 5,
            "currency": null,
            "deviation": "Allow with warning",
            "checkWhen": {
              "grade": ["1", "2", "3"]
            }
          },
          {
            "ruleName": "Senior_Grades_Lead_Time",
            "value": 2,
            "currency": null,
            "deviation": "Allow with warning",
            "checkWhen": {
              "grade": ["4", "5", "6"]
            }
          }
        ]
      },
      {
        "ruleGroupId": "1746316800009",
        "ruleGroupName": "Maximum Stay Rate",
        "rules": [
          {
            "ruleName": "Metro_Hotel_Rate",
            "value": 3500,
            "currency": "INR",
            "deviation": "Allow with warning",
            "checkWhen": {
              "destinationCategory": "Metro"
            }
          }
        ]
      }
    ]
  }
}
```

---

## 📈 Test Coverage Metrics

### **Coverage Goals**

```
✅ RuleGroup Coverage: 100% (34/34 RuleGroups)
   - 23 Domestic Travel Policy RuleGroups
   - 11 Allocation-related RuleGroups
   
✅ UI Validation: 100%
   - All input fields
   - All dropdowns
   - All buttons
   - All validation messages
   
✅ Functional Coverage: 100%
   - Create operations
   - Read operations
   - Update operations
   - Delete operations
   
✅ Workflow Coverage: 100%
   - MakerOnly
   - MakerApprover
   - MakerCheckerApprover
   
✅ Integration Coverage: 100%
   - All RuleGroup interactions
   - Alternative models
   - Dependencies
```

---

## 🚀 Execution Schedule

```
📅 Daily (CI/CD Pipeline)
   - Smoke tests
   - Critical path tests
   - Lookup validation
   Duration: 30 minutes
   
📅 Weekly (Regression)
   - Full E2E configuration
   - Governance workflows
   - Alternative models
   Duration: 1.5 hours
   
📅 Pre-Release
   - Complete functional coverage
   - Performance tests
   - Cross-browser tests
   Duration: 3 hours
   
📅 On-Demand
   - Specific RuleGroup testing
   - Bug verification
   - Hotfix validation
```

---

## 📝 Defect Reporting Template

```
**Defect Title:** [RuleGroup Name] - [Brief description]

**Severity:** Critical / High / Medium / Low

**RuleGroup:** [Name and ID]

**Steps to Reproduce:**
1. Navigate to...
2. Click on...
3. Enter...

**Expected Result:**
[What should happen per spec]

**Actual Result:**
[What actually happened]

**Evidence:**
- Screenshot/Video
- Console logs
- Network request/response

**Spec Reference:**
[Page number and section from Domestic_Travel_Policy.pdf]

**Impact:**
[How this affects policy configuration or evaluation]
```

---

## ✅ Success Criteria

```
✅ All 34 RuleGroups validated
✅ 100% UI compliance with spec
✅ 100% data persistence accuracy
✅ All governance workflows functional
✅ Zero critical/high severity defects
✅ Performance within acceptable limits
✅ Cross-browser compatibility confirmed
✅ Regression suite passing at 100%
✅ Documentation complete
✅ Test data reusable for future cycles
```

---

This automation plan provides **complete coverage** of the Domestic Travel Policy feature, from individual RuleGroup validation to end-to-end policy configuration and governance lifecycle testing. The modular approach allows for **efficient regression testing** and **scalable maintenance** as the feature evolves.
