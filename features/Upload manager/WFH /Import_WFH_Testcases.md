# WFH Bulk Upload — Comprehensive Test Cases

# Template Column Reference

| Column Name                 | Mandatory | Valid Values / Format                                 |
| --------------------------- | --------- | ----------------------------------------------------- |
| Employee Code               | ✅ Yes     | Valid employee code                                   |
| Work From Home From         | ✅ Yes     | `dd-mm-yyyy`                                          |
| Work From Home Till         | ✅ Yes     | `dd-mm-yyyy`                                          |
| Reason                      | ✅ Yes     | Must match configured WFH reasons                     |
| Work From Home Request Type | ✅ Yes     | `S` = Single, `P` = Period                            |
| Work From Home Day          | ✅ Yes     | `dd-mm-yyyy`                                          |
| Work From Home Type         | ✅ Yes     | `AD` = All Day, `FP` = First Half, `SP` = Second Half |

---

# 🟢 Positive Test Cases — Expected to Pass

---

## TC-P01 — Single Day All-Day WFH

### Test Data

```text id="2v1w8t"
Employee Code       : EMP001
WFH From            : 25-05-2026
WFH Till            : 25-05-2026
Reason              : Personal Work
Request Type        : S
WFH Day             : 25-05-2026
WFH Type            : AD
```

### Expected Result

* Transaction created successfully
* User day updated as WFH — All Day

---

## TC-P02 — Single Day First-Half WFH

### Preconditions

* `PartialWFH = Enabled`

### Test Data

```text id="h7m4rc"
Employee Code       : EMP002
WFH From            : 25-05-2026
WFH Till            : 25-05-2026
Reason              : Doctor Appointment
Request Type        : S
WFH Day             : 25-05-2026
WFH Type            : FP
```

### Expected Result

* Transaction created successfully
* User day updated as WFH — First Half
* Scheduled From time used as mark-in

---

## TC-P03 — Single Day Second-Half WFH

### Preconditions

* `PartialWFH = Enabled`

### Test Data

```text id="22p9m9"
Employee Code       : EMP003
WFH From            : 25-05-2026
WFH Till            : 25-05-2026
Reason              : Home Repair
Request Type        : S
WFH Day             : 25-05-2026
WFH Type            : SP
```

### Expected Result

* Transaction created successfully
* User day updated as WFH — Second Half
* Scheduled Till time used as mark-out

---

## TC-P04 — Period WFH for Multiple Days

### Test Data

```text id="njst90"
Employee Code       : EMP004
WFH From            : 26-05-2026
WFH Till            : 30-05-2026
Reason              : Project Deadline
Request Type        : P
WFH Day             : 26-05-2026
WFH Type            : AD
```

### Expected Result

* WFH transaction created for the complete period
* All days marked as WFH — All Day

---

## TC-P05 — Period WFH with Mixed WFH Types

### Test Data

```text id="2llgxa"
Row 1:
EMP005 | 26-05-2026 | 30-05-2026 | WFH Week | P | 26-05-2026 | AD

Row 2:
EMP005 | 26-05-2026 | 30-05-2026 | WFH Week | P | 27-05-2026 | FP

Row 3:
EMP005 | 26-05-2026 | 30-05-2026 | WFH Week | P | 28-05-2026 | SP
```

### Expected Result

* Each day receives the correct WFH type
* Mixed WFH types processed successfully

---

## TC-P06 — Future-Date WFH Request

### Test Data

```text id="9xq8hc"
Employee Code       : EMP006
WFH From            : 10-06-2026
WFH Till            : 10-06-2026
Reason              : Personal
Request Type        : S
WFH Day             : 10-06-2026
WFH Type            : AD
```

### Expected Result

* Request accepted if within configured MaxAdvanceRequestPeriod

---

## TC-P07 — Valid Retrospective Request

### Preconditions

* Upload date: `22-05-2026`
* Retrospective requests enabled

### Test Data

```text id="r8jmx5"
Employee Code       : EMP007
WFH From            : 20-05-2026
WFH Till            : 20-05-2026
Reason              : Connectivity Issue
Request Type        : S
WFH Day             : 20-05-2026
WFH Type            : AD
```

### Expected Result

* Request accepted if within MaxRetrospectivePeriod

---

# 🔴 Negative Test Cases — Expected to Fail

---

## TC-N01 — Employee Code Missing

### Expected Result

* Row rejected
* Validation message: Employee Code is mandatory

---

## TC-N02 — Invalid Employee Code

### Test Data

```text id="5mv4xv"
Employee Code : EMP9999
```

### Expected Result

* Row rejected
* Employee not found

---

## TC-N03 — Invalid Date Format

### Test Data

```text id="4q9r6m"
WFH From : 2026/05/25
```

### Expected Result

* Row rejected
* Date format must be `dd-mm-yyyy`

---

## TC-N04 — Till Date Earlier Than From Date

### Expected Result

* Row rejected
* Till date cannot be before From date

---

## TC-N05 — Invalid Request Type

### Test Data

```text id="d8y9bh"
Request Type : X
```

### Expected Result

* Row rejected
* Valid values are `S` or `P`

---

## TC-N06 — Invalid WFH Type

### Test Data

```text id="md3qgv"
WFH Type : HD
```

### Expected Result

* Row rejected
* Valid values are `AD`, `FP`, or `SP`

---

## TC-N07 — Partial WFH Not Allowed

### Preconditions

* `PartialWFH = Disabled`

### Test Data

```text id="cbjlwm"
WFH Type : FP
```

### Expected Result

* Row rejected
* Partial WFH not permitted

---

## TC-N08 — Invalid Reason

### Test Data

```text id="d2k3kp"
Reason : Vacation
```

### Expected Result

* Row rejected
* Reason not configured in tenant settings

---

## TC-N09 — WFH Day Outside Date Range

### Expected Result

* Row rejected
* WFH Day must fall within WFH From and Till dates

---

## TC-N10 — Duplicate WFH Request

### Preconditions

* Existing WFH already present for same employee and date

### Expected Result

* Row rejected
* Duplicate request detected

---

## TC-N11 — Max Request Limit Exceeded

### Expected Result

* Row rejected
* Max WFH request limit exceeded

---

## TC-N12 — Retrospective Limit Exceeded

### Expected Result

* Row rejected
* Retrospective period exceeded

---

## TC-N13 — Advance Request Limit Exceeded

### Expected Result

* Row rejected
* Advance request period exceeded

---

## TC-N14 — Retrospective Request Disabled

### Preconditions

* `RetrospectiveRequest = Disabled`

### Expected Result

* Row rejected
* Retrospective WFH not allowed

---

## TC-N15 — Employee Not Covered Under WFH Applicability

### Expected Result

* Row rejected
* Employee not covered under any WFH feature applicability

---

# 🟡 Edge / Boundary Test Cases

---

## TC-E01 — Single-Day Request

### Test Data

```text id="6fczg4"
Request Type : S
WFH From     : 25-05-2026
WFH Till     : 25-05-2026
```

### Expected Result

* Valid single-day WFH request

---

## TC-E02 — Period Request with Same From and Till Date

### Test Data

```text id="cn6zq3"
Request Type : P
WFH From     : 25-05-2026
WFH Till     : 25-05-2026
```

### Expected Result

* System accepts or warns
* One-day period considered technically valid

---

## TC-E03 — Weekend / Holiday WFH

### Expected Result

* System behavior depends on policy configuration
* May reject or warn for non-working day

---

## TC-E04 — Blank Rows Between Data

### Expected Result

* Blank rows skipped gracefully
* Valid rows processed successfully

---

## TC-E05 — Large Batch Upload

### Test Data

```text id="n4v2tl"
100+ valid rows
```

### Expected Result

* All rows processed successfully
* No timeout or partial processing failure

---

## TC-E06 — Same Employee with Non-Overlapping Dates

### Expected Result

* Both requests processed independently

---

## TC-E07 — Same Employee with Overlapping Dates

### Expected Result

* Overlapping row rejected
* Duplicate/conflicting period validation triggered

---

# Test Coverage Summary

| Category              | Count | Coverage                                    |
| --------------------- | ----- | ------------------------------------------- |
| Positive Test Cases   | 7     | Happy-path validations                      |
| Negative Test Cases   | 15    | Validation failures and policy restrictions |
| Edge / Boundary Cases | 7     | Boundary behavior and robustness checks     |
| Total                 | 29    | End-to-end bulk upload validation coverage  |
