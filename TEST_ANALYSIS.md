# SDET Requirements Analysis — Gym Class Reservation

## Overview

This document analyzes the Gym Class Reservation application requirements to identify areas suitable for automated testing, select a focused automation subset, and explain the selection rationale. The analysis is based on REQUIREMENTS.md, which represents BRD.md requirements.

The application is small and focused, limited to a single reservation flow with no user accounts or payment processing. Database and server-side persistence are out of scope; however, temporary state persistence (availability changes) within the current browser session is required. Automation should be proportional to this scope.

---

## Functional Areas for Automation Analysis

### 1. Class Selection
**What:** User selects one of four gym classes (Yoga, Pilates, Functional Training, Spinning).  
**Why Relevant:** Foundational step; determines available sessions and price.  
**Behavior Types:**
- **Positive:** Select each of the 4 classes; sessions display correctly for selected class.
- **State:** Class selection affects available sessions; changing class updates session list.
- **Boundary:** All 4 classes must be selectable.

### 2. Session Selection
**What:** User selects one of three predefined sessions for the chosen class.  
**Why Relevant:** Determines available places and affects total reservation capacity.  
**Behavior Types:**
- **Positive:** Select any session with available places.
- **Negative:** Cannot successfully reserve from a session with 0 places available.
- **Boundary:** Sessions with 1 place, sessions with 10 places, and full sessions (0 places).
- **State:** Session selection affects participant count limits.

**Test Data Anchor:**
- Pilates Saturday 11:30 starts with 0 places (full) — cannot be reserved.
- Spinning Thursday 18:00 starts with 1 place (boundary) — can reserve 1 only.
- Functional Training Wednesday 18:00 has 10 places (full capacity) — can reserve 1–10.

### 3. Number of Participants
**What:** User specifies how many places to reserve (1 to remaining capacity).  
**Why Relevant:** Determines total price; must not exceed session capacity.  
**Behavior Types:**
- **Boundary:** Minimum 1 participant; maximum = remaining capacity.
- **Negative:** Participant count below 1 is invalid; count exceeding remaining places is invalid.
- **Calculation:** Affects total price directly.

### 4. Price Calculation
**What:** Total price = number of participants × price per participant.  
**Why Relevant:** Core calculation; directly verifiable.  
**Behavior Types:**
- **Positive:** Correct calculation for each class.
- **Boundary:** Single participant (minimum); maximum participants for given session.
- **Calculation:** Four different prices (€8, €10, €11, €12).

**Test Data Anchor:**
- Yoga €8: 1 participant = €8; 6 participants = €48.
- Pilates €10: 1 participant = €10; 4 participants = €40.
- Functional Training €12: 5 participants = €60.
- Spinning €11: 7 participants = €77.

### 5. Reservation Summary
**What:** User reviews selected class, session, participants, price before confirming.  
**Why Relevant:** Verifies user understands the reservation; allows changes.  
**Behavior Types:**
- **State:** Summary reflects current selections.
- **State-Transition:** Changes to class/session/participants update summary.

### 6. Reservation Confirmation
**What:** User explicitly confirms reservation; application indicates success.  
**Why Relevant:** Confirmation is a required action; prevents accidental reservations.  
**Behavior Types:**
- **Positive:** Valid reservation confirms successfully.
- **Negative:** Invalid or incomplete reservation cannot be confirmed.
- **State-Transition:** Confirmation moves reservation into completed state.

### 7. Validation & Invalid Reservations
**What:** Application prevents confirmation when:
- No class selected.
- No session selected.
- Participant count < 1 or exceeds remaining places.
- Selected session has 0 places.  
**Why Relevant:** Boundary protection; prevents data consistency issues.  
**Behavior Types:**
- **Negative:** Each invalid condition blocks confirmation.
- **Boundary:** Edges where validation triggers (0 vs 1 participant; capacity vs. excess).

### 8. Availability Management
**What:** Reserved places deducted from session availability; changes persist in session; reset on refresh.  
**Why Relevant:** Core state behavior; ensures subsequent reservations see updated availability.  
**Behavior Types:**
- **State-Transition:** After confirmation, available places decrease.
- **Persistence:** Changes remain for next reservation in same session (browser session).
- **Reset:** Refresh/reopen restores initial availability.

**Test Data Anchor:**
- Yoga Monday 18:00 starts with 10 places; if user reserves 3, should show 7 remaining.
- Yoga Wednesday 19:00 starts with 6 places; if user reserves 2, should show 4 remaining.

### 9. Multiple Sequential Reservations
**What:** After confirming a reservation, user can start another; prior availability changes persist.  
**Why Relevant:** Tests session-level state persistence.  
**Behavior Types:**
- **State-Transition:** First reservation → confirmation → return to state where another reservation can be created.
- **Persistence:** Second reservation sees updated availability from first.

---

## Behavior Dimensions Summary

| Dimension | Count | Coverage in Subset |
|---|---|---|
| Positive scenarios | 7 | ✓ Core flow |
| Negative scenarios | 5+ | ✓ Selected critical cases |
| Boundary conditions | 8+ | ✓ Participant & availability edges |
| Calculation behavior | 4 price points | ✓ Core classes |
| State transitions | 6 major | ✓ Reservation flow |

---

## Selected Automation Subset

The following focused scenarios are selected for automated test design:

### Scenario A: Happy Path — Complete a Valid Reservation
**Scope:** Class selection → session selection → valid participant count → review → confirm → see confirmation.  
**Why Selected:**
- Tests the primary user flow end-to-end.
- Demonstrates class-to-session relationship, price calculation, and confirmation.
- Covers positive scenarios, calculation, and state transitions.
- Validates that the application works for its intended purpose.

**Coverage:** Positive flow, calculation (price), state transitions.

---

### Scenario B: Validate Participant Count Boundaries
**Scope:** Attempt to reserve with participant counts at boundaries: 1 (minimum), counts below 1, and counts exceeding remaining places.  
**Why Selected:**
- Boundary conditions are critical for correctness.
- Tests that invalid participant counts prevent successful reservation confirmation.
- Low complexity; high value for catching edge cases.
- Clarifies what constitutes a "valid" reservation.

**Variations:**
- Reserve 1 from a session with 1 place (minimum, boundary).
- Specify an invalid participant count below 1; verify reservation cannot be confirmed.
- Specify a participant count exceeding remaining places; verify reservation cannot be confirmed.

**Coverage:** Negative scenarios, boundary conditions, validation.

---

### Scenario C: Prevent Reservation from Full Session
**Scope:** Attempt to reserve from a session with 0 places available; verify reservation cannot be confirmed.  
**Why Selected:**
- Critical boundary: full sessions must prevent successful reservation confirmation.
- Pilates Saturday 11:30 provides a guaranteed test case (initial availability = 0).
- Single-point validation test.

**Coverage:** Boundary condition, negative scenario, validation.

---

### Scenario D: Verify Price Calculation Across Classes
**Scope:** Complete valid reservations for different classes; verify total price matches formula.  
**Why Selected:**
- Price calculation is deterministic and verifiable.
- Tests calculation logic in context of live reservation.
- Covers all four price points (€8, €10, €11, €12).
- Moderate complexity; high confidence value.

**Variations:**
- Yoga (€8) × 1 = €8.
- Pilates (€10) × 4 = €40.
- Functional Training (€12) × 5 = €60.
- Spinning (€11) × 7 = €77.

**Coverage:** Calculation behavior.

---

### Scenario E: Availability Decreases After Confirmation & Persists
**Scope:** Confirm a reservation; verify available places decrease; confirm second reservation with updated availability.  
**Why Selected:**
- Tests critical state-transition: availability deduction.
- Tests persistence of state across reservations in same session.
- Validates the core availability-management requirement.
- Covers state transitions and persistence.

**Example:** Yoga Wednesday 19:00 starts with 6 places. Reserve 2 → should show 4. Reserve 2 again → should show 2. Verify these changes persist through each confirmation.

**Coverage:** State transitions, persistence, availability management.

---

## Requirements Intentionally Left Outside the Initial Subset

The following valid requirements are excluded from the initial automated subset to keep the work proportional:

1. **UI Feedback Messages & Error Handling**  
   - Requirement: "User should receive enough feedback to understand that the reservation cannot yet be confirmed."  
   - Reason for Exclusion: Feedback content and presentation are design-dependent; not quantifiable without knowing exact text, styling, or location. Better suited for manual review.

2. **Changing Selections Before Confirmation**  
   - Requirement: "User should be able to change the selected class, session, or number of participants before confirming."  
   - Reason for Exclusion: A standard happy path exercises a sequential flow (class → session → count → confirm) without changing selections. Testing this requirement fully would require testing multiple change sequences (change class then session, change count then session, etc.), which would unnecessarily expand the scope of this bounded assessment. This behavior is intentionally excluded from the initial automation subset and is better suited for targeted manual verification or a separate automation pass.

3. **Comprehensive Session & Availability Combinations**  
   - Requirement: Each class has 3 sessions with varying initial availability.  
   - Reason for Exclusion: Automated testing of all 12 sessions is excluded from the initial subset in favor of representative coverage. Scenario D tests all four classes; Scenarios A and E exercise representative sessions. Exhaustive coverage of all 12 session combinations is intentionally deferred.

4. **Confirmation Success Indication Quality**  
   - Requirement: "Application should clearly indicate that the reservation was successful."  
   - Reason for Exclusion: What constitutes "clear" is subjective (visual, text, placement). Automated verification would require prescriptive DOM knowledge that requirements do not provide.

5. **Visual Design Quality**  
   - Requirement: "Application should provide a simple and understandable interface."  
   - Reason for Exclusion: What constitutes "simple" and "understandable" are qualitative judgments not measurable from these requirements. This aspect belongs in manual UI review. Note: The BRD does not define accessibility requirements.

6. **Payment & External Confirmations**  
   - Requirement: "No payment is made through the application. No email, SMS, printed ticket, or external confirmation is required."  
   - Reason for Exclusion: Out of scope by definition; nothing to automate.

---

## Technical Unknowns & Assumptions

The following information cannot be derived from requirements and will be necessary for test design implementation:

### DOM & Interaction
1. **Class Selection Interaction:** How are classes presented? (Dropdown, buttons, radio buttons, clickable list items?)
2. **Session Selection Interaction:** How are sessions displayed and selected? (Dropdown, buttons, table, expandable list?)
3. **Participant Count Input:** Is participant count entered as text field, spinner control, or dropdown?
4. **Confirmation Trigger:** What element triggers confirmation? (Button, form submit, link?)

### Selectors & Locators
5. **CSS/XPath Selectors:** No selectors, element IDs, or class names are prescribed.
6. **Dynamic vs. Static Content:** Will pages require waits for dynamic rendering, or is content static?
7. **Form Fields:** Are form fields accessible via standard HTML attributes (id, name, class)?

### Display & Messaging
8. **Success Indication:** How is successful confirmation indicated? (Page redirect, message overlay, text change, color change, icon?)
9. **Validation Feedback:** How are validation errors communicated? (Inline error text, toast/alert, button disabled, specific field highlight?)
10. **Availability Display Format:** How is "4 places remaining" displayed? (Text, number, progress bar, text in parentheses?)
11. **Price Display:** Where and how is total price shown? (Below input, in summary card, in confirmation page?)
12. **Date/Time Format:** How are session times displayed? (12-hour with AM/PM, 24-hour, with timezone?)

### Navigation & State
13. **Navigation After Confirmation:** Does confirmation redirect to a new page, show an overlay, or update the same page?
14. **"Start Another Reservation" Trigger:** Is there a button, link, or automatic redirect?
15. **Availability Update Timing:** Do availability changes appear immediately after confirming, or require page action?

### Application Behavior
16. **Application URL:** What is the base URL? (Not prescribed by requirements.)
17. **Session Persistence:** Does the browser session include local storage, IndexedDB, or in-memory state?
18. **Refresh Behavior:** When the user refreshes the page, how is initial state restored? (Reset to class selection, or restore last selection?)

---

## Subset Selection Rationale

**Why These Five Scenarios?**

1. **Coverage of Core Requirements:** The selected subset tests the primary reservation flow, validation, calculation, and state management — the core of the application.

2. **Deterministic & Verifiable:** Each scenario has clear inputs and expected outputs:
   - Scenario A: Completes without error; shows confirmation.
   - Scenario B: Invalid participant counts prevent successful reservation confirmation.
   - Scenario C: Full session cannot be successfully reserved/confirmed.
   - Scenario D: Price matches formula for all classes.
   - Scenario E: Availability decreases and persists.

3. **Proportional to Application Size:** Five focused scenarios are enough to demonstrate the key behaviors of a small application without being exhaustive.

4. **Uses Concrete Test Data:** Scenarios leverage specific initial availability values (e.g., Pilates Saturday 11:30 = 0 places, Yoga Wednesday 19:00 = 6 places) to anchor expectations.

5. **Minimal Dependency on UI Details:** Scenarios focus on behavior and business logic rather than specific selectors, messages, or visual design.

6. **Representative of Both Positive & Negative Cases:**
   - Scenarios A, D: Positive (valid reservations).
   - Scenarios B, C: Negative (blocked confirmations, validation of invalid conditions).
   - Scenario E: State-transition (persistence across actions).

---

## Excluded Scenarios & Rationale

**Why Not Include Every Session & Availability Combination?**

Exhaustive coverage of all 12 sessions is intentionally excluded from this initial automation subset. Instead, representative coverage is selected: Scenario D tests all four class types; Scenario E and the happy path (Scenario A) exercise representative sessions. This representative approach is sufficient for a bounded assessment of this small application.

**Why Not Include UI Message Verification?**

Requirements specify that users "should receive enough feedback" and that confirmation "should clearly indicate" success, but do not prescribe the text, location, or format. Automating message verification would require inventing DOM details. Manual review is more appropriate.

**Why Not Include Every Change-Before-Confirming Permutation?**

The requirement states that users "should be able to change the selected class, session, or number of participants before confirming." A standard happy path does not exercise change behavior; it tests a direct sequential flow (class → session → count → confirm). Comprehensive testing of this requirement would require testing multiple change sequences, which would unnecessarily expand the scope of this bounded assessment. This behavior is intentionally excluded from the initial automation subset because representative coverage is considered sufficient. Full coverage may be deferred to a later phase or addressed through targeted manual testing.

---

## Conclusion

The five-scenario subset provides a focused, representative automation design that:
- Validates the core reservation flow.
- Tests critical boundary and validation logic.
- Verifies calculation correctness.
- Ensures availability state management works as specified.

The subset is proportional to the small application, avoids inventing technical details, and leaves qualitative UI concerns (feedback messages, visual design) to manual review.

All scenarios are grounded in REQUIREMENTS.md and BRD.md; no unsupported behavior has been invented.
