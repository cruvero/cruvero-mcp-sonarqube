# Phase 7: Bulk+RBAC

## Overview
Full bulk ops (>50 pagination), RBAC stubs (project_policy).

## Why Now
Follows PHASE6 (risk 0.3); precedes PHASE8 (risk 0.1).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P7-T01 | pkg/sonarqube/tools/*.go: BulkUpdate(keys[], action) with threshold check, paged if >50 | Go Developer | phase-6 | Bulk funcs in tools | `go test -v -run TestBulkUpdate ./pkg/sonarqube/tools` |
| P7-T02 | pkg/rbac/policy.go: CheckProject(project, action) stub (allow all, future allowlist) | Go Developer | P7-T01 | rbac.go + tests | `go test ./pkg/rbac` |
| P7-T03 | manager.go: BulkSearch(ps=500, paged) util | Go Developer | P7-T02 | Bulk util | `go test ./pkg/sonarqube -run TestBulkSearch` |