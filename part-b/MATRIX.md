# Impact vs Effort Matrix

## The Matrix

|                    | **Low Effort** | **High Effort** |
|--------------------|----------------|-----------------|
| **High Impact** | Search Filter Persistence (Problem 2)<br>Username Validation (Problem 5)<br>Session Expiry Warning (Problem 6) | Tatkal Virtual Queue (Problem 1)<br>Seat Preference Locking (Problem 3) |
| **Low Impact** | Guest Train Search (Problem 4) | AI Waitlist Confirmation Predictor |

---

# How I Scored Each Dimension

## Impact Scoring (1–5)

Impact was determined using the following criteria:

- Number of affected users.
- Whether the issue affects the core booking flow.
- Frequency of occurrence.
- User frustration caused.
- Potential business impact.

---

## Effort Scoring (1–5)

Effort was determined based on:

- Number of frontend changes.
- Backend modifications.
- Database updates.
- API development.
- Third-party integrations.
- Infrastructure changes.
- Risk of affecting existing services.

---

# Placement Justifications

---

## Problem 1 — Tatkal Virtual Queue System

### Quadrant

**High Impact • High Effort**

Tatkal booking affects millions of users every day and directly impacts successful ticket booking. Implementing a distributed queue system requires major backend changes, real-time communication, and infrastructure upgrades. Although expensive, this feature provides the highest long-term value and should be treated as a strategic project.

---

## Problem 2 — Search Filter Persistence

### Quadrant

**High Impact • Low Effort**

Train search is one of the most frequently used features on the platform. Preserving filter state mainly involves frontend state management and minor backend support without requiring major infrastructure changes. This makes it an ideal quick win with immediate usability improvements.

---

## Problem 3 — Seat Preference Locking

### Quadrant

**High Impact • High Effort**

Seat allocation directly affects booking satisfaction, especially for families and senior citizens. The solution requires changes to the reservation engine, concurrency handling, and temporary seat locking mechanisms. While technically complex, it significantly improves user trust and booking reliability.

---

## Problem 4 — Guest Train Search

### Quadrant

**Low Impact • Low Effort**

Allowing guest users to browse train schedules improves the experience for new visitors but does not directly affect successful bookings. The implementation mainly involves exposing read-only APIs and adjusting authentication checks. It can be completed quickly when development resources are available.

---

## Problem 5 — Real-Time Username Validation

### Quadrant

**High Impact • Low Effort**

Registration is the first interaction many users have with IRCTC. Adding real-time username validation only requires a lightweight API endpoint and frontend validation logic. This small improvement significantly reduces registration failures and improves the onboarding experience.

---

## Problem 6 — Session Expiry Warning

### Quadrant

**High Impact • Low Effort**

Unexpected session expiry interrupts the booking process and frustrates users. Implementing activity tracking, session timers, and refresh mechanisms is relatively straightforward while providing immediate benefits. This feature reduces repeated logins and support requests with minimal engineering effort.

---

## AI Feature — Waitlist Confirmation Probability Predictor

### Quadrant

**Low Impact • High Effort**

Although AI-powered waitlist prediction improves decision-making, it does not fix a core booking failure. Building, training, deploying, and continuously maintaining a machine learning pipeline requires significant engineering effort and historical data. This feature should be considered a long-term enhancement after critical booking issues have been addressed.

---

# Recommended Sprint Order

1. Search Filter Persistence (Problem 2)
2. Real-Time Username Validation (Problem 5)
3. Session Expiry Warning (Problem 6)
4. Guest Train Search (Problem 4)
5. Tatkal Virtual Queue (Problem 1)
6. Seat Preference Locking (Problem 3)
7. AI Waitlist Confirmation Predictor