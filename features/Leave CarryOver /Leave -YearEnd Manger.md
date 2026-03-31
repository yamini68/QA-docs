
## LastAction Codes - Simple Definitions

| Code | Meaning |
|------|---------|
| **CN** | Cancelled |
| **DE** | Deleted |
| **RP** | Rejected |
| **RI** | Returned |
| **AR** | Auto-Rejected |
| **AP** | Approved |

**Rule**: Transactions with LastAction = CN/DE/RP/RI/AR are **excluded** from `LeaveYearLeavesAvailed` calculation. Only AP (and other non-excluded actions) are counted.
