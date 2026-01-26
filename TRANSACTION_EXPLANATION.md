# Transaction Explanation Example

Below is an example of how a single transaction prediction is explained
using the standardized reason code framework.

---

## Transaction Summary

- **Transaction ID:** Test Sample #44
- **Model Decision:** FRAUD
- **Model Confidence:** Very High (1.00)

---

## Primary Reason Codes (Why this transaction was flagged)

### RC01 – Destination Currency / Corridor Risk

- **Direction:** Increases fraud risk
- **Active Signal:** Destination currency = MXN
- **Impact Strength:** High

### RC04 – Location / Geographic Inconsistency

- **Direction:** Increases fraud risk
- **Active Signal:** Account location and IP location mismatch
- **Impact Strength:** Medium

### RC05 – Device / IP Trust Profile

- **Direction:** Increases fraud risk
- **Active Signal:** New or unfamiliar device detected
- **Impact Strength:** Medium

---

## Secondary Contributing Factors

### RC06 – Account Tenure and Transaction Behavior Pattern

- **Direction:** Increases fraud risk
- **Active Signal:** Elevated transaction velocity in last 24 hours
- **Impact Strength:** Low

---

## Protective Signals

### RC05 – Device / IP Trust Profile

- **Direction:** Reduces fraud risk
- **Active Signal:** High device trust score
- **Impact Strength:** Low

---

## Notes

- Explanations are generated using SHAP values aggregated at the
  reason-code level.
- One-hot encoded model features are not exposed directly.
- Sensitive attributes (e.g., IP addresses) are abstracted for privacy.
