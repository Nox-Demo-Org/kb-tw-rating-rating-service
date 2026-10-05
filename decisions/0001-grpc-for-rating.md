---
type: Architecture Decision
title: 'ADR 0001: Adoption of gRPC for Rating and Pricing Requests'
description: 'To serve high-volume rating requests efficiently, the service requires an inter-service communication protocol that ensures: - Fast serialization and low latency for real-time pricing queries.'
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/decisions/0001-grpc-for-rating.md
tags:
- rating-service
- decisions
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

# ADR 0001: Adoption of gRPC for Rating and Pricing Requests

## Status
Accepted

## Context
The [[index|rating-service]] acts as an internal calculation and pricing engine for Tidewell's insurance products, covering home and motor policies. Core upstream systems (such as policy administration) require real-time, low-latency evaluation of risk factors, policy premium calculations, and pro-rata pricing adjustments during policy issuance, renewals, and mid-term adjustments.

To serve high-volume rating requests efficiently, the service requires an inter-service communication protocol that ensures:
- Fast serialization and low latency for real-time pricing queries.
- Strict schema enforcement for complex input parameters (e.g., product type, postcode bands, claims history, sum insured thresholds, and cover options) and itemized line breakdowns.
- Consistent cross-language client code generation for calling services.

## Decision
Adopt **gRPC** over HTTP/2 with Protocol Buffers as the primary interface for [[entities/rating-service|rating-service]].

Key implementation details:
- Define the public service contract and data schemas in `src/main/proto/rating.proto` (see [[summaries/api-spec]]).
- Expose gRPC endpoints implemented in [[entities/rating-service|RatingService.kt]] to handle rating quotes, premium calculations, and pro-rata adjustments.
- Translate incoming gRPC payloads into internal domain representations defined in [[entities/model|Model.kt]] to be processed by [[entities/factors|Factors.kt]] and [[entities/prorata|ProRata.kt]].
- Support [[concepts/pricing-calculation|pricing calculations]] and [[concepts/prorata-rounding|pro-rata rounding]] natively through the generated service interfaces.

## Consequences
### Positive
- **Performance:** HTTP/2 multiplexing and binary Protobuf serialization reduce latency and network overhead during synchronous pricing queries.
- **Strong Typing & Contract Safety:** The schema defined in `rating.proto` provides a clear, versioned contract between rating-service and core policy systems.
- **Generated Client Stubs:** Automated stub generation streamlines integration for internal consumers across Tidewell's ecosystem.

### Trade-offs & Limitations
- **Tooling Requirements:** Calling services must support gRPC client tooling rather than standard JSON/REST interfaces.
- **Debuggability:** Binary payload inspection requires gRPC-aware debugging tools or reflection compared to plain text protocols.
