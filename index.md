---
okf_version: '0.2'
title: rating-service
description: rating-service is an internal calculation and pricing service for Tidewell's insurance products (including home and motor policies).
generated:
  at: '2026-10-05T12:44:23Z'
---

# rating-service

`rating-service` is an internal calculation and pricing service for Tidewell's insurance products (including home and motor policies). It is responsible for evaluating risk factors, calculating policy premiums, and handling pro-rata pricing adjustments for policy renewals and mid-term adjustments.

### Main Components
- **Factors (`Factors.kt`)**: Maintains the pricing factor tables (base rates, postcode bands, claims history, sum insured thresholds, and cover options) and computes premium multiplier lines based on incoming quote requests.
- **Rating Service (`RatingService.kt` / `rating.proto`)**: Exposes gRPC endpoints to allow core services (such as policy administration) to request real-time policy rating and pricing breakdowns.
- **Pro-Rata Calculator (`ProRata.kt`)**: Computes pro-rated premiums and adjustments for policy cancellations and mid-term amendments using half-up rounding.
- **Domain Model (`Model.kt`)**: Defines data representations for requests, multiplier lines, and rating responses.

### Data Flow & Operations
1. Pricing requests are received via gRPC from calling services with policy parameters (e.g. product type, postcode, claims history, sum insured, and selected cover options).
2. `Factors.lookup` maps the attributes against base rates and actuarial tables to determine risk multipliers.
3. The final premium and itemized line breakdowns are evaluated and returned to the caller.
4. Pro-rata calculations compute partial-period premiums during policy alterations.
5. Factor tables are updated manually on a monthly cadence based on pricing team spreadsheets.

### How to Run
The application is built with Kotlin and Gradle, exposing a gRPC interface defined in `src/main/proto/rating.proto`.

<!-- okf:contents -->

## Contents

- [Concepts and flows](/concepts/index.md) — 2 pages. Flows, lifecycles and cross-cutting mechanisms.
- [Architecture decisions](/decisions/index.md) — 2 pages. One ADR per architecture decision the code or documents make evident.
- [Components and data models](/entities/index.md) — 4 pages. One page per significant component and core data model.
- [Interfaces and references](/summaries/index.md) — 1 page. API, event and module references for the application.
- [Change log](/log.md) — every generation and sync, newest first.

<!-- /okf:contents -->
