# AI Feature Specification: Waitlist Confirmation Probability Predictor

## Problem It Solves

This feature addresses **Problem 2 (Search Filters)** and extends the train search experience by helping users make better booking decisions.

Currently, users only see whether a ticket is Available, RAC, or Waitlisted. However, they receive no indication of whether a waitlisted ticket is likely to be confirmed before the journey date. As a result, many passengers either book tickets that never get confirmed or spend significant time manually searching for alternatives.

---

## Proposed Feature — User Perspective

When a user searches for trains, every waitlisted train displays an AI-generated prediction showing the likelihood of ticket confirmation before departure.

Example:

```
Train: 12622 Tamil Nadu Express

Status: WL 18

🟢 Confirmation Probability: 87%

Expected Status:
Likely Confirmed

Recommended Action:
Book this train.
```

If the prediction indicates a low probability, the system recommends alternative trains with better confirmation chances.

This helps users make informed decisions without manually researching historical confirmation trends.

---

## Model or API Choice

### Model

**XGBoost Classification Model**

### Why XGBoost?

- High prediction accuracy on structured historical railway data.
- Fast inference suitable for millions of daily users.
- Easier to train and maintain than deep neural networks.
- Handles missing values and seasonal variations effectively.

Future versions may migrate to **Google Vertex AI AutoML** for automated retraining and deployment.

---

## Training or Input Data

The model will use historical booking data collected by IRCTC.

### Required Features

- Train Number
- Source Station
- Destination Station
- Journey Date
- Quota (General, Tatkal, Premium Tatkal)
- Current Waitlist Number
- Booking Date
- Day of Week
- Festival or Holiday Indicator
- Historical Cancellation Rate
- Historical Confirmation Rate
- Coach/Class
- Seasonal Demand

### Data Source

- IRCTC Booking Database
- Indian Railways Reservation Records
- Historical Ticket Cancellation Logs
- Calendar and Holiday Database

---

## How Output Is Shown to the User

The prediction appears directly below the ticket status.

Example:

```
----------------------------------------------------

12622 Tamil Nadu Express

Sleeper

Current Status:
WL 18

🟢 87% Chance of Confirmation

Recommendation

✓ Safe to Book

Alternative Trains

12618
12620

----------------------------------------------------
```

Color Indicators

🟢 80–100% → High chance

🟡 50–79% → Moderate chance

🔴 Below 50% → Low chance

Users can tap **Why?** to view a short explanation based on historical booking trends.

---

## Confidence Threshold and Fallback

The AI prediction is displayed only when the model confidence is **80% or higher**.

If confidence is below 80%:

```
Prediction unavailable.

Historical data is insufficient for an accurate estimate.

Please check alternate trains.
```

If the AI service is unavailable:

- Hide the prediction.
- Continue displaying the normal IRCTC booking flow.
- Allow booking without interruption.

---

## Success Metrics

- Increase bookings with confirmed tickets by **25%**.
- Reduce customer complaints related to waitlisted bookings by **20%**.
- Increase user engagement with train recommendations.
- Improve user satisfaction during train selection.
- Reduce manual searches for alternative trains.

---

## Limitations and Risks

- Sudden train cancellations may reduce prediction accuracy.
- Special trains or newly introduced routes may not have sufficient historical data.
- Festival seasons may produce unexpected demand spikes.
- Users may rely too heavily on predictions, even though they are probabilistic.
- The prediction should be presented as guidance, not a guarantee.

---

## Future Enhancements

- Personalized recommendations based on previous journeys.
- Route suggestions with shorter travel times.
- AI-powered fare prediction.
- Smart journey planner integrating buses and metro connections.
- Voice-based train search in regional languages.