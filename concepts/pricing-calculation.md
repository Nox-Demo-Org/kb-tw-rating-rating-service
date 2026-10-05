---
type: Concept
title: Pricing Calculation
description: The pricing calculation in rating-service computes risk multipliers and baseline premiums based on policy attributes supplied in a PriceRequest (see model).
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/concepts/pricing-calculation.md
tags:
- rating-service
- concepts
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

# Pricing Calculation

The pricing calculation in `rating-service` computes risk multipliers and baseline premiums based on policy attributes supplied in a `PriceRequest` (see [[entities/model]]). Calculations are driven by factor tables defined in `Factors.kt` and evaluated via `Factors.lookup()`.

---

## Calculation Algorithm

When calculating pricing factors, `Factors.lookup()` evaluates four primary risk components against the incoming [[entities/model|PriceRequest]], generating an itemized list of `Line` multiplier objects.

```
                  ┌──────────────────────────────────────────┐
                  │          PriceRequest Input              │
                  └────────────────────┬─────────────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────────┐
         ▼                             ▼                             ▼
┌──────────────────┐          ┌──────────────────┐          ┌──────────────────┐
│  Postcode Band   │          │  Claims History  │          │   Sum Insured    │
│ postcode.take(2) │          │ coerceAtMost(3)  │          │ > 50,000,000 p   │
└────────┬─────────┘          └────────┬─────────┘          └────────┬─────────┘
         │                             │                             │
         └─────────────────────────────┼─────────────────────────────┘
                                       │
                                       ▼
                       ┌───────────────────────────────┐
                       │ Cover Options (coverOptions)  │
                       └───────────────┬───────────────┘
                                       │
                                       ▼
                       ┌───────────────────────────────┐
                       │     List<Line> Multipliers    │
                       └───────────────────────────────┘
```

### 1. Base Rates (`BASE_RATE_PENCE`)
Base premiums are established per insurance product type in pence:

| Product Key | Base Premium (Pence) | Equivalent Value (£) |
|-------------|----------------------|----------------------|
| `"home"`    | `28_000L`            | £280.00              |
| `"motor"`   | `41_000L`            | £410.00              |

### 2. Postcode Band Multiplier (`postcode_band`)
The calculation extracts the first two characters of the postcode (`req.postcode.take(2)`) and looks up the corresponding multiplier in `POSTCODE_BANDS`. If the postcode prefix is not found, it defaults to `1.0`.

* `"EC"`: `1.35`
* `"M1"`: `1.2`
* `"LS"`: `1.1`
* `"EX"`: `0.9`
* `"TR"`: `0.85`
* *Default / Fallback*: `1.0`

### 3. Claims History Multiplier (`claims_history`)
The number of claims in the last 5 years (`req.claimsLast5Years`) is capped at a maximum of 3 using `.coerceAtMost(3)` and looked up in `CLAIMS`. If no match is found, it falls back to `1.6`.

* `0` claims: `0.9` (discount for zero claims)
* `1` claim: `1.15`
* `2` claims: `1.35`
* `3` (or more) claims: `1.6`
* *Default / Fallback*: `1.6`

### 4. Sum Insured Multiplier (`sum_insured`)
Evaluates whether the policy sum insured exceeds the 50,000,000 pence threshold (£500,000):

$$\text{Multiplier} = \begin{cases} 1.25 & \text{if } \text{sumInsuredPence} > 50{,}000{,}000 \\ 1.0 & \text{otherwise} \end{cases}$$

### 5. Cover Options Multipliers
Each selected cover option string in `req.coverOptions` is evaluated against `COVER_OPTIONS`. Unrecognized options default to `1.0`.

* `"accidental_damage"`: `1.12`
* `"legal_cover"`: `1.03`
* `"courtesy_car"`: `1.05`
* *Default / Unrecognized*: `1.0`

---

## Output Line Breakdown

The result of `Factors.lookup(req)` returns a combined `List<Line>`:

1. `Line("postcode_band", multiplier)`
2. `Line("claims_history", multiplier)`
3. `Line("sum_insured", multiplier)`
4. One `Line(optionName, multiplier)` for each entry in `req.coverOptions`

---

## Operational Considerations

* **Factor Maintenance**: Factor tables are maintained manually in code by the pricing team on an approximate monthly cadence.
* **Verification**: There are no automated unit tests for `Factors` or `lookup()`. Changes are manually checked and reconciled against pricing spreadsheets prior to deployment. See [[decisions/factor-table-maintenance]] for details.
* **Integration**: Orchestration of quote requests via gRPC is managed by [[entities/rating-service]], and partial-term rate adjustments are handled by [[concepts/prorata-rounding]] and [[entities/prorata]].
