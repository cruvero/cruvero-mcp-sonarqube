# Phase 2: CoreReadTools

## Overview
Implement projects/measures read tools.

## Why Now
Depends on PHASE1A (risk 0.2); precedes PHASE2A/PHASE3.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P2-T01 | Config envs in pkg/config/config.go | Go Developer | phase-1 | Load() func/tests | `go test -v ./pkg/config` |
| P2-T02 | pkg/sonarqube/tools/projects.go List() via /api/projects/search | Go Developer | P2-T01 | Tool + models | `go test -v ./pkg/sonarqube/tools/projects` |
| P2-T03 | pkg/sonarqube/tools/measures.go ComponentMeasures() | Go Developer | P2-T02 | Tool funcs | `go test -v ./pkg/sonarqube/tools/measures` |