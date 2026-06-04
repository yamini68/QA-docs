#### Chunk 1 — Dynamic CSV Generation (Pre-condition)

Before uploading, auto-generate the CSV with a **future date that hasn't been used**.

**Strategy options:**

*   **Today + N days offset** — calculate today + 7 days (or any buffer) at runtime, write it into the CSV dynamically before each run
    
*   **Date pool file** — maintain a used\_dates.json, pick the next unused date, mark it used after the run
    
*   **Timestamp-based** — always use today's date + run timestamp mod to guarantee uniqueness
    

The cleanest for CI/CD: **runtime date calculation** — no state file to manage.

#### Chunk 2 — Upload Flow (Admin Side)

1.  Login to admin panel
    
2.  Navigate to Upload Manager → WFH Bulk Upload
    
3.  Generate CSV dynamically (from Chunk 1) and upload it
    
4.  Assert upload success response / no error toast
    

#### Chunk 3 — Verification on Employee Side

1.  Login with employee credentials
    
2.  Navigate to Calendar view → assert the specific date shows WFH marker
    
3.  Navigate to Listing/Transaction view → assert the WFH transaction record exists with correct date and status
    

#### Chunk 4 — Teardown (Optional but Recommended)

*   Cancel/delete the WFH entry after test so the same date _could_ be reused if needed
    
*   Or log the used date so future runs skip it
