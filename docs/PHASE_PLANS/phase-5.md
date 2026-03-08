# Phase 5: Gateway+Health

## Overview
Gateway service, health/heartbeat.

## Why Now
Precedes PHASE6 (risk 0.2).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P5-T01 | Fork charts/mcp-sonarqube/; values.yaml sonar envs | Helm Operator | phase-4 | Chart files | `helm lint charts/mcp-sonarqube` |
| P5-T02 | internal/gateway/service.go register/heartbeat/healthz | Go Developer | P5-T01 | Gateway funcs | `curl -f http://localhost:8080/healthz` |