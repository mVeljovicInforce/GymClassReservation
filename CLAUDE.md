# Project Context

This is an individual SDET assessment project for the Gym Class Reservation
web application.

The SDET task is to:

- analyze the application requirements;
- identify areas suitable for automation;
- select a small meaningful automation subset;
- create an automated-test design specification for that subset.

Implementation and execution of automated tests are not required.

## Sources of Truth

`BRD.md` is the authoritative business source.

`REQUIREMENTS.md` is the project working representation derived from `BRD.md`.

If `REQUIREMENTS.md` conflicts with `BRD.md`, the BRD remains authoritative
unless a human-approved requirement change exists.

Do not invent requirements.

If behavior is unclear or not defined, identify it as an ambiguity or technical
assumption instead of treating an assumption as a requirement.

## Project Structure

- Business source: `BRD.md`
- Working requirements: `REQUIREMENTS.md`
- Assessment prompts: `PROMPTS.md`
- SDET test analysis: `TEST_ANALYSIS.md`
- Automated-test design specification: `AUTOMATION_DESIGN.md`

## SDET Working Rules

- Keep the work proportional to this small application.
- Base test analysis and automation design on confirmed requirements.
- Distinguish required behavior from technical assumptions.
- Prefer a small meaningful automation subset over exhaustive coverage.
- Include positive, negative, boundary, state-transition, and calculation
  behavior where relevant during analysis.
- Do not implement automated tests unless explicitly requested.
- Do not claim that automated tests were executed.
- Do not invent application URLs, selectors, DOM structure, or implementation
  details.
- Keep technical assumptions visible in the automation design.
- Do not modify `BRD.md`.

## Proposed Automation Technology

For the automation design, assume the following familiar SDET stack unless
there is a reason to propose otherwise:

- Java
- Selenium WebDriver
- Cucumber
- TestNG
- Maven

This is a proposed design technology only. No automation implementation is
required for this assessment.

## Checking

Claude Code output must be reviewed against `BRD.md`.

A document is not accepted merely because Claude Code says it is complete.

Before accepting:
- verify that requirements are faithfully represented;
- verify that selected automation scenarios map to actual requirements;
- verify that assumptions are clearly labeled;
- verify that no unsupported behavior has been invented.