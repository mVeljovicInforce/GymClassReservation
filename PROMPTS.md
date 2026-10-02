# Assessment Prompts

This file records the prompts used during the SDET assessment.

---

## Prompt 1 — Create Working Requirements

```text
Read BRD.md and CLAUDE.md.

The first task is to create the project working requirements in
REQUIREMENTS.md.

BRD.md is the authoritative business source.

Create REQUIREMENTS.md as a faithful, usable working representation of the BRD.

Requirements:

- Preserve all required business behavior from BRD.md.
- Preserve all class names, prices, sessions, initial availability values,
  participant limits, reservation-state behavior, and scope boundaries.
- Organize the document so that it is easy to use later for SDET analysis.
- Do not invent requirements.
- Do not add implementation details.
- Do not choose selectors, URLs, frameworks, DOM structure, or test design.
- Do not modify BRD.md.
- If the BRD contains an ambiguity, preserve the requirement and identify the
  ambiguity rather than silently resolving it.

After creating REQUIREMENTS.md:

1. Compare it back to BRD.md.
2. Report any information that may have been omitted, changed, or interpreted.
3. Do not modify any other assessment artifact yet.

```

## Prompt 2 — REQUIREMENTS.md Refinement

```text
Review REQUIREMENTS.md against BRD.md one more time.

The content is generally correct, but make these specific corrections:

1. Preserve requirement modality from the BRD.
   Do not strengthen BRD statements from "should" to "must".
   Where BRD.md says "should", keep that meaning in REQUIREMENTS.md.
   Where BRD.md says "must", preserve "must".

2. Remove duplicated wording where the same requirement is stated twice,
   such as the full-session / zero-availability restriction.

3. Ensure the Markdown syntax is clean and normal.
   Do not leave escaped Markdown characters such as:
   \|, \-, \*\*, or numbered-list escapes
   unless they are genuinely required.

4. Do not add, remove, or reinterpret business behavior.

After editing, compare REQUIREMENTS.md against BRD.md and report whether any
business behavior, numeric value, scope boundary, or requirement strength differs.
Do not modify BRD.md or any other file.
...

```

## Prompt 3: SDET Analysis

```text
Read:

- BRD.md
- REQUIREMENTS.md
- CLAUDE.md

REQUIREMENTS.md has been reviewed against BRD.md and may now be used as the
project working requirements representation. BRD.md remains authoritative.

Perform the SDET requirements analysis for this assessment and create
TEST_ANALYSIS.md.

The artifact should:

- identify the important functional areas considered for automation;
- consider positive, negative, boundary, calculation, and state-transition
  behavior where relevant;
- identify meaningful automation candidates;
- select a small representative subset suitable for automation;
- explain why that subset was selected;
- identify valid requirements intentionally left outside the initial automated
  subset;
- clearly identify technical unknowns that cannot be derived from the
  requirements.

Do not implement automated tests.

Do not create Java code, feature files, pom.xml, runners, step definitions,
selectors, or executable automation.

Do not invent application behavior or technical implementation details.

Keep the artifact proportional to this small application.

After creating TEST_ANALYSIS.md, summarize the selected subset and explain the
reasoning behind the selection.
...
```

## Promt 4: SDET Analysis Refinement

```text

Review TEST_ANALYSIS.md against BRD.md and REQUIREMENTS.md.

The analysis is generally good, but correct these issues:

1. Do not state that the application has "no persistence".
   The BRD requires temporary availability state persistence within the current
   browser session, while database/server persistence is out of scope.
   Make this distinction explicit.

2. Do not strengthen the full-session requirement.
   The BRD says a full session cannot be reserved.
   It does not say that a full session cannot be selected.
   Replace any wording such as "cannot select a full session" with wording that
   verifies that a successful reservation cannot be completed.

3. Do not claim that change-before-confirmation behavior is implicitly covered
   by the happy path.
   A normal happy path does not exercise changing class, session, or participant
   count before confirmation.
   If this behavior remains outside the selected automation subset, state that
   clearly and explain why.

4. Do not treat accessibility as a BRD requirement.
   The BRD requires a simple and understandable interface but does not define
   accessibility requirements.
   Remove or clearly distinguish accessibility as outside the stated requirements.

5. Remove unsupported reasoning such as:
   "If the reservation flow works for one session, it will work for others."
   Instead, explain that exhaustive coverage of all 12 sessions is intentionally
   excluded from the initial subset because representative coverage is considered
   sufficient for this bounded assessment.

6. Wherever Scenario C or its summary says a full session "cannot be selected",
   change it to "cannot be successfully reserved/confirmed".

Do not change the selected automation scope unless one of these corrections
genuinely requires it.

Do not add new requirements or implementation details.

After editing, summarize exactly what was corrected and why.
...

```
## Prompt 5: SDET Analysis Refinemnt v1.1

```text

Perform one final focused review of TEST_ANALYSIS.md.

Make only these corrections:

1. Do not state that invalid participant values such as 0 or negative numbers
   cannot be entered.
   The requirements only establish that participant count below 1 is invalid
   and that an invalid reservation cannot be confirmed.
   Use business-outcome wording rather than assuming UI input prevention.

2. Replace wording such as "validation prevents invalid inputs" with wording
   that states invalid reservations cannot be successfully confirmed.

3. Remove unsupported complexity labels such as "quadratic" or "exponential"
   when discussing change-before-confirmation permutations.
   Simply state that exhaustive permutations would unnecessarily expand the
   scope of this bounded assessment.

4. Do not claim that specific availability values are exercised by scenarios
   unless those values are explicitly defined in the selected scenario.
   Use general representative-coverage wording where appropriate.

5. Clean the Markdown formatting.
   Use normal Markdown headings, bullets, tables, and code formatting.
   Remove unnecessary escaped Markdown characters and bold wrappers around
   headings.

Do not change the selected automation subset.
Do not add new requirements or implementation details.

After editing, report only the corrections made.
...
```

## Prompt 6: SDET Analysis Refinement v1.2

```Text

Make two final corrections to TEST_ANALYSIS.md only.

1. Replace the statement that after confirmation the application resets to
   class selection.

   The BRD does not prescribe that exact UI state.

   Use requirement-faithful wording such as:
   "return to a state where another reservation can be created."

2. Clean the Markdown syntax throughout the file:
   - headings should use normal # / ## / ### syntax without bold wrappers
   - bullet lists should use normal "-" characters
   - numbered lists should use normal "1." syntax
   - tables should use normal "|" syntax
   - remove unnecessary escape characters such as \-, \|, \*, and \.

Do not change the analysis, selected automation subset, rationale, or any
business content beyond the single requirement correction above.

After editing, report only what was changed.
...
```

## Prompt 7: SDET Automation Design

```Text

Read:

- BRD.md
- REQUIREMENTS.md
- CLAUDE.md
- TEST_ANALYSIS.md

TEST_ANALYSIS.md has been human-reviewed and accepted.

Create AUTOMATION_DESIGN.md for the automation subset already selected in
TEST_ANALYSIS.md.

This is an automated-test design specification only.

Do not implement automated tests.
Do not execute tests.
Do not create Java source files, feature files, step definitions, runners,
hooks, pom.xml, or selectors.

Use the following proposed automation stack for the design:

- Java
- Selenium WebDriver
- Cucumber
- TestNG
- Maven

This stack is a technical design choice only and is not prescribed by the BRD.

For each selected automation scenario from TEST_ANALYSIS.md, include:

- scenario objective;
- requirement area(s) covered;
- concrete test data;
- preconditions or required starting state;
- proposed test flow;
- expected results / main assertions;
- relevant state considerations;
- technical assumptions or unknowns that cannot be confirmed from the
  requirements.

The design must remain behavior-focused.

Do not invent:

- application URL;
- selectors;
- DOM structure;
- control types;
- exact validation-message text;
- exact confirmation-message text;
- implementation details that are not present in the requirements.

Where the BRD defines an outcome but not the UI mechanism, describe the
expected business outcome rather than assuming how the UI prevents or displays
it.

Also include concise sections for:

- proposed automation architecture/structure if implementation were later
  requested;
- selector/locator strategy at a conceptual level only;
- synchronization/wait strategy;
- test independence and browser-session state handling;
- approach for scenarios that intentionally require sequential state;
- information that must be confirmed before implementation begins.

Keep the document proportional to this small assessment.

Use TEST_ANALYSIS.md as the source for the selected subset.
Do not add new automation scenarios unless necessary to clarify one of the
already selected scenarios.

After creating AUTOMATION_DESIGN.md:

1. Review it against BRD.md, REQUIREMENTS.md, and TEST_ANALYSIS.md.
2. Confirm that every designed scenario belongs to the accepted subset.
3. Identify any technical assumption that remains unresolved.
4. Report any place where the design could not be made more specific without
   inventing application details.

   ...
   ```

   ## Prompt 8: SDET Automation Design Refinement v1.1
   ```text

   Review AUTOMATION_DESIGN.md against BRD.md, REQUIREMENTS.md, and the accepted
TEST_ANALYSIS.md.

The design is generally good, but make the following focused corrections.

1. Keep the automation scope aligned with the accepted TEST_ANALYSIS.md.

Scenario E in TEST_ANALYSIS.md covers:
- availability decrease after confirmation;
- persistence across a second reservation in the same browser session.

Refresh/reset verification was not selected as part of Scenario E.

Remove refresh/reset verification from Scenario E's objective, test flow,
assertions, and required scenario behavior.

Do not create a new refresh scenario.

2. Remove prescriptive "return to class selection" wording.

The BRD only requires that after successful confirmation the user can start
another reservation and the application returns to a state where another
reservation can be created.

Replace statements such as:
"return to class selection"

with requirement-faithful wording such as:
"start another reservation" or
"return to a state where another reservation can be created."

Apply the same correction to the pre-implementation questions.

3. Correct Scenario D — Price Calculation Across Classes.

The main requirement is verification of total price in the reservation summary.

Do not confirm reservations in these variations unless confirmation is needed
for the selected scenario, which it is not.

Remove the contradiction where the flow says confirmation is optional but
State Considerations says every variation confirms and changes availability.

The preferred design is:
- select class/session;
- specify participant count;
- verify price in the reservation summary;
- end the variation without confirmation.

State considerations should therefore state that no reservation is confirmed
and availability is not changed by this scenario.

4. In Scenario D, do not say that total price may be shown in the summary
"or confirmation".

The requirement explicitly states that the reservation summary should show the
total price.

Keep the assertion tied to the summary.

5. Replace storage-like wording such as:
"reservation is recorded"

with requirement-faithful wording such as:
"reservation is successfully confirmed."

6. Do not prescribe exact UI wording when the BRD does not provide it.

For example, replace:
displays "10 places available"

with:
indicates that 10 places remain

or equivalent behavior-focused wording.

7. In the wait strategy, do not prescribe an arbitrary 5–10 second timeout
before the application is known.

Describe the timeout as configurable and to be determined from observed
application behavior.

Do not change:
- the five selected automation scenarios;
- the proposed technology stack;
- the overall architecture unless required by the corrections above.

Do not add new requirements, scenarios, selectors, URLs, or UI assumptions.

After editing, report exactly what was corrected.
...
```

## Prompt 9: SDET Automation Design Refinement v1.2
```Text

Perform one final consistency cleanup of AUTOMATION_DESIGN.md.

Make only these corrections:

1. In the pre-implementation checklist, replace:
   "How does the user return to class selection after confirmation?"

   with requirement-faithful wording such as:
   "How does the user start another reservation after confirmation?"

   Do not assume a specific UI state.

2. In Scenario B, replace the broad statement:
   "All state must persist in the browser session."

   with wording that reflects the actual requirement:
   availability changes resulting from confirmed reservations persist within
   the current browser session, while invalid attempts do not change
   availability.

3. In Scenario D, use the requirement terminology:
   "Total price = Participant Count × Price per Participant"
   instead of "Price per Class."

4. In Scenario A State Considerations, remove refresh/reset verification because
   refresh behavior is not part of the selected automation subset.
   Do not create a separate refresh scenario.

Do not change the five selected scenarios, test data, architecture, technology
stack, or any other design content.

After editing, report only the four corrections made.
...
```

## Prompt 10: SDET Claude.md Update
```Text

Review and update CLAUDE.md for this SDET assessment project.

Before editing, read:

- BRD.md
- REQUIREMENTS.md
- TEST_ANALYSIS.md
- AUTOMATION_DESIGN.md

Update CLAUDE.md so that it contains only stable project guidance that would be useful in future Claude Code interactions.

CLAUDE.md should clearly define:

- the project purpose;
- the SDET assessment role and expected deliverables;
- BRD.md as the authoritative business source;
- REQUIREMENTS.md as the reviewed working representation of the BRD;
- the purpose of TEST_ANALYSIS.md;
- the purpose of AUTOMATION_DESIGN.md;
- the project file structure;
- important working boundaries;
- the proposed automation stack:
  Java, Selenium WebDriver, Cucumber, TestNG, Maven;
- rules for handling unknown implementation details;
- rules for checking Claude Code output before accepting it.

Important constraints:

- Keep CLAUDE.md concise and project-level.
- Do not duplicate detailed content from TEST_ANALYSIS.md or AUTOMATION_DESIGN.md.
- Do not copy individual test scenarios into CLAUDE.md.
- Do not include temporary task instructions or prompt history.
- Do not invent application URLs, selectors, DOM structure, control types, or implementation details.
- Do not state that automated tests were implemented or executed.
- Do not modify BRD.md or any other project file.
- Preserve the distinction between business requirements and technical design decisions.
- If REQUIREMENTS.md conflicts with BRD.md, BRD.md remains authoritative.
- Unknown behavior should be identified as an ambiguity or technical unknown, not silently assumed.

Keep the document proportional to this small assessment project.

After updating CLAUDE.md, report:
1. what sections were added or changed;
2. what content was intentionally excluded because it belongs in another artifact;
3. whether any existing CLAUDE.md guidance conflicted with the current project state.

...
```

## Prompt 11:
```Text




