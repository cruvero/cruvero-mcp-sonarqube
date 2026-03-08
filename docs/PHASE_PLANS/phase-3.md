# Phase 3: WriteTools

## Overview
Complete PHASE2A read (issues/hotspots), PHASE3 write, PHASE3A QG/QP.

## Why Now
Precedes PHASE4 (risk 0.3); enables bulk/RBAC.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P3-T01 | pkg/sonarqube/tools/issues.go ListIssues(), hotspots.go ListHotspots(); write.go Assign/Transition; qgqp.go Create/Delete | Go Developer | phase-2 | Tool files + risk tiers | `go test -v ./pkg/sonarqube/tools/...` |