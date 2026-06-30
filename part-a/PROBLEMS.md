# IRCTC Problem Discovery — Part A

## Summary

* **Total problems documented:** 6 (3 given + 3 self-discovered)
* **Platform explored:** irctc.co.in
* **Date of exploration:** *(Add today's date)*
* **Devices used:** Desktop (Google Chrome)

---

# Problem 1: Tatkal Booking Crashes at 10:00 AM [Given]

## What is broken

The IRCTC website becomes extremely slow or unresponsive when Tatkal booking opens at 10:00 AM. Users experience server errors, session timeouts, CAPTCHA resets, and failed bookings due to sudden traffic spikes.

## Affected users

* Tatkal passengers
* Daily commuters
* Emergency travelers
* Approximately 20–40 lakh users attempting bookings during the Tatkal window

## Frequency

Occurs daily during the Tatkal booking window (10:00–10:05 AM).

## Current flow — step by step

1. User logs into IRCTC before 10:00 AM.
2. Searches for the desired train.
3. Selects Tatkal quota.
4. Fills passenger details.
5. Clicks **Book Now** at 10:00 AM.
6. Website freezes or returns an HTTP 502/session timeout.
7. User refreshes the page and finds Tatkal seats already booked.

## Where exactly it breaks

**Step 6.**

The backend receives a massive number of concurrent requests but provides no waiting queue or booking progress information, leaving users unsure whether their request is still processing.

---

# Problem 2: Search Filters Do Not Work Reliably [Given]

## What is broken

Train search filters such as class, quota, and seat availability do not always return accurate results. Filters may reset after page refreshes or display trains that do not satisfy the selected criteria.

## Affected users

* All train-search users
* First-time users
* Senior citizens
* Users comparing multiple trains

## Frequency

Occurs intermittently, especially during periods of high traffic.

## Current flow — step by step

1. User enters source, destination, and travel date.
2. Clicks **Search Trains**.
3. Search results are displayed.
4. User applies filters such as Sleeper Class and Available Seats.
5. Results reload.
6. Waitlisted trains still appear or filters reset automatically.
7. User manually reviews the complete train list again.

## Where exactly it breaks

**Steps 5–6.**

The selected filter state is not consistently preserved after refreshing availability data, resulting in inaccurate search results.

---

# Problem 3: Seat Selection Resets Randomly [Given]

## What is broken

Passengers selecting a preferred berth sometimes lose their selection after moving to the next booking step. The system replaces the chosen berth with automatic seat allocation.

## Affected users

* Families
* Senior citizens
* Persons with disabilities
* Passengers requesting lower berths

## Frequency

Estimated to occur in 15–25% of booking sessions involving manual seat selection.

## Current flow — step by step

1. User selects a train and class.
2. Opens the seat map.
3. Chooses a preferred berth.
4. Clicks **Proceed**.
5. Passenger details page loads.
6. The selected berth changes to **Auto** or another berth.
7. User loses the original seat selection.

## Where exactly it breaks

**Steps 4–6.**

The selected seat information is not consistently preserved between booking screens.

---

# Problem 4: Mandatory Login Before Viewing Train Availability [Self-Discovered]

## What is broken

Users are required to log in before they can view train availability and booking information. This creates unnecessary friction for users who simply want to check train timings, fares, or seat availability before deciding to book.

## How I found it

While exploring the website, I searched for trains between two stations. Instead of displaying train details directly, the website required me to log in before continuing.

## Description

The train search flow redirects users to the login page before displaying train availability.

## Affected users

* First-time visitors
* Occasional travelers
* Users comparing travel options
* Users checking schedules before deciding to book

## Frequency

Occurs every time an unauthenticated user attempts to access booking-related information.

## Current flow — step by step

1. User opens the IRCTC website.
2. User enters source station, destination, and journey date.
3. User clicks **Search Trains**.
4. The website redirects the user to the login page.
5. User must log in or create an account.
6. Only after successful authentication can train availability be viewed.

## Where exactly it breaks

**Step 4.**

The browsing experience is interrupted by mandatory authentication, even though the user is only attempting to view train information.

---

# Problem 5: Username Availability Validation is Inconsistent [Self-Discovered]

## What is broken

The registration page does not consistently validate username availability in real time. Users often discover that their chosen username is unavailable only after completing most or all of the registration form.

## How I found it

While creating an account, I entered different usernames. Sometimes the availability message appeared only after clicking another field, and in one attempt it appeared only after submitting the entire registration form.

## Description

The username field provides inconsistent validation, causing users to repeat parts of the registration process.

## Affected users

* New users registering for IRCTC
* Users creating accounts during busy periods

## Frequency

Observed multiple times during registration testing.

## Current flow — step by step

1. User opens the registration page.
2. User enters a preferred username.
3. User continues filling personal information.
4. No reliable confirmation of username availability is shown.
5. User submits the registration form.
6. The system reports that the username already exists.
7. User returns to choose another username and resubmits the form.

## Where exactly it breaks

**Steps 4–6.**

The absence of dependable real-time validation increases user effort and extends the registration process.

---

# Problem 6: Login Session Expires During Authentication [Self-Discovered]

## What is broken

If users spend several minutes completing the login process, the website displays a session expiration message and requires them to restart. During my testing, the timeout message continued to appear even after retrying immediately.

## How I found it

While testing the login page, I intentionally spent more than five minutes entering details. After submitting the form, the website reported that the session had expired and asked me to log in again.

## Description

An alert appears indicating that the session has expired and instructs the user to return and repeat the login process.

## Affected users

* First-time users
* Elderly users
* Users on slower internet connections
* Users taking longer to complete login

## Frequency

Observed during testing when the login session exceeded several minutes.

## Current flow — step by step

1. User opens the login page.
2. User begins entering login credentials.
3. More than five minutes are spent completing the form.
4. User clicks **Login**.
5. A session expiration alert is displayed.
6. User repeats the login process.
7. The timeout message may continue appearing, preventing successful login.

## Where exactly it breaks

**Steps 5–7.**

The application expires the login session without warning and provides a poor recovery experience, forcing users to repeat their work.

