# Phase 7: Bulk+RBAC

## Overview
Full bulk >50 impl, RBAC stubs (project_policy).

## Why Now
Depends on PHASE6 (risk 0.3); precedes PHASE8.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P7-T01 | BulkUpdate() in tools with threshold | Go Developer | phase-6 | Bulk funcs/tests | `go test -v -run TestBulkUpdate` |
| P7-T02 | pkg/rbac/policy.go CheckProject(project, action) | Go Developer | P7-T01 | RBAC stubs | `go test ./pkg/rbac` |