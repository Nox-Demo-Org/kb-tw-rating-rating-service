---
type: Component
title: Domain Models (Model.kt)
description: Model.kt defines the core domain representations and Data Transfer Objects (DTOs) used by rating-service to process pricing calculations, factor evaluations, and quote responses.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/entities/model.md
tags:
- rating-service
- entities
sources:
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/kotlin/com/tidewell/rating/Model.kt
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/kotlin/com/tidewell/rating/Factors.kt
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

<!-- anchor: src/main/kotlin/com/tidewell/rating/Model.kt:L1-L1 -->
<!-- anchor: src/main/kotlin/com/tidewell/rating/Factors.kt:L1-L20 -->

# Domain Models (Model.kt)

`Model.kt` defines the core domain representations and Data Transfer Objects (DTOs) used by `rating-service` to process pricing calculations, factor evaluations, and quote responses.

## Core Models

Based on usage in [[entities/factors]] (`Factors.kt`), the domain models include:

### `PriceRequest`
Encapsulates the policy parameters required to perform a factor lookup and premium calculation:
* `postcode`: `String` - Postal code used to determine geographical risk factors (e.g. via `POSTCODE_BANDS` lookup on `req.postcode.take(2)`).
* `claimsLast5Years`: `Int` - Claim count over the previous 5 years used to determine claims history multipliers.
* `sumInsuredPence`: `Long` (or numeric equivalent) - Policy sum insured in pence, evaluated against the threshold of `50_000_000` pence.
* `coverOptions`: `List<String>` (or collection) - Selected optional add-on covers (such as `"accidental_damage"`, `"legal_cover"`, or `"courtesy_car"`).

### `Line`
Represents an itemized risk factor multiplier or add-on cover adjustment generated during pricing:
* `name`: `String` - Descriptor for the rating factor or cover option (e.g., `"postcode_band"`, `"claims_history"`, `"sum_insured"`, or specific cover option names).
* `multiplier`: `Double` - The numeric multiplier evaluated against base rates during [[concepts/pricing-calculation]].

## Responsibilities

* **Data Encapsulation**: Provide strongly typed data containers for inbound pricing request parameters and rating factor multipliers.
* **Pricing Pipeline Interoperability**: Serve as the common interface between incoming gRPC requests ([[entities/rating-service]], [[summaries/api-spec]]), rating factor lookups ([[entities/factors]]), and calculation algorithms ([[concepts/pricing-calculation]]).

## Dependencies

* **Consumers**:
  * [[entities/factors]]: Accepts `PriceRequest` and produces `List<Line>` via `Factors.lookup(req)`.
  * [[entities/rating-service]]: Uses request and response models to service gRPC pricing operations.
* **Related Concepts**:
  * [[concepts/pricing-calculation]]: Relies on `Line` multiplier values to calculate the final policy premium.
