# Project Context — Gym Class Reservation SDET Assessment

## Project Purpose

This is an individual SDET (Software Development Engineer in Test) assessment project for the Gym Class Reservation web application. The assessment focuses on requirements analysis and test design without implementation.

## SDET Assessment Scope

The SDET task is to:

1. Analyze the application requirements for areas suitable for automated testing.
2. Identify behavior types to test (positive, negative, boundary, calculation, state-transition).
3. Select a small meaningful automation subset proportional to the application scope.
4. Create a detailed automated-test design specification for the selected subset.

**Out of scope:** Implementation, execution, or demonstration of automated tests; inventing technical details not present in requirements.

## Sources of Truth

**BRD.md** is the authoritative business source. All other project documents are derived from or validated against the BRD.

**REQUIREMENTS.md** is the verified working representation of BRD.md requirements. It has been reviewed for:
- Faithful preservation of all required business behavior, numeric values, and scope boundaries.
- Correct requirement modality (should vs. must).
- Clear distinction between requirements and technical unknowns.

If REQUIREMENTS.md conflicts with BRD.md, the BRD remains authoritative unless a human-approved requirement change exists.

## Project Artifacts

### BRD.md
Business Requirements Document. Authoritative source defining:
- Application purpose and intended user
- Available classes and pricing
- Session data and capacity
- Reservation flow and behavior
- Validation and scope boundaries

### REQUIREMENTS.md
Working requirements representation. Derived from BRD.md with:
- Organized sections for functional areas and behavior types
- Preserved business values (all prices, sessions, availability, limits)
- Clear requirement strength language (should vs. must)
- Identification of ambiguities rather than silent resolution
- Human-verified alignment to the BRD

### TEST_ANALYSIS.md
SDET requirements analysis. Identifies:
- Functional areas suitable for automation (9 areas analyzed)
- Behavior dimensions (positive, negative, boundary, calculation, state-transition)
- Five automation scenarios selected for the focused subset
- Valid requirements intentionally left outside the initial subset (with rationale)
- Technical unknowns that cannot be derived from requirements (18 identified)

### AUTOMATION_DESIGN.md
Automated-test design specification. Provides detailed design for each of the five selected scenarios:
- Test objective and requirements coverage
- Concrete test data sourced from REQUIREMENTS.md
- Preconditions and test flow
- Expected results and assertions
- State considerations and technical unknowns

Also includes:
- Proposed automation architecture and project structure
- Selector/locator strategy at conceptual level (no specific selectors prescribed)
- Synchronization and wait approach
- Test independence and browser session state handling
- Information that must be confirmed before implementation

## Selected Automation Subset

Five scenarios were selected for automation design:

1. **Scenario A: Happy Path** — Complete valid reservation end-to-end
2. **Scenario B: Participant Count Boundaries** — Validate minimum, invalid, and excess counts
3. **Scenario C: Full Session Prevention** — Verify full sessions cannot be reserved
4. **Scenario D: Price Calculation** — Verify price formula across all classes
5. **Scenario E: Availability Persistence** — Verify availability decreases and persists across reservations

See TEST_ANALYSIS.md for rationale and coverage analysis.

## Working Boundaries & Constraints

### Requirements & Behavior
- Do not invent requirements or behavior.
- If behavior is unclear or undefined, identify it as an ambiguity or technical unknown rather than assuming it.
- Distinguish required behavior from technical assumptions.
- Preserve all numeric values, scope boundaries, and requirement strength from the BRD.

### Technical Details
- Do not invent application URLs, selectors, DOM structure, control types, exact message text, or implementation details.
- Where the BRD defines an outcome but not the UI mechanism, describe the business outcome rather than assuming how the UI implements it.
- Keep technical assumptions visible and clearly documented.
- Document unknowns that cannot be resolved without seeing the application implementation.

### Scope & Proportionality
- Keep all work proportional to this small, focused application.
- Prefer small meaningful subsets over exhaustive coverage.
- Avoid over-engineering for a simple reservation form and business logic.

### Documentation
- Do not implement or execute automated tests.
- Do not claim tests were executed or demonstrate test runs.
- Do not modify BRD.md or other completed project artifacts.
- Keep CLAUDE.md focused on stable project guidance, not temporary task instructions.

## Proposed Automation Technology Stack

For automated-test design and future implementation, assume the following familiar stack unless there is a reason to propose otherwise:

- **Language:** Java
- **Web Automation:** Selenium WebDriver
- **Test Scenarios:** Cucumber (Gherkin syntax)
- **Test Runner:** TestNG
- **Build & Dependency Management:** Maven

This is a proposed design technology only. Implementation decisions may differ. The stack was chosen for familiarity and industry prevalence, not because it is prescribed by the BRD.

## Handling Unknown Implementation Details

The BRD does not prescribe technical implementation. When implementation details are unknown:

1. **Document the unknown.** Record exactly what information is needed (e.g., "How is participant count input?").
2. **Do not assume.** Do not invent a UI mechanism, control type, or behavior.
3. **Describe the business outcome.** Focus on the requirement (e.g., "Participant count below 1 is invalid") rather than the UI mechanism.
4. **Keep unknowns visible.** Maintain a clear list in the design document (see AUTOMATION_DESIGN.md "Information That Must Be Confirmed Before Implementation").

Unknown details that must be confirmed before implementation include:
- Application URL and deployment environment
- HTML structure, element IDs, and CSS class naming
- Control types (dropdown, spinner, text field, buttons, etc.)
- Exact text of validation messages, confirmation messages, or feedback
- Navigation and state-management approach
- Browser session state persistence mechanism

## Verification & Acceptance Criteria

Claude Code output must be reviewed against BRD.md and REQUIREMENTS.md before acceptance.

**A document is not accepted merely because Claude Code says it is complete.**

Before accepting any artifact:

1. **Faithfulness:** Verify that all required business behavior, numeric values, and scope boundaries are preserved exactly from the BRD/REQUIREMENTS.md.
2. **Traceability:** For TEST_ANALYSIS.md and AUTOMATION_DESIGN.md, verify that selected scenarios map to actual requirements and that rationale is clear.
3. **Technical Honesty:** Verify that unknowns are clearly labeled, assumptions are explicit, and no unsupported behavior has been invented.
4. **Proportionality:** Verify that analysis and design remain proportional to this small application.
5. **No Invention:** Verify that no application URLs, selectors, DOM structure, exact message text, or implementation details are prescribed.