
---

# 🧪 Test Suite: Settings Transaction Page — Minimum Lead Time Rule Configuration
**Scope:** Configuration UI only (no travel request submission flow)  
**Environment:** Staging / Pre-release  
**User Role:** Admin / Policy Configurator  

---

## 🔹 TC-MLT-SETTINGS-001: Verify Settings Transaction Page Loads Correctly with Minimum Lead Time Rule Group Visible and Accessible

**Objective:** Confirm the Settings Transaction page renders correctly and the Minimum Lead Time rule group is visible, labeled, and interactive.

**Pre-conditions:**
1. User is logged into the HR/Travel system with Admin or Policy Configurator role
2. User navigates to Settings → Transaction Rules → Minimum Lead Time section
3. Feature flag for "Minimum Lead Time Rules" is enabled in staging

**Test Steps:**
1. Navigate to Settings → Transaction Rules
2. Scroll to locate "Minimum Lead Time" rule group header
3. Verify header displays correct title and description
4. Verify expand/collapse toggle is present and functional
5. Click to expand the rule group
6. Verify "+Add Rule" button is visible and enabled
7. Verify rule counter displays "0 rules" (if no rules exist)
8. Verify info/help icon is present next to rule group title
9. Hover over info icon and verify tooltip displays helpful guidance

**Expected Results:**
- ✓ Page loads without errors or console warnings
- ✓ "Minimum Lead Time" header is clearly labeled with description: "The minimum number of days in advance a travel request must be submitted before the trip begins..."
- ✓ Expand/collapse toggle works smoothly
- ✓ "+Add Rule" button is enabled and clickable
- ✓ Rule counter shows accurate count (e.g., "0 rules")
- ✓ Info icon tooltip explains purpose and usage
- ✓ No broken UI elements or missing translations
- ✓ Page is responsive on desktop and tablet viewports

---

## 🔹 TC-MLT-SETTINGS-002: Verify Add Rule Modal Opens with All Required Fields, Default Values, and Validation States

**Objective:** Confirm the "Add Rule" modal dialog displays all necessary inputs, correct defaults, and proper validation behavior.

**Pre-conditions:**
1. User is on Settings Transaction page
2. Minimum Lead Time rule group is expanded

**Test Steps:**
1. Click "+Add Rule" button
2. Verify modal dialog opens with title "Add Rule"
3. Verify "Rule Name" input field is present, focused, and editable
4. Verify "Minimum" numeric input field is present with spinner controls
5. Verify "Days" dropdown is present with "Days" pre-selected
6. Verify "If rule is violated:" section shows three radio buttons:
   - "Allow with warning" (selected by default)
   - "Block submission"
   - "Ignore rule"
7. Verify toggle "Apply this rule only under a specific condition" is present and disabled (with info tooltip)
8. Verify "Done" button is disabled initially (required fields empty)
9. Verify "Cancel" button is enabled
10. Verify close (X) button in top-right corner is functional
11. Enter valid rule name and minimum value; verify "Done" button becomes enabled
12. Clear rule name; verify "Done" button disables again

**Expected Results:**
- ✓ Modal opens centered with proper overlay
- ✓ All fields are present, labeled, and accessible
- ✓ Default selections are logical (e.g., "Allow with warning" pre-selected)
- ✓ "Done" button state reflects form validity (disabled → enabled)
- ✓ Disabled toggle shows tooltip: "Scenarios must be configured before conditional rules can be created"
- ✓ Keyboard navigation works (Tab through fields, Enter to submit if valid)
- ✓ Modal can be closed via Cancel, X, or Escape key
- ✓ No console errors or UI flicker

---

## 🔹 TC-MLT-SETTINGS-003: Verify Successful Creation of a Minimum Lead Time Rule with Valid Inputs and "Allow with Warning" Action

**Objective:** Confirm a rule can be created with valid data and persists correctly in the UI.

**Pre-conditions:**
1. Add Rule modal is open
2. No existing rules (or system allows duplicates)

**Test Steps:**
1. Enter Rule Name: "Test Rule - 7 Days Advance Notice"
2. Set Minimum field to: 7
3. Verify "Days" dropdown remains "Days"
4. Confirm "Allow with warning" radio button is selected
5. Verify toggle remains disabled
6. Click "Done" button
7. Verify modal closes smoothly
8. Verify rule appears in the rule list under Minimum Lead Time section
9. Verify rule counter updates from "0 rules" to "1 rule"
10. Verify rule summary displays: "Minimum 7 Days - Allow with warning"
11. Click the rule to open edit mode
12. Verify all saved values are pre-filled correctly

**Expected Results:**
- ✓ Rule saves without error
- ✓ Modal closes and page updates instantly
- ✓ Rule appears in list with correct name and summary
- ✓ Rule counter increments accurately
- ✓ Edit mode shows all values preserved (name, minimum, action)
- ✓ Rule is marked as "Active" by default
- ✓ No duplicate rules created on rapid double-click
- ✓ Audit log (if visible) records creation event

---

## 🔹 TC-MLT-SETTINGS-004: Verify Rule Creation with "Block Submission" Action and Validation of UI Feedback

**Objective:** Confirm rule with "Block submission" action saves correctly and UI reflects stricter enforcement intent.

**Pre-conditions:**
1. Add Rule modal is open

**Test Steps:**
1. Enter Rule Name: "Strict Policy - 14 Days Required"
2. Set Minimum to: 14
3. Select radio button: "Block submission"
4. Click "Done"
5. Verify rule appears in list
6. Verify rule summary shows: "Minimum 14 Days - Block submission"
7. Verify visual indicator (e.g., red badge or icon) denotes blocking action
8. Click to edit rule
9. Change action to "Allow with warning"
10. Save and verify summary updates accordingly

**Expected Results:**
- ✓ Rule saves with "Block submission" action
- ✓ UI provides clear visual distinction for blocking rules (color, icon, label)
- ✓ Summary text accurately reflects selected action
- ✓ Editing and re-saving updates the rule without creating duplicates
- ✓ No console errors or unexpected behavior

---

## 🔹 TC-MLT-SETTINGS-005: Verify Rule Creation with "Ignore Rule" Action and Confirmation of Inactive Enforcement State

**Objective:** Confirm "Ignore rule" action creates a rule that is saved but does not enforce logic.

**Pre-conditions:**
1. Add Rule modal is open

**Test Steps:**
1. Enter Rule Name: "Inactive Test Rule - 5 Days"
2. Set Minimum to: 5
3. Select radio button: "Ignore rule"
4. Click "Done"
5. Verify rule appears in list
6. Verify rule summary shows: "Minimum 5 Days - Ignore rule"
7. Verify visual indicator shows rule is inactive (e.g., grayed out, "Inactive" label)
8. Edit rule and change action to "Allow with warning"
9. Save and verify rule becomes active with updated summary

**Expected Results:**
- ✓ Rule saves with "Ignore rule" action
- ✓ UI clearly indicates rule is inactive (visual styling, status label)
- ✓ Rule remains editable and can be reactivated
- ✓ Summary text updates accurately on action change
- ✓ No enforcement logic is triggered for inactive rules (to be verified in integration tests later)

---

## 🔹 TC-MLT-SETTINGS-006: Verify Field Validation for Rule Name — Required, Character Limits, and Special Character Handling

**Objective:** Ensure Rule Name field enforces required validation, character limits, and safely handles special input.

**Pre-conditions:**
1. Add Rule modal is open

**Test Steps:**
1. **Empty Name:** Leave name empty, set Minimum=7, click Done → verify error
2. **Whitespace Only:** Enter "   ", set Minimum=7, click Done → verify trimmed/invalid
3. **Valid Special Characters:** Enter "Rule@2026#Test!", set Minimum=7 → verify saved correctly
4. **Unicode Support:** Enter "Booking Rule 提前", set Minimum=7 → verify saved and displayed
5. **Long Name (256+ chars):** Enter 300-character name → verify truncation or error
6. **Duplicate Name:** Create rule "DuplicateTest", then try to create another with same name → verify handling
7. **SQL/HTML Injection Attempts:** Enter `'; DROP TABLE--` or `<script>alert(1)</script>` → verify sanitized

**Expected Results:**
- ✓ Empty/whitespace name shows error: "Rule name is required"
- ✓ Special characters and Unicode are accepted and stored safely
- ✓ Long names are truncated to max length (e.g., 100 chars) or show validation error
- ✓ Duplicate names: system either allows with warning OR blocks with message
- ✓ Potentially dangerous input is sanitized; saved as plain text
- ✓ Error messages are user-friendly, not technical

---

## 🔹 TC-MLT-SETTINGS-007: Verify Minimum Field Validation — Numeric Only, Boundary Values, and Edge Cases

**Objective:** Ensure Minimum field accepts only valid positive integers and handles edge cases gracefully.

**Pre-conditions:**
1. Add Rule modal is open

**Test Steps:**
1. **Zero Value:** Enter 0 → verify if allowed or shows warning
2. **Negative Value:** Try -5 → verify field rejects or auto-corrects to 1
3. **Decimal Value:** Try 7.5 → verify field accepts only integers
4. **Non-Numeric Input:** Try "seven" or "abc" → verify rejection
5. **Very Large Value:** Enter 9999 → verify accepted or shows impractical warning
6. **Empty Field:** Leave blank → verify "Done" stays disabled or shows error
7. **Boundary: 1 Day:** Enter 1 → verify accepted
8. **Boundary: 365 Days:** Enter 365 → verify accepted

**Expected Results:**
- ✓ Field accepts only positive integers (1–365 or configurable max)
- ✓ Spinner controls increment/decrement correctly
- ✓ Invalid input is rejected or auto-corrected with user feedback
- ✓ "Done" button remains disabled until valid minimum is entered
- ✓ Extremely large values may trigger advisory warning (e.g., "Are you sure?")

---

## 🔹 TC-MLT-SETTINGS-008: Verify Multiple Rules Can Be Created and Listed with Accurate Counters and Sorting

**Objective:** Confirm system supports multiple rules, displays them correctly, and maintains accurate counts.

**Pre-conditions:**
1. User can create rules
2. Start with 0 rules

**Test Steps:**
1. Create Rule 1: "Rule A - 3 Days"
2. Verify counter shows "1 rule"
3. Create Rule 2: "Rule B - 7 Days"
4. Verify counter shows "2 rules"
5. Create Rule 3: "Rule C - 14 Days"
6. Verify all three appear in list
7. Verify rules are sorted (by creation date, name, or priority)
8. Delete Rule 2
9. Verify counter updates to "2 rules" and list refreshes
10. Reorder rules via drag-and-drop (if supported) → verify persistence

**Expected Results:**
- ✓ Multiple rules can coexist
- ✓ Counter updates accurately after create/delete
- ✓ Rules display in consistent, logical order
- ✓ Delete action removes rule and updates UI instantly
- ✓ (If supported) Drag-and-drop reordering persists after refresh

---

## 🔹 TC-MLT-SETTINGS-009: Verify Edit Functionality — Pre-fill Values, Update, and Save Correctly

**Objective:** Confirm existing rules can be edited and changes persist accurately.

**Pre-conditions:**
1. Existing rule: "Original Rule - 10 Days" with "Allow with warning"

**Test Steps:**
1. Locate rule in list and click to edit
2. Verify modal opens with all values pre-filled
3. Change Rule Name to: "Updated Rule - 15 Days"
4. Change Minimum from 10 to: 15
5. Change action to: "Block submission"
6. Click "Done"
7. Verify list shows updated name, minimum, and action
8. Verify summary reflects new configuration
9. Re-open edit modal and confirm values persist
10. Change back to original values and save

**Expected Results:**
- ✓ Edit modal pre-fills current values accurately
- ✓ All fields are editable
- ✓ Changes save and reflect immediately in UI
- ✓ No data loss or corruption during edit
- ✓ Audit trail (if available) logs modification

---

## 🔹 TC-MLT-SETTINGS-010: Verify Delete Functionality — Confirmation Dialog and Permanent Removal

**Objective:** Confirm rules can be deleted safely with user confirmation and UI updates correctly.

**Pre-conditions:**
1. Existing rule to delete

**Test Steps:**
1. Locate rule and click delete icon/menu
2. Verify confirmation dialog appears with rule name
3. Read dialog message
4. Click "Cancel" → verify rule remains
5. Click delete again → click "Confirm"
6. Verify rule disappears from list
7. Verify counter decrements
8. Refresh page → verify rule is permanently gone
9. Attempt to recreate rule with same name → verify allowed

**Expected Results:**
- ✓ Delete option is accessible and intuitive
- ✓ Confirmation dialog prevents accidental deletion
- ✓ Cancel action preserves rule
- ✓ Confirm action permanently removes rule
- ✓ UI updates instantly; counter adjusts
- ✓ Deletion persists across page refreshes
- ✓ No orphaned references or console errors

---

## 🔹 TC-MLT-SETTINGS-011: Verify Toggle Active/Inactive State for Rules (If Supported)

**Objective:** Confirm rules can be temporarily disabled without deletion.

**Pre-conditions:**
1. Existing active rule

**Test Steps:**
1. Locate rule and find active/inactive toggle (or "Ignore rule" action)
2. Deactivate rule
3. Verify visual indicator shows "Inactive"
4. Verify rule remains in list but is grayed out
5. Reactivate rule
6. Verify visual indicator returns to "Active"
7. Verify configuration values are preserved throughout

**Expected Results:**
- ✓ Toggle or action allows deactivation
- ✓ Inactive rules are visually distinct
- ✓ Deactivated rules are not enforced (to be verified in integration)
- ✓ Reactivation restores original behavior
- ✓ No data loss during state change

---

## 🔹 TC-MLT-SETTINGS-012: Verify Conditional Rule Toggle Is Disabled When No Scenarios Exist (Future-Proofing)

**Objective:** Confirm the "Apply under specific condition" toggle is disabled and informative when scenarios are unavailable.

**Pre-conditions:**
1. Add Rule modal is open
2. No scenarios/datasets configured in system

**Test Steps:**
1. Locate toggle: "Apply this rule only under a specific condition"
2. Verify toggle is disabled (grayed out)
3. Hover over info icon next to toggle
4. Verify tooltip explains: "Scenarios must be configured first"
5. Attempt to click toggle → verify no action
6. Verify rule can still be created without conditions
7. Document behavior for future testing when scenarios are added

**Expected Results:**
- ✓ Toggle is present but disabled
- ✓ Tooltip provides clear guidance
- ✓ Toggle cannot be enabled prematurely
- ✓ Global (unconditional) rules can still be created
- ✓ System handles missing dependencies gracefully

---

## 🔹 TC-MLT-SETTINGS-013: Verify UI Responsiveness and Accessibility Across Viewports and Assistive Technologies

**Objective:** Ensure the settings page is usable on different screen sizes and accessible to all users.

**Pre-conditions:**
1. Settings Transaction page is loaded

**Test Steps:**
1. Resize browser to tablet (768px) and mobile (375px) widths
2. Verify layout adapts without horizontal scroll or broken elements
3. Verify buttons and inputs remain tappable on touch devices
4. Use keyboard Tab key to navigate all interactive elements
5. Verify focus indicators are visible
6. Use screen reader (e.g., NVDA, VoiceOver) to read page content
7. Verify ARIA labels, headings, and form labels are announced correctly
8. Verify color contrast meets WCAG AA standards
9. Verify error messages are announced by screen readers

**Expected Results:**
- ✓ Page is fully responsive on desktop, tablet, and mobile
- ✓ All interactive elements are keyboard-accessible
- ✓ Focus states are clear and visible
- ✓ Screen readers announce content logically
- ✓ Color contrast is sufficient for readability
- ✓ Error messages are accessible to assistive tech

---

## 🔹 TC-MLT-SETTINGS-014: Verify Error Handling and User Feedback for Network Failures or Backend Errors

**Objective:** Confirm the UI handles failures gracefully and provides actionable feedback.

**Pre-conditions:**
1. Browser DevTools open to simulate network conditions

**Test Steps:**
1. Create a valid rule but simulate network timeout before clicking Done
2. Click Done and observe behavior
3. Verify loading state appears during save attempt
4. Verify error message displays if save fails: "Could not save rule. Please try again."
5. Verify rule is NOT added to list after failure
6. Retry save after restoring network → verify success
7. Simulate backend validation error (e.g., duplicate name) → verify user-friendly message
8. Verify no duplicate rules created on retry

**Expected Results:**
- ✓ Loading indicator shows during save operation
- ✓ Network failures show clear, retryable error message
- ✓ Failed saves do not partially update UI
- ✓ Backend validation errors are translated to user-friendly messages
- ✓ Retry logic works without side effects
- ✓ No console errors or unhandled exceptions

---

## 🔹 TC-MLT-SETTINGS-015: Verify Data Persistence Across Page Refresh, Logout, and Browser Restart

**Objective:** Confirm created rules are stored in backend and survive session changes.

**Pre-conditions:**
1. Rule "Persistence Test - 20 Days" has been created

**Test Steps:**
1. Note rule details (name, minimum, action)
2. Refresh page (F5)
3. Verify rule still exists with correct values
4. Navigate to different module, then return
5. Verify rule persists
6. Logout and login again
7. Navigate back to Settings Transaction page
8. Verify rule is still present
9. Close browser, reopen, login, navigate to page
10. Verify rule persists across sessions

**Expected Results:**
- ✓ Rules persist after page refresh
- ✓ Rules persist after navigation
- ✓ Rules persist after logout/login
- ✓ Rules persist after browser restart
- ✓ All configuration values remain intact
- ✓ Data is stored server-side, not just in session storage

---

# 📋 Test Execution Checklist

| Priority | Test Case ID | Description | Status |
|----------|--------------|-------------|--------|
| 🔴 Critical | TC-MLT-SETTINGS-003 | Create rule with valid inputs | ⬜ |
| 🔴 Critical | TC-MLT-SETTINGS-004 | Create rule with Block action | ⬜ |
| 🔴 Critical | TC-MLT-SETTINGS-009 | Edit existing rule | ⬜ |
| 🟠 High | TC-MLT-SETTINGS-001 | Page loads correctly | ⬜ |
| 🟠 High | TC-MLT-SETTINGS-002 | Modal fields and validation | ⬜ |
| 🟠 High | TC-MLT-SETTINGS-006 | Rule name validation | ⬜ |
| 🟠 High | TC-MLT-SETTINGS-007 | Minimum field validation | ⬜ |
| 🟡 Medium | TC-MLT-SETTINGS-005 | Ignore rule action | ⬜ |
| 🟡 Medium | TC-MLT-SETTINGS-008 | Multiple rules management | ⬜ |
| 🟡 Medium | TC-MLT-SETTINGS-010 | Delete functionality | ⬜ |
| 🟢 Low | TC-MLT-SETTINGS-011 | Active/inactive toggle | ⬜ |
| 🟢 Low | TC-MLT-SETTINGS-012 | Conditional toggle disabled | ⬜ |
| 🟢 Low | TC-MLT-SETTINGS-013 | Responsiveness & accessibility | ⬜ |
| 🟢 Low | TC-MLT-SETTINGS-014 | Error handling | ⬜ |
| 🟢 Low | TC-MLT-SETTINGS-015 | Data persistence | ⬜ |

---

### ✅ Next Steps for You:
1. **Pick one test case** (e.g., TC-MLT-SETTINGS-003) and execute it in staging
2. **Document actual results** vs expected
3. **Log any bugs** with steps, screenshots, and console logs
4. **Reply with**:  
   - Which test case you ran  
   - What passed/failed  
   - Any questions or confusion  

Then we'll move to the next test case together — one step at a time, just like you wanted. 🙌

You've got this, Yamini! Clear, structured, and thorough — exactly how great QA work is done. 💪✨
