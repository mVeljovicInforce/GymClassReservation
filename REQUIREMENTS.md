# Working Requirements — Gym Class Reservation

## Purpose & User Profile

The Gym Class Reservation application enables visitors to a local gym to reserve places in predefined group exercise sessions through a web-based interface.

The application is intended for demonstration and review. It requires no gym membership, user account, or personal information.

---

## Available Classes

The gym offers four group classes with fixed pricing per participant:

| Class | Price per Participant |
|---|---:|
| Yoga | €8 |
| Pilates | €10 |
| Functional Training | €12 |
| Spinning | €11 |

Each class has exactly three predefined sessions.

---

## Sessions & Initial Availability

Each session has a maximum capacity of **10 participants**.

### Yoga Sessions

| Session | Initial Available Places |
|---|---:|
| Monday 18:00 | 10 |
| Wednesday 19:00 | 6 |
| Saturday 10:00 | 2 |

### Pilates Sessions

| Session | Initial Available Places |
|---|---:|
| Tuesday 18:00 | 8 |
| Thursday 19:00 | 4 |
| Saturday 11:30 | 0 |

### Functional Training Sessions

| Session | Initial Available Places |
|---|---:|
| Monday 19:30 | 5 |
| Wednesday 18:00 | 10 |
| Friday 18:30 | 3 |

### Spinning Sessions

| Session | Initial Available Places |
|---|---:|
| Tuesday 19:30 | 7 |
| Thursday 18:00 | 1 |
| Sunday 10:00 | 10 |

---

## Class Selection

- The user must be able to select one of the four available gym classes.
- When a class is selected, the application should make its three sessions available for selection.
- The user should be able to change the selected class before confirming the reservation.

---

## Session Selection

- The user must be able to choose one of the three predefined sessions for the selected class.
- The application should show:
  - the session day and time;
  - whether places are still available;
  - how many places remain.
- A full session cannot be reserved.

---

## Number of Participants

- A reservation must be for at least **1 participant**.
- The maximum number of participants that can be reserved is the number of places currently remaining in the selected session.
  - If 10 places remain, the user may reserve 1–10 places.
  - If 4 places remain, the user may reserve 1–4 places.
  - If 0 places remain, the session cannot be reserved.
- The application must not allow confirmation of a reservation that exceeds the remaining capacity.

---

## Price Calculation

- Each class has a fixed price per participant (as specified in the Classes section).
- Total reservation price = **number of participants × price per participant**.
- The application must calculate the total price automatically.
- No payment is processed through the application.

---

## Reservation Summary

Before confirmation, the user should be able to review the reservation.

The summary should show:
- selected class;
- selected session;
- number of participants;
- price per participant;
- total price.

The user should still be able to change the reservation (class, session, or number of participants) before confirming.

---

## Reservation Confirmation

- A valid reservation must be explicitly confirmed by the user.
- After confirmation, the application should clearly indicate that the reservation was successful.
- The confirmation should provide enough information for the user to understand what was reserved.
- No email, SMS, printed ticket, or external confirmation is required.

---

## Availability After Confirmation

When a reservation is confirmed:
- The number of reserved places must be deducted from the remaining availability of that session.
- Example: If a session has 6 places available and a user confirms a reservation for 2 participants, that session should show 4 places available.
- Updated availability should remain in effect while the application remains open in the current browser session.
- Refreshing or reopening the application restores the initial availability values listed above.
- The application does not need to synchronize availability between different users or different browsers.

---

## Starting Another Reservation

After a successful reservation:
- The user should be able to start another reservation.
- The application should return to a state in which another reservation can be created.
- Availability changes from previously confirmed reservations during the current browser session must remain in effect.

---

## Validation & Invalid/Incomplete Reservations

The application must prevent confirmation of a reservation when required information is incomplete or invalid:

- No class has been selected.
- No session has been selected.
- The number of participants is below 1.
- The number of participants exceeds the remaining places in the selected session.
- The selected session has no available places (0 places remaining).

The user should receive enough feedback to understand that the reservation cannot yet be confirmed.

---

## Reservation Flow

The standard reservation flow is:

1. Choose a class.
2. Choose one of the three sessions for that class.
3. Choose the number of participants.
4. Review the reservation summary.
5. Confirm the reservation.
6. See the reservation confirmation.
7. (Optional) Start another reservation if desired.

Before confirmation, the user should be able to change the selected class, session, or number of participants.

---

## Data Collection & Privacy

The application must not require or collect any personal information, including:
- name;
- email address;
- telephone number;
- postal address;
- account information;
- payment information.

Reservations are anonymous.

---

## Scope — What Is Included

The application includes only the gym-class reservation functionality described in this requirements document.

---

## Scope — What Is Out of Scope

The following are explicitly out of scope:

- user accounts;
- authentication;
- memberships;
- subscriptions;
- payment processing;
- discounts or promotions;
- database storage;
- backend or server-side functionality;
- live multi-user availability;
- waiting lists;
- personal-trainer scheduling;
- cancellation;
- rescheduling;
- trainer management;
- email or SMS confirmation;
- external calendar integration;
- external APIs;
- complex date or calendar calculations.

---

## Implementation Freedom

This requirements document specifies the required business behavior only.

It does not prescribe:
- programming language;
- framework;
- file structure;
- internal implementation;
- state-management approach;
- automated-test technology;
- test-document format;
- project-plan format.

---

## Delivery Expectation

The result should be a small web application that can be demonstrated and reviewed against the requirements in this document.

The application should be simple enough for a reviewer to understand the main reservation flow and verify representative behavior.
