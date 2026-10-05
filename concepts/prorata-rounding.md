---
type: Concept
title: Pro-Rata Premium Calculations and Rounding
description: Pro-rata premium calculations determine partial-period pricing adjustments, refunds, and additional premiums when policies undergo mid-term adjustments (MTAs) or cancellations.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/concepts/prorata-rounding.md
tags:
- rating-service
- concepts
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

# Pro-Rata Premium Calculations and Rounding

Pro-rata premium calculations determine partial-period pricing adjustments, refunds, and additional premiums when policies undergo mid-term adjustments (MTAs) or cancellations. 

The pro-rata calculation logic is housed in the `ProRata` component (`src/main/kotlin/com/tidewell/rating/ProRata.kt`), referenced in [[entities/prorata]].

## Overview and Use Cases

During the lifecycle of a home or motor insurance policy, changes to coverage terms, sum insured values, or policy status require adjustments to the annual premium calculated by [[concepts/pricing-calculation]]:
- **Mid-Term Adjustments (MTAs)**: Calculating the incremental difference or refund for the remaining active policy term when risk factors or cover options change.
- **Policy Cancellations**: Calculating the unearned premium refund proportional to the unused days remaining in the policy term.
- **Policy Renewals / Alterations**: Adjusting terms across partial billing periods.

## Rounding Strategy

To maintain financial parity across internal billing, core policy administration, and rating interfaces:
- Pro-rata adjustments are calculated using **half-up rounding** (`HALF_UP`).
- Rounding ensures deterministic monetary amounts across fractional day or period splits.

> *Note: Detailed internal methods and field signatures within `src/main/kotlin/com/tidewell/rating/ProRata.kt` could not be loaded directly from source. Refer to [[entities/prorata]] and [[summaries/api-spec]] for API-level pro-rata contracts and gRPC interface definitions.*

## Related Documentation
- [[entities/prorata]] – Component definition for `ProRata.kt`
- [[concepts/pricing-calculation]] – Full policy rating and base premium calculation engine
- [[entities/rating-service]] – gRPC service orchestration for rating requests
- [[index]] – High-level architecture overview of `rating-service`
