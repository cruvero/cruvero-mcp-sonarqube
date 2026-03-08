# Phase 4: Destructive+Polish

## Overview
Polish SonarManager multi-instance; destructive tools.

## Why Now
Precedes PHASE5 (risk 0.4).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P4-T01 | pkg/sonarqube/manager.go New() with instanceId/OTEL | Go Developer | phase-3 | Manager struct/tests | `go test -v ./pkg/sonarqube` |
| P4-T02 | Destructive: BulkDelete() with warnings | Go Developer | P4-T01 | Impl + tests | `go test -v -run TestDelete` |