---
type: Component
title: RatingService
description: RatingService (src/main/kotlin/com/tidewell/rating/RatingService.kt) implements the gRPC interface defined in src/main/proto/rating.proto.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/entities/rating-service.md
tags:
- rating-service
- entities
sources:
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/kotlin/com/tidewell/rating/RatingService.kt
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/proto/rating.proto
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

<!-- anchor: src/main/kotlin/com/tidewell/rating/RatingService.kt:L1-L1 -->
<!-- anchor: src/main/proto/rating.proto:L1-L1 -->

# RatingService

`RatingService` (`src/main/kotlin/com/tidewell/rating/RatingService.kt`) implements the gRPC interface defined in `src/main/proto/rating.proto`. It serves as the primary entry point for core services (such as policy administration) to request real-time policy rating, premium calculations, and pro-rata adjustments.

*(Note: Direct file contents for `RatingService.kt` and `rating.proto` were unavailable due to a fetch exception; details below reflect the architectural specifications and data flow defined across the service).*

## Responsibilities

- **gRPC Interface Handling**: Exposes gRPC endpoints for quote rating, premium calculations, and pro-rata adjustments (see [[summaries/api-spec]] and [[decisions/0001-grpc-for-rating]]).
- **Pricing Orchestration**: Coordinates quote evaluation by receiving policy parameters (product type, postcode, claims history, sum insured, and cover options) and invoking factor lookups (see [[concepts/pricing-calculation]]).
- **Pro-Rata Adjustments**: Orchestrates partial-period premium calculations and adjustments for renewals, cancellations, and mid-term amendments (see [[concepts/prorata-rounding]]).
- **Response Construction**: Constructs final premium results and itemized line breakdowns to return to calling services.

## Dependencies

- **[[entities/factors]] (`Factors.kt`)**: Supplies base rates and actuarial multiplier tables (postcode bands, claims history, sum insured, and cover options) via `Factors.lookup`.
- **[[entities/prorata]] (`ProRata.kt`)**: Provides pro-rated premium calculation and half-up rounding logic.
- **[[entities/model]] (`Model.kt`)**: Provides domain and DTO representations including `PriceRequest`, `Line` multiplier objects, and rating responses.
- **`src/main/proto/rating.proto`**: Protocol buffer definitions governing gRPC request and response schemas.

## Data Flow

1. A calling service sends a pricing or adjustment request via gRPC containing policy attributes.
2. `RatingService` delegates attribute mapping and factor lookups to [[entities/factors]].
3. Evaluated multipliers and base rates are combined to calculate the final policy premium and line item breakdown.
4. For mid-term alterations or cancellations, adjustments are computed using [[entities/prorata]].
5. The resulting quote breakdown is returned to the client over gRPC.
