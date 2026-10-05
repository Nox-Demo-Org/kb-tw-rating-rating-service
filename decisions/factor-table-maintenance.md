---
type: Architecture Decision
title: Maintenance, Update Workflow, and Testing Strategy for Factor Tables
description: Actuarial models and risk calculations are maintained and revised by the pricing team using external spreadsheets.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/decisions/factor-table-maintenance.md
tags:
- rating-service
- decisions
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

# Maintenance, Update Workflow, and Testing Strategy for Factor Tables

## Status
Accepted

## Context
The pricing calculation engine in `rating-service` relies on factor multiplier lookup tables and base rates defined in `src/main/kotlin/com/tidewell/rating/Factors.kt` (see [[entities/factors]] and [[concepts/pricing-calculation]]). These factors represent actuarial pricing models for home and motor insurance products and include:
- Base rates (`BASE_RATE_PENCE`): `"home"` (28,000 pence) and `"motor"` (41,000 pence).
- Postcode bands (`POSTCODE_BANDS`): Area codes such as `"EC"`, `"M1"`, `"LS"`, `"EX"`, and `"TR"`.
- Claims history multipliers (`CLAIMS`): Claim counts from 0 to 3.
- Sum insured threshold: Fixed multiplier (1.25) when `sumInsuredPence > 50_000_000`.
- Optional covers (`COVER_OPTIONS`): Multipliers for `"accidental_damage"`, `"legal_cover"`, and `"courtesy_car"`.

Actuarial models and risk calculations are maintained and revised by the pricing team using external spreadsheets. The service requires an established workflow for keeping these in-code tables synchronized with business pricing updates.

## Decision
1. **Direct In-Code Factor Tables**: Factor tables and base rates are embedded directly into the codebase in `Factors.kt` as static `Map` constants rather than loaded dynamically from an external database or rules engine.
2. **Monthly Update Cadence**: The pricing factor tables are updated in source code roughly once a month when new actuarial pricing revisions are issued by the pricing team.
3. **Spreadsheet-Based Verification**: Verification of table updates and the `lookup()` method is performed manually by checking the calculated rates by hand against the pricing team's spreadsheets.
4. **No Automated Unit Tests**: In accordance with the current team process, there are no automated unit tests written for the `Factors` table definitions or the `lookup(req: PriceRequest)` method.

## Consequences
- **Zero Overhead for Dynamic Loading**: Keeping factors hardcoded in `Factors.kt` provides fast in-memory lookups during [[concepts/pricing-calculation]] without runtime external I/O dependencies.
- **Manual Verification Effort**: Every monthly update requires an engineer or pricing analyst to manually cross-reference quote outputs against spreadsheet calculations.
- **Regression Risk**: Because there are no automated test suites covering `Factors.kt` or `lookup()`, logic errors (e.g., changes to fallback defaults or key slicing logic like `req.postcode.take(2)`) must be caught entirely during manual review before deployment.
