# Components and data models

One page per significant component and core data model.

## Pages

- [Factors](/entities/factors.md) — Factors is the core actuarial lookup component located in src/main/kotlin/com/tidewell/rating/Factors.kt.
- [Domain Models (Model.kt)](/entities/model.md) — Model.kt defines the core domain representations and Data Transfer Objects (DTOs) used by rating-service to process pricing calculations, factor evaluations, and quote responses.
- [ProRata](/entities/prorata.md) — The ProRata component (src/main/kotlin/com/tidewell/rating/ProRata.kt) provides calculation utilities for determining partial-period premiums and pricing adjustments within rating-service.
- [RatingService](/entities/rating-service.md) — RatingService (src/main/kotlin/com/tidewell/rating/RatingService.kt) implements the gRPC interface defined in src/main/proto/rating.proto.
