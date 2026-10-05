---
type: Interface Reference
title: API Specification
description: The rating-service exposes a gRPC interface defined in src/main/proto/rating.proto to support real-time quote rating, premium calculations, and pro-rata adjustments across Tidewell's insurance products.
resource: https://github.com/Nox-Demo-Org/kb-tw-rating-rating-service/blob/main/summaries/api-spec.md
tags:
- rating-service
- summaries
sources:
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/src/main/proto/rating.proto
- resource: https://github.com/Nox-Demo-Org/rating-service/blob/HEAD/docs/adr/0001-grpc-for-rating.md
generated:
  by: nox/gemini-3.7-flash
  at: '2026-10-05T12:44:23Z'
---

<!-- anchor: src/main/proto/rating.proto:L1-L1 -->
<!-- anchor: docs/adr/0001-grpc-for-rating.md:L1-L1 -->

# API Specification

The `rating-service` exposes a gRPC interface defined in `src/main/proto/rating.proto` to support real-time quote rating, premium calculations, and pro-rata adjustments across Tidewell's insurance products.

## Overview

The gRPC API serves as the primary integration point for internal callers (such as policy administration services) requiring pricing calculations for home and motor policies.

The service handles:
- Quoting and real-time policy premium calculations ([[concepts/pricing-calculation]]).
- Factor line breakdowns and risk multiplier evaluations ([[entities/factors]]).
- Pro-rata adjustments for policy renewals, mid-term alterations, and cancellations ([[entities/prorata]], [[concepts/prorata-rounding]]).

For details on the architectural decision to use gRPC for rating operations, refer to [[decisions/0001-grpc-for-rating]].

## Interface Definitions & Schemas

> **Note:** The underlying protobuf definitions in `src/main/proto/rating.proto` could not be loaded at the time of documentation generation. Exact RPC method names, request messages, response structures, and field tags are not documented here.

For service implementation and data model mappings:
- See [[entities/rating-service]] for the service orchestration implementation (`RatingService.kt`).
- See [[entities/model]] for the internal domain models and DTO structures (`Model.kt`).
- Return to the [[index]] for the high-level system overview.
