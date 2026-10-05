---
type: Component
title: Factors
description: Factors is the core actuarial lookup component located in src/main/kotlin/com/tidewell/rating/Factors.kt.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/entities/factors.md
tags:
- rating-service
- entities
sources:
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/kotlin/com/tidewell/rating/Factors.kt
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

<!-- anchor: src/main/kotlin/com/tidewell/rating/Factors.kt:L1-L20 -->

# Factors

`Factors` is the core actuarial lookup component located in `src/main/kotlin/com/tidewell/rating/Factors.kt`. It contains the pricing factor tables and generates the list of multiplier lines applied during premium evaluation in [[concepts/pricing-calculation]].

## Responsibilities

- **Factor Table Definitions**: Houses the static lookup tables for base rates, postcode geographic risk bands, claims history, and optional cover add-ons.
- **Factor Resolution (`lookup`)**: Evaluates an incoming [[entities/model|PriceRequest]] and constructs a list of `Line` objects containing the factor name and corresponding multiplier.
- **Default Fallbacks**: Provides fallback multipliers when requested parameters (such as unlisted postcodes or cover options) are not found in the lookup tables.

## Dependencies

- **[[entities/model]]**: Consumes `com.tidewell.rating.PriceRequest` as the input payload for calculations and constructs `com.tidewell.rating.Line` instances as outputs.
- **[[entities/rating-service]]**: Invokes `Factors.lookup` during the quote and policy rating orchestration workflows.
- **[[decisions/factor-table-maintenance]]**: Governs the manual update cycle and spreadsheet verification procedures for the factor tables.

---

## Factor Tables and Constants

The companion object of `Factors` defines the static lookup tables:

### Base Rates (`BASE_RATE_PENCE`)
Specifies the initial unadjusted base rate in pence per product type:
```kotlin
val BASE_RATE_PENCE = mapOf(
    "home" to 28_000L,
    "motor" to 41_000L
)
```

### Postcode Risk Bands (`POSTCODE_BANDS`)
Maps the first two characters of a UK postcode (`req.postcode.take(2)`) to a regional risk multiplier:
```kotlin
val POSTCODE_BANDS = mapOf(
    "EC" to 1.35,
    "M1" to 1.2,
    "LS" to 1.1,
    "EX" to 0.9,
    "TR" to 0.85
)
```
*If a postcode prefix is not found in `POSTCODE_BANDS`, the lookup defaults to `1.0`.*

### Claims History Multipliers (`CLAIMS`)
Maps the number of claims lodged in the last 5 years to a risk multiplier:
```kotlin
val CLAIMS = mapOf(
    0 to 0.9,
    1 to 1.15,
    2 to 1.35,
    3 to 1.6
)
```
*The claims count is capped using `.coerceAtMost(3)`. If unresolved, it defaults to `1.6`.*

### Cover Options (`COVER_OPTIONS`)
Maps selectable add-on cover identifiers to their premium multiplier:
```kotlin
val COVER_OPTIONS = mapOf(
    "accidental_damage" to 1.12,
    "legal_cover" to 1.03,
    "courtesy_car" to 1.05
)
```
*Any cover option in `req.coverOptions` not found in the table defaults to `1.0`.*

---

## Lookup Evaluation Logic

The `lookup(req: PriceRequest): List<Line>` method evaluates a `PriceRequest` against the defined tables to produce multiplier lines:

```kotlin
fun lookup(req: PriceRequest): List<Line> = listOf(
    Line("postcode_band", POSTCODE_BANDS[req.postcode.take(2)] ?: 1.0),
    Line("claims_history", CLAIMS[req.claimsLast5Years.coerceAtMost(3)] ?: 1.6),
    Line("sum_insured", if (req.sumInsuredPence > 50_000_000) 1.25 else 1.0),
) + req.coverOptions.map { Line(it, COVER_OPTIONS[it] ?: 1.0) }
```

### Output Line Breakdown
1. **`postcode_band`**: Extracted from `req.postcode.take(2)` via `POSTCODE_BANDS` (default `1.0`).
2. **`claims_history`**: Extracted from `req.claimsLast5Years.coerceAtMost(3)` via `CLAIMS` (default `1.6`).
3. **`sum_insured`**: Evaluates `req.sumInsuredPence`. If greater than `50_000_000` (50 million pence / £500,000), applies a `1.25` multiplier; otherwise `1.0`.
4. **Dynamic Cover Options**: Iterates over `req.coverOptions`, creating a `Line` for each option using `COVER_OPTIONS` (default `1.0`).

---

## Maintenance and Testing Notes

As noted in the codebase documentation:
- The rating factor table is modified by the pricing team approximately once a month.
- There are no automated unit tests for `Factors` or `lookup()`; updates are validated manually against actuarial spreadsheets.
- Refer to [[decisions/factor-table-maintenance]] for details on the operational process.
