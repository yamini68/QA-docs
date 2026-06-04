You can represent it as a clean execution flow like this:

```text
WFH Bulk Upload Validation Flow
│
├── 1. Dynamic CSV Generation (Pre-Condition)
│   │
│   ├── Calculate Future Date
│   │     └── Today + 7 Days (Runtime)
│   │
│   ├── Update CSV Template
│   │     └── Replace WFH Date with Generated Date
│   │
│   └── Save Generated CSV
│
├── 2. Admin Upload Flow
│   │
│   ├── Login → Admin Portal
│   │
│   ├── Navigate
│   │     └── Upload Manager
│   │           └── WFH Bulk Upload
│   │
│   ├── Upload Generated CSV
│   │
│   └── Validate
│         ├── Upload Successful
│         └── No Error Toast / Validation Errors
│
├── 3. Employee Verification Flow
│   │
│   ├── Login → Employee Portal
│   │
│   ├── Calendar Verification
│   │     └── Generated Future Date displays WFH Marker
│   │
│   └── Transaction Verification
│         ├── WFH Request Exists
│         ├── Date Matches Uploaded Date
│         └── Status is Correct
│
└── 4. Cleanup / Teardown (Optional)
    │
    ├── Cancel/Delete WFH Request
    │
    └── OR
          └── Log Used Date
                └── Skip in Future Runs
```



