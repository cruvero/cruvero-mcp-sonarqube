# Phase 6: Helm+ArgoCD

## Overview
Bulk stubs, gateway health.

## Why Now
Precedes PHASE7 (risk 0.1).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P6-T01 | pkg/sonarqube/manager.go BulkSearch() pagination | Go Developer | phase-5 | Bulk methods | `go test ./pkg/sonarqube -run TestBulkSearch` |
| P6-T02 | internal/gateway/service.go health/heartbeat | Go Developer | P6-T01 | Service funcs | `go test ./internal/gateway` |