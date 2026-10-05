# Architecture decisions

One ADR per architecture decision the code or documents make evident.

## Pages

- [ADR 0001: Adoption of gRPC for Rating and Pricing Requests](/decisions/0001-grpc-for-rating.md) — To serve high-volume rating requests efficiently, the service requires an inter-service communication protocol that ensures: - Fast serialization and low latency for real-time pricing queries.
- [Maintenance, Update Workflow, and Testing Strategy for Factor Tables](/decisions/factor-table-maintenance.md) — Actuarial models and risk calculations are maintained and revised by the pricing team using external spreadsheets.
