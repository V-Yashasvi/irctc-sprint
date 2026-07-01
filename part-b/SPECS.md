# IRCTC Product Design Sprint — Part B
## Feature Specifications

> **Repository:** irctc-sprint  
> **Reference Document:** `part-a/PROBLEMS.md`

---

# Feature Spec 1: Tatkal Virtual Queue System

## Problem Statement

As documented in **Problem 1** of Part A, the IRCTC website experiences severe server overload every day during Tatkal booking between **10:00 AM and 10:05 AM**. Millions of users attempt to book simultaneously, resulting in server crashes, session timeouts, failed payments, and booking failures. Users receive no feedback about their booking status, leading to repeated refreshes that further increase server load.

---

## Current State (from Part A)

Currently, users log into IRCTC before Tatkal opens, search for their train, fill passenger details, and click **Book Now** exactly at 10:00 AM.

At this point the backend receives millions of requests simultaneously. Since requests are processed immediately without any traffic management, users encounter HTTP 502 errors, session timeouts, and failed bookings even before seat availability is checked.

The failure occurs during **Step 6** of the current booking flow.

---

## Proposed Solution

Introduce a **Virtual Tatkal Queue System**.

Instead of allowing every user to hit the booking servers simultaneously, users entering the Tatkal booking page before opening time are automatically assigned a queue number.

Once Tatkal opens, users are processed in queue order based on server capacity.

The interface continuously displays:

- Queue Position
- Estimated Wait Time
- Live Progress Bar
- Countdown Timer
- Booking Window

When the user's turn arrives, they receive a **90-second booking slot** to complete their reservation.

---

## Proposed User Flow

1. User opens Tatkal booking page before 10:00 AM.
2. User is automatically assigned a queue position.
3. Screen displays remaining time until booking opens.
4. Queue starts processing at 10:00 AM.
5. Queue number updates in real time.
6. User receives notification when their turn arrives.
7. Passenger details are pre-filled.
8. User completes payment within 90 seconds.
9. Booking confirmation is displayed.
10. If user fails to complete booking, the slot is released to the next user.

---

## Technical Implementation Plan

### System Components Affected

- Frontend Booking UI
- Booking Backend
- Session Management
- Queue Service
- Notification Service
- Railway Reservation API

---

### New Data Requirements

New Queue Collection

| Field | Description |
|--------|-------------|
| queueId | Unique Queue ID |
| userId | Logged-in user |
| queuePosition | Current position |
| bookingStatus | Waiting / Active / Completed / Expired |
| bookingWindow | 90 second timer |
| joinedAt | Queue entry timestamp |

---

### API Changes

#### POST /tatkal/queue/join

Creates queue entry.

Response

```json
{
  "queueId":"Q91231",
  "position":4210
}
```

---

#### GET /tatkal/queue/status/{queueId}

Returns

```json
{
"position":382,
"estimatedWait":"3 min"
}
```

---

#### POST /tatkal/queue/expire

Expires inactive booking slots.

---

### Frontend Changes

New screens/components:

- Queue Dashboard
- Live Position Counter
- Progress Bar
- Booking Countdown Timer
- Auto-filled Passenger Details
- Queue Notification Popup

---

### Third-party Services

- Redis (Queue Management)
- WebSocket / Socket.IO
- Firebase Push Notifications (Optional)

---

## Success Metrics

- Tatkal booking completion increases from **40% → 70%**
- Server crash rate decreases below **5%**
- Queue abandonment reduced by **60%**
- User satisfaction score increases by **30%**

---

## Edge Cases & Constraints

- User closes browser while in queue
- Internet disconnects temporarily
- Queue server failure
- Payment timeout
- Duplicate login attempts
- Redis failure should automatically fall back to normal booking
- Must support low-bandwidth users through periodic polling if WebSockets fail

---

# Feature Spec 2: Persistent Smart Search Filters

## Problem Statement

As identified in **Problem 2**, train search filters frequently reset or display incorrect search results after refreshing train availability. Users must repeatedly reapply filters, increasing booking time and reducing confidence in search results.

---

## Current State (from Part A)

Users search trains and apply filters such as:

- Sleeper
- AC
- Available Seats

After availability refreshes, filters are reset or ignored, forcing users to manually search again.

Failure occurs during **Steps 5–6**.

---

## Proposed Solution

Implement **Persistent Search Filters**.

Filters remain active until users explicitly clear them.

Availability refreshes should never reset selected filters.

Users can also save frequently used filter combinations.

---

## Proposed User Flow

1. Search train.
2. Apply filters.
3. Filters remain visible.
4. Refresh availability.
5. Results automatically maintain selected filters.
6. User books train.

---

## Technical Implementation

### System Components

- Search API
- Search UI
- Browser Storage
- User Profile

---

### New Data

```
savedFilters
lastUsedFilters
```

---

### API

GET /search

Returns selected filters.

PUT /user/preferences

Stores filter preferences.

---

### Frontend

- Sticky Filter Bar
- Active Filter Chips
- Saved Filters Dropdown
- Clear All Button

---

### Third-party

None

---

## Success Metrics

- Reduce repeated searches by 50%
- Increase successful train selection
- Improve search satisfaction score

---

## Edge Cases

- Filters unavailable for certain routes
- Browser storage disabled
- Session expiration
- Mobile responsiveness

---


# Feature Spec 3: Seat Preference Locking

## Problem Statement

Problem 3 identified that manually selected berths are often replaced with automatic allocation while moving between booking pages. Families, senior citizens, and passengers requiring lower berths lose their selected seats.

---

## Current State

Seat preference is chosen but disappears after clicking Proceed.

Failure occurs during **Steps 4–6**.

---

## Proposed Solution

Introduce **Seat Preference Locking**.

Selected berth remains reserved during the booking session until payment succeeds or the booking session expires.

---

## Proposed User Flow

1. Select berth.
2. Seat temporarily locked.
3. Proceed to passenger details.
4. Confirmation page displays selected berth.
5. Payment completed.
6. Seat confirmed.

---

## Technical Implementation

### Components

- Seat Allocation Service
- Reservation Backend
- Booking Session

---

### New Data

```
seatPreference
seatLockExpiry
bookingSession
```

---

### APIs

POST /seat/lock

POST /seat/unlock

GET /seat/status

---

### Frontend

- Locked Seat Badge
- Reservation Timer
- Warning before expiry

---

### Third-party

None

---

## Success Metrics

- 90% berth preference retention
- Reduced customer complaints
- Faster booking completion

---

## Edge Cases

- Seat already booked
- Lock expires
- Payment failure
- Concurrent booking attempts

---

# Feature Spec 4: Guest Train Search

## Problem Statement

Problem 4 identified unnecessary login requirements before users can even browse trains. This creates friction for first-time visitors and users comparing travel options.

---

## Current State

Users are redirected to login before train availability is displayed.

Failure occurs during **Step 4**.

---

## Proposed Solution

Allow users to browse:

- Train timings
- Fare
- Availability
- Route

without authentication.

Login should only be required before booking.

---

## Proposed User Flow

1. Open website.
2. Search trains.
3. View availability.
4. Compare trains.
5. Login only when booking.

---

## Technical Implementation

### Components

- Search API
- Authentication
- Booking Module

---

### New Data

None

---

### APIs

GET /public/search

GET /public/train

---

### Frontend

Guest Mode Banner

Login Before Booking Popup

---

### Third-party

None

---

## Success Metrics

- Increased visitor engagement
- Higher booking conversion
- Lower bounce rate

---

## Edge Cases

- Booking without login
- Session expiration
- Cached search results

---

# Feature Spec 5: Real-Time Username Validation

## Problem Statement

Problem 5 identified inconsistent username validation during registration, forcing users to complete the form before discovering that their chosen username is unavailable.

---

## Current State

Username availability is checked inconsistently.

Users repeat registration multiple times.

---

## Proposed Solution

Provide **instant username availability checking** while typing.

Offer alternative usernames when unavailable.

---

## Proposed User Flow

1. Enter username.
2. System checks instantly.
3. Green ✓ if available.
4. Suggestions shown if unavailable.
5. Complete registration.

---

## Technical Implementation

### Components

- Registration API
- User Database
- Frontend Validation

---

### New Data

Temporary validation cache.

---

### APIs

GET /username/check

Response

```
Available
Unavailable
Suggestions
```

---

### Frontend

Live validation icon

Suggested usernames

---

### Third-party

None

---

## Success Metrics

- 80% reduction in registration retries
- Faster registration
- Reduced user frustration

---

## Edge Cases

- Slow internet
- Duplicate requests
- Cached usernames

---

# Feature Spec 6: Session Expiry Warning & Recovery

## Problem Statement

Problem 6 identified that login sessions expire without warning, forcing users to restart authentication and causing repeated failures.

---

## Current State

Users spend several minutes entering credentials.

Session expires unexpectedly.

Login must be repeated.

---

## Proposed Solution

Introduce a **Session Expiry Warning System**.

Users receive warnings before expiration and can extend the session with one click.

---

## Proposed User Flow

1. Login page opens.
2. Timer silently runs.
3. One minute before expiry, warning appears.
4. User clicks "Continue Session."
5. Session refreshed.
6. Login completed.

---

## Technical Implementation

### Components

- Authentication
- Session Manager
- Frontend Timer

---

### New Data

```
sessionExpiry

lastActivity

refreshToken
```

---

### APIs

POST /session/refresh

GET /session/status

---

### Frontend

Warning Dialog

Continue Session Button

Session Timer

---

### Third-party

None

---

## Success Metrics

- 70% reduction in session expiry errors
- Faster login completion
- Reduced support tickets

---

## Edge Cases

- Token already expired
- Refresh API failure
- Multiple tabs open
- Browser sleep mode

Graceful fallback:

Redirect to login while preserving entered credentials where possible.

---
