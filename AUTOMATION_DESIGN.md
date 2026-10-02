# Automated-Test Design Specification — Gym Class Reservation

## Overview

This document provides a detailed test design specification for the five automation scenarios selected in TEST_ANALYSIS.md. The design focuses on business behavior and outcomes without prescribing implementation details such as URLs, selectors, control types, or message text.

The proposed automation stack is Java, Selenium WebDriver, Cucumber, TestNG, and Maven. This is a technical design choice and is not prescribed by the BRD; implementation decisions may differ.

All test designs are grounded in REQUIREMENTS.md and BRD.md.

---

## Proposed Automation Architecture

### High-Level Structure

If implementation were to proceed, the following architecture is recommended for proportionality and maintainability:

- **Test Layer:** Cucumber feature files defining scenarios in Gherkin syntax (Given/When/Then).
- **Step Definition Layer:** Java step definitions mapping Gherkin steps to test code.
- **Page Object Layer:** Minimal page objects or interaction classes to encapsulate application access patterns. Given the small application scope, a single or few lightweight page objects are expected.
- **Test Data Layer:** Test data (class names, prices, session times, initial availability) sourced from REQUIREMENTS.md; no hard-coded values that differ from the requirements.
- **Assertion/Verification Layer:** Assertion helpers to verify business outcomes (reservation confirmed, availability updated, price calculated correctly).
- **WebDriver Management:** TestNG listener or Cucumber hooks to manage WebDriver lifecycle (initialization, teardown).

### Project Structure (Maven)

```
src/test/java/
  com/gym/reservation/
    steps/              # Step definition classes
    pages/              # Page object classes (if needed)
    utils/              # Assertion helpers, test data
    config/             # Configuration, WebDriver setup

src/test/resources/
  features/             # Cucumber feature files
  application.properties # Base URL and configuration (to be determined)
```

---

## Scenario Designs

### Scenario A: Happy Path — Complete a Valid Reservation

#### Objective
Verify that a user can successfully complete an end-to-end reservation flow: select a class, select a session with available places, specify a valid participant count, review the reservation, and confirm it.

#### Requirements Coverage
- Class Selection (REQUIREMENTS.md: "Class Selection" section)
- Session Selection (REQUIREMENTS.md: "Session Selection" section)
- Number of Participants (REQUIREMENTS.md: "Number of Participants" section)
- Price Calculation (REQUIREMENTS.md: "Price Calculation" section)
- Reservation Summary (REQUIREMENTS.md: "Reservation Summary" section)
- Reservation Confirmation (REQUIREMENTS.md: "Reservation Confirmation" section)

#### Concrete Test Data
- **Class:** Yoga
- **Session:** Monday 18:00 (initial availability: 10 places)
- **Participant Count:** 3
- **Expected Price:** 3 × €8 = €24

#### Preconditions
- Application is loaded and displays the initial class selection interface.
- No prior reservations in the current browser session.
- All session data is at initial availability as defined in REQUIREMENTS.md.

#### Test Flow

1. **Select Class:** User selects Yoga from available classes.
2. **Verify Sessions Display:** Application displays three Yoga sessions with availability information.
3. **Select Session:** User selects Monday 18:00 (10 places available).
4. **Specify Participant Count:** User specifies 3 participants.
5. **Verify Summary:** Application displays reservation summary showing:
   - Class: Yoga
   - Session: Monday 18:00
   - Participants: 3
   - Price per participant: €8
   - Total price: €24
6. **Confirm Reservation:** User confirms the reservation.
7. **Verify Confirmation:** Application displays confirmation indicating the reservation was successful.

#### Expected Results / Main Assertions

- Yoga sessions are displayed when Yoga is selected.
- Monday 18:00 is available for selection and indicates that 10 places remain.
- Summary displays correct class, session, participant count, unit price, and total price (€24).
- Confirmation is shown after explicit confirmation action.
- No errors or validation failures occur during the flow.

#### State Considerations

- Availability of Yoga Monday 18:00 should decrease from 10 to 7 after confirmation.
- This change must persist for any subsequent reservations in the same browser session.

#### Technical Assumptions / Unknowns

- How classes are presented (dropdown, buttons, radio buttons, list).
- How sessions are displayed (dropdown, table, list, buttons).
- How participant count is input (text field, spinner, dropdown).
- How availability is displayed (text, number, formatted string).
- What indicates successful confirmation (page redirect, overlay, text change, button state).
- What URL or route the application uses.

---

### Scenario B: Validate Participant Count Boundaries

#### Objective
Verify that the application prevents confirmation of reservations with invalid participant counts: below minimum (1) and exceeding remaining capacity.

#### Requirements Coverage
- Number of Participants (REQUIREMENTS.md: "Number of Participants" section)
- Validation & Invalid Reservations (REQUIREMENTS.md: "Validation & Invalid/Incomplete Reservations" section)

#### Concrete Test Data

**Variation 1: Minimum Valid Participant Count**
- Class: Spinning
- Session: Thursday 18:00 (initial availability: 1 place)
- Participant Count: 1
- Expected: Reservation should confirm successfully.

**Variation 2: Participant Count Below Minimum**
- Class: Yoga
- Session: Saturday 10:00 (initial availability: 2 places)
- Participant Count: 0 or negative value
- Expected: Reservation should NOT confirm.

**Variation 3: Participant Count Exceeding Remaining Capacity**
- Class: Pilates
- Session: Thursday 19:00 (initial availability: 4 places)
- Participant Count: 5 (exceeds 4 remaining)
- Expected: Reservation should NOT confirm.

#### Preconditions
- Application is loaded.
- No prior reservations in the current browser session.
- All session data is at initial availability.

#### Test Flow (Variation 1)

1. Select Spinning.
2. Verify Thursday 18:00 is available (shows 1 place).
3. Specify 1 participant.
4. Verify summary shows correct data.
5. Confirm reservation.
6. Verify confirmation is displayed.

#### Test Flow (Variation 2)

1. Select Yoga.
2. Select Saturday 10:00 (2 places).
3. Attempt to specify 0 or negative participant count.
4. Verify that the application prevents confirmation (business outcome: reservation cannot be confirmed).
5. Verify user receives feedback about the invalid state.

#### Test Flow (Variation 3)

1. Select Pilates.
2. Select Thursday 19:00 (4 places).
3. Attempt to specify 5 participants.
4. Verify that the application prevents confirmation (business outcome: reservation cannot be confirmed).
5. Verify user receives feedback about the invalid state.

#### Expected Results / Main Assertions

**Variation 1:**
- Confirmation succeeds; reservation is successfully confirmed.
- Availability of Thursday 18:00 decreases from 1 to 0.

**Variation 2:**
- Confirmation fails or is prevented.
- Assertion: Participant count below 1 does not result in a confirmed reservation.
- User receives feedback (exact text/presentation not prescribed by requirements).

**Variation 3:**
- Confirmation fails or is prevented.
- Assertion: Participant count exceeding remaining capacity does not result in a confirmed reservation.
- User receives feedback (exact text/presentation not prescribed by requirements).

#### State Considerations

- Variation 1 results in a confirmed reservation and decreases availability.
- Variations 2 and 3 do NOT confirm and do NOT affect availability.
- Availability changes resulting from confirmed reservations persist within the current browser session.

#### Technical Assumptions / Unknowns

- How participant count is input; whether the UI validates on input, on submission, or both.
- Whether 0 or negative values can be entered in the UI, or if the UI prevents them before testing can occur.
- Exact wording and presentation of validation feedback.
- What element receives focus or is highlighted when validation fails.

---

### Scenario C: Prevent Reservation from Full Session

#### Objective
Verify that a session with zero remaining places cannot be successfully reserved.

#### Requirements Coverage
- Session Selection (REQUIREMENTS.md: "Session Selection" section — "A full session cannot be reserved.")
- Validation & Invalid Reservations (REQUIREMENTS.md: "Validation & Invalid/Incomplete Reservations" section)

#### Concrete Test Data
- **Class:** Pilates
- **Session:** Saturday 11:30 (initial availability: 0 places)
- **Participant Count:** 1 (or any valid count; the session is full)
- **Expected:** Reservation cannot be confirmed.

#### Preconditions
- Application is loaded.
- No prior reservations in the current browser session.
- Pilates Saturday 11:30 is available in the session list.

#### Test Flow

1. Select Pilates.
2. Verify that Saturday 11:30 is listed and shows 0 places available.
3. Attempt to select Saturday 11:30.
   - If the UI prevents selection, verify the session cannot be selected (business outcome: full session cannot be reserved).
   - If the UI allows selection, proceed to step 4.
4. If Saturday 11:30 is selected, specify 1 participant.
5. Attempt to confirm the reservation.
6. Verify that the reservation does NOT confirm.

#### Expected Results / Main Assertions

- Saturday 11:30 indicates that 0 places remain.
- Assertion: No successful reservation can be completed for Saturday 11:30.
- Assertion: Attempting to reserve from this session results in a blocked confirmation or prevented action.
- Availability of the session remains 0 after the attempted reservation.

#### State Considerations

- Failing to reserve from a full session must not affect any state.
- No availability change should occur.

#### Technical Assumptions / Unknowns

- Whether a full session can be selected in the UI (then blocked at confirmation) or is disabled/hidden from selection.
- Exact user-facing indication that the session is full or cannot be selected.
- Exact error or feedback message if selection/confirmation is attempted.

---

### Scenario D: Verify Price Calculation Across Classes

#### Objective
Verify that the total reservation price is calculated correctly (participants × price per class) for all four classes.

#### Requirements Coverage
- Price Calculation (REQUIREMENTS.md: "Price Calculation" section — "number of participants × price per participant")

#### Concrete Test Data

**Variation 1: Yoga**
- Class: Yoga
- Price per participant: €8
- Session: Monday 18:00 (10 places)
- Participant Count: 1
- Expected Total: €8

**Variation 2: Pilates**
- Class: Pilates
- Price per participant: €10
- Session: Tuesday 18:00 (8 places)
- Participant Count: 4
- Expected Total: €40

**Variation 3: Functional Training**
- Class: Functional Training
- Price per participant: €12
- Session: Monday 19:30 (5 places)
- Participant Count: 5
- Expected Total: €60

**Variation 4: Spinning**
- Class: Spinning
- Price per participant: €11
- Session: Tuesday 19:30 (7 places)
- Participant Count: 7
- Expected Total: €77

#### Preconditions
- Application is loaded.
- No prior reservations in the current browser session.
- All session data is at initial availability.

#### Test Flow (Common to All Variations)

1. Select the class.
2. Verify the class is selected and its sessions are displayed.
3. Select a session with sufficient remaining places.
4. Specify the participant count.
5. Verify the summary displays the correct total price.

#### Expected Results / Main Assertions

- **Variation 1:** Summary displays total price €8.
- **Variation 2:** Summary displays total price €40.
- **Variation 3:** Summary displays total price €60.
- **Variation 4:** Summary displays total price €77.
- **General Assertion:** Total price = Participant Count × Price per Participant for all classes.

#### State Considerations

- No reservation is confirmed in this scenario; availability is not affected.
- Each variation should be independent and repeatable.

#### Technical Assumptions / Unknowns

- Where in the summary the total price is displayed.
- What currency symbol or format is used (€ is specified in REQUIREMENTS.md; exact formatting is not).
- Whether decimal values or rounding rules apply (test data are whole numbers, so this may not be determinable from requirements).
- Whether price displays update live as participant count changes or only in the summary.

---

### Scenario E: Availability Decreases After Confirmation & Persists

#### Objective
Verify that reserved places are deducted from session availability and changes persist across multiple reservations in the same browser session.

#### Requirements Coverage
- Availability Management (REQUIREMENTS.md: "Availability After Confirmation" section)
- Multiple Sequential Reservations (REQUIREMENTS.md: "Starting Another Reservation" section)

#### Concrete Test Data

**First Reservation:**
- Class: Yoga
- Session: Wednesday 19:00 (initial availability: 6 places)
- Participant Count: 2
- Expected Availability After: 4 places

**Second Reservation (same session):**
- Class: Yoga
- Session: Wednesday 19:00 (expected availability before: 4 places)
- Participant Count: 2
- Expected Availability After: 2 places

#### Preconditions
- Application is loaded.
- No prior reservations in the current browser session.
- Yoga Wednesday 19:00 has initial availability of 6 places.

#### Test Flow

1. **First Reservation:**
   - Select Yoga.
   - Select Wednesday 19:00; verify it shows 6 places available.
   - Specify 2 participants.
   - Verify summary shows total price €16 (2 × €8).
   - Confirm reservation.
   - Verify confirmation is displayed.

2. **Start Another Reservation:**
   - From confirmation, return to a state where another reservation can be created.
   - Select Yoga again.
   - Select Wednesday 19:00 again.
   - **Verify Assertion:** Wednesday 19:00 now shows 4 places available (decreased from 6 by 2).

3. **Second Reservation (same session):**
   - Specify 2 participants.
   - Verify summary shows total price €16.
   - Confirm reservation.
   - Verify confirmation is displayed.

4. **Verify Persistence:**
   - Return to a state where another reservation can be created.
   - Select Yoga.
   - Select Wednesday 19:00.
   - **Verify Assertion:** Wednesday 19:00 now shows 2 places available (decreased from 4 by 2).

#### Expected Results / Main Assertions

- After first confirmation: Wednesday 19:00 availability changes from 6 to 4.
- After returning and re-selecting the same session: availability displays as 4 (persisted).
- After second confirmation: Wednesday 19:00 availability changes from 4 to 2.
- After returning again: availability displays as 2 (persisted).

#### State Considerations

- This scenario is **sequential and stateful.** The second reservation depends on the state left by the first.
- Browser session state must persist between confirmations.
- This scenario requires careful test isolation or must be run as a single test with multiple phases.

#### Technical Assumptions / Unknowns

- Where and how availability is displayed (text, number, formatted string).
- Whether availability updates immediately after confirmation or requires a UI action/navigation.

---

## Cross-Cutting Design Considerations

### Selector & Locator Strategy

Given that the BRD and REQUIREMENTS.md do not prescribe selectors or DOM structure, the following strategy is recommended:

- **Identify Elements by Business Function:** Rather than relying on element IDs or CSS classes, identify elements by their role and context (e.g., "class selection area," "session list," "participant count input," "confirm button").
- **Use Accessible Locators:** Prefer selectors based on accessible properties (label text, aria-label, role) when possible, as these are less fragile to UI changes.
- **Page Object Abstraction:** Encapsulate element locators in page objects or interaction helpers to isolate test code from UI details.
- **Locator Documentation:** Document locators with comments explaining the business context (e.g., "button to confirm reservation") rather than the technical selector.

**Note:** Specific selectors cannot be provided until the application implementation is known.

### Synchronization & Wait Strategy

- **Explicit Waits:** Use Selenium WebDriver's explicit wait mechanism (WebDriverWait with expected conditions) rather than hard sleeps.
- **Wait Conditions:**
  - For dropdown or list display: Wait for elements representing sessions to be visible when a class is selected.
  - For summary display: Wait for price/summary elements to be visible after participant count is specified.
  - For confirmation: Wait for a confirmation indicator to appear after the confirm action.
- **Polling Timeout:** The timeout value should be configurable and determined from observed application behavior during testing.
- **State Persistence (Scenario E):** After confirmation, wait for the confirmation UI to appear before returning to a state where another reservation can be created. Verify availability updates are visible before proceeding.

### Test Independence & Browser Session State

- **Test Isolation:** Each test scenario should ideally run independently. However, Scenario E is intentionally sequential.
- **Browser Session Persistence (Scenario E Only):** Scenario E requires a single browser session across two reservations. All other scenarios should use a fresh browser session.
- **Approach:**
  - Create a new WebDriver instance before each test (TestNG @BeforeMethod or Cucumber hooks).
  - For Scenario E, use a dedicated test method or hook that maintains the same WebDriver instance across multiple reservation phases (first reservation, verify persistence, second reservation, verify persistence again).
  - Document this clearly in test code.
- **Initial State Assumption:** All tests assume the application loads with initial availability values. No test should assume state left by a prior test.

### Sequential State Handling (Scenario E)

Scenario E is unique because it requires sequential state changes within a single browser session.

- **Test Structure:** Implement as a single test method with multiple phases (first reservation, verify persistence, second reservation, verify persistence again).
- **Logging:** Log each phase's completion and key assertions to make test progression clear.
- **Separation of Concerns:** Separate "make a reservation" into a reusable helper method invoked for both the first and second reservation.
- **Failure Handling:** If any phase fails, fail the entire test with clear indication of which phase failed.

### Information That Must Be Confirmed Before Implementation

The following details cannot be derived from the BRD/REQUIREMENTS and must be confirmed or decided before implementation:

1. **Application URL:** What is the base URL for the application? (Not prescribed by BRD.)
2. **Deployment/Environment:** Where is the application deployed for testing? (Development, staging, local instance?)
3. **DOM Structure & Selectors:** What HTML elements, attributes, and selectors are used for classes, sessions, participant input, confirm button, price display, confirmation message?
4. **Input Mechanisms:** How is participant count entered (text input allowing 0/negative, or a spinner that enforces minimum)? How are classes and sessions selected (dropdown, buttons, radio, etc.)?
5. **Availability Display Format:** Exact format of "6 places" or "places remaining" or other phrasing.
6. **Price Display Format:** Exact format of €8, €8.00, or other currency formatting.
7. **Confirmation Indication:** How does the application indicate successful confirmation? (Page redirect, message overlay, confirmation page, button state change?)
8. **Feedback for Invalid Cases:** How are validation failures communicated to users? (Inline error text, disabled button, toast, alert, field highlighting?)
9. **Session State Mechanism:** How is availability state persisted in the browser? (In-memory, localStorage, sessionStorage, IndexedDB?)
10. **Refresh Behavior:** Does refresh clear all state, or are some elements restored?
11. **Starting Another Reservation:** How does the user start another reservation after confirmation? (Button, link, automatic redirect, other mechanism?)

---

## Test Data Reference

All test data is sourced from REQUIREMENTS.md Section "Sessions & Initial Availability":

| Class | Session | Initial Places | Price |
|---|---|---|---|
| Yoga | Monday 18:00 | 10 | €8 |
| Yoga | Wednesday 19:00 | 6 | €8 |
| Yoga | Saturday 10:00 | 2 | €8 |
| Pilates | Tuesday 18:00 | 8 | €10 |
| Pilates | Thursday 19:00 | 4 | €10 |
| Pilates | Saturday 11:30 | 0 | €10 |
| Functional Training | Monday 19:30 | 5 | €12 |
| Functional Training | Wednesday 18:00 | 10 | €12 |
| Functional Training | Friday 18:30 | 3 | €12 |
| Spinning | Tuesday 19:30 | 7 | €11 |
| Spinning | Thursday 18:00 | 1 | €11 |
| Spinning | Sunday 10:00 | 10 | €11 |

---

## Conclusion

This design specification provides detailed test outlines for the five automation scenarios selected in TEST_ANALYSIS.md, focusing on business behavior and outcomes. The design remains technology-agnostic where possible and defers implementation decisions (selectors, messages, navigation patterns) to the application implementation phase.

All scenarios are grounded in REQUIREMENTS.md and BRD.md. No unsupported behavior has been invented.

Before implementing automated tests, the pre-implementation confirmation checklist (above) must be completed with application details.
