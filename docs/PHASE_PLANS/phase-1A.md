# Phase 1A: SonarManager

## Overview
Implement SonarQube manager with auth, OTEL, logging, multi-instance support.

## Why Now
Follows PHASE1 (risk 0.1); precedes PHASE2 (risk 0.2).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P1A-T01 | pkg/sonarqube/manager.go: New(ctx, config, instanceId?) with http.Client (token auth), OTEL tracer provider, slog | Go Developer | phase-1 | manager.go + tests | `go test -v ./pkg/sonarqube -run TestNew TestAuth` |
| P1A-T02 | internal/sonarqube/client.go: base DoAPI(path, params) with retries/backoff | Go Developer | P1A-T01 | client.go + mock tests | `go test -v ./pkg/sonarqube -run TestClient` |
| P1A-T03 | Integration smoke: validate http://sonar.dev.gchinfo.com/api/system_status | Go Developer | P1A-T02 | Integration test | `SONAR_URL=... go test integration` |