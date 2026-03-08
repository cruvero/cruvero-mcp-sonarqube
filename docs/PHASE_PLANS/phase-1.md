# Phase 1: Scaffold+Config

## Overview
Scaffold core MCP server by mirroring cruvero-mcp-k8s@dev (non-k8s), config/RBAC stubs, gateway/runtime/config pkgs.

## Why Now
Precedes PHASE1A (risk 0.1); avoids parallel refactors.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P1-T01 | Clone/Adapt repo; retain cmd/gateway/main.go, internal/runtime/server.go, pkg/config/config.go; remove k8s; add go.mod | Cruvero Plan Architect v2 | none | Repo with Makefile/go.mod | `go mod tidy && go test ./... -v -coverprofile=coverage.out` |