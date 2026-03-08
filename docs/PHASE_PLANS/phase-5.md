# Phase 5: Gateway+Health

## Overview
Implement gateway service, registration, heartbeat, health checks.

## Why Now
Follows PHASE4 (risk 0.2); precedes PHASE6 (risk 0.1).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P5-T01 | cmd/gateway/main.go: MCP server mux (/mcp), tool registry | Go Developer | phase-4 | main.go + health endpoints | `go build ./cmd/gateway && ./gateway --health` |
| P5-T02 | internal/gateway/service.go: RegisterTools(), Heartbeat(), Healthz() with OTEL | Go Developer | P5-T01 | service.go + tests | `go test -v ./internal/gateway && curl -f localhost:8080/healthz` |