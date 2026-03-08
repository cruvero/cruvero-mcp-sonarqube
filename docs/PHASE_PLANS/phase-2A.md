# Phase 2A: IssuesHotspotsRead

## Overview
Implement read tools for issues and hotspots (list/search by project/branch).

## Why Now
Follows PHASE2 (risk 0.1); precedes PHASE3 (risk 0.3).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P2A-T01 | pkg/sonarqube/tools/issues.go: List(project, branch?, types?, statuses?) via api/issues/search | Go Developer | phase-2 | issues.go + models/tests | `go test -v ./pkg/sonarqube/tools/issues` |
| P2A-T02 | pkg/sonarqube/tools/hotspots.go: List(project, branch?) via api/hotspots/search | Go Developer | P2A-T01 | hotspots.go + tests | `go test -v ./pkg/sonarqube/tools/hotspots` |