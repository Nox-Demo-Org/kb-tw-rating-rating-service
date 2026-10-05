---
type: Component
title: ProRata
description: The ProRata component (src/main/kotlin/com/tidewell/rating/ProRata.kt) provides calculation utilities for determining partial-period premiums and pricing adjustments within rating-service.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/entities/prorata.md
tags:
- rating-service
- entities
sources:
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/kotlin/com/tidewell/rating/ProRata.kt
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

<!-- anchor: src/main/kotlin/com/tidewell/rating/ProRata.kt:L1-L1 -->

# ProRata

The `ProRata` component (`src/main/kotlin/com/tidewell/rating/ProRata.kt`) provides calculation utilities for determining partial-period premiums and pricing adjustments within `rating-service`.

## Responsibilities

- **Mid-Term Adjustments (MTAs)**: Calculates pro-rated premium adjustments when policy details, cover options, or parameters change mid-term.
- **Policy Cancellations**: Computes pro-rated refund or remaining premium balances when policies are cancelled prior to their natural expiry.
- **Partial-Period Pricing**: Evaluates time-apportioned premium values based on policy effective duration.
- **Rounding Parity**: Applies standard half-up rounding logic to ensure currency amounts maintain parity across downstream services (see [[concepts/prorata-rounding]]).

## Calculation and Rounding

`ProRata` handles apportioning annual or period premiums across partial terms. When computing adjustments:
1. The fraction of the active policy term is determined.
2. Premium adjustments are calculated against the baseline policy premium.
3. Currency amounts are rounded using half-up rounding to standard decimal precision.

For more information on the overarching pricing workflow and rounding behavior, see [[concepts/pricing-calculation]] and [[concepts/prorata-rounding]].

## Dependencies

- **[[entities/rating-service]]**: Invokes `ProRata` calculations to fulfill pro-rata adjustment endpoints exposed over gRPC (`rating.proto`).
- **[[entities/model]]**: Utilizes domain and request/response models representing pricing calculations and line breakdowns.
- **[[summaries/api-spec]]**: Defines the gRPC request and response contracts for pro-rata operations.

*(Note: Detailed internal signatures and method implementations within `src/main/kotlin/com/tidewell/rating/ProRata.kt` are subject to the underlying source implementation).*
