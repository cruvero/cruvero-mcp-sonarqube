# Phase 1: Core Services & Interfaces Segment A Segment A Segment A

## Overview
Execute the first scoped segment of this workstream with deterministic outputs and validation. This phase scaffolds the core MCP server structure by mirroring the cruvero-mcp-k8s@dev repository (excluding k8s-specific logic) and implementing basic config/RBAC stubs, establishing the gateway, runtime, and config packages required for all subsequent SonarQube integrations.

## Why Now
This phase must precede later phases to provide the foundational repository structure and interfaces. Specifically, it satisfies the dependency edge PHASE1 Scaffold+Config → PHASE1A SonarManager (risk: 0.1), enabling SonarQube manager auth/OTEL/logging setup without upstream refactoring. Without this scaffold, all tools (PHASE2+) would require parallel repo adaptations, increasing scope drift risk.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P1-T01 | Clone github.com/cruvero/cruvero-mcp-k8s@dev to github.com/cruvero/cruvero-mcp-sonarqube/main; adapt exact non-k8s structure: retain cmd/gateway/main.go (HTTP mux entrypoint), internal/runtime/server.go (OTEL-traced gRPC server), pkg/config/config.go (viper/env loader for SONAR_URL, SONAR_TOKEN, BulkThreshold); remove internal/k8s/*; add go.mod with github.com/sourcegraph/conc/v2 for bulk ops | Cruvero Plan Architect v2 | none | git repo at github.com/cruvero/cruvero-mcp-sonarqube with Makefile, go.mod, and listed files/commits | `go mod tidy && go test ./... -v -coverprofile=coverage.out && go tool cover -func=coverage.out \| grep total \| awk '{print $3}' \| cut -d'%' -f1 \| xargs test {} -ge 80 && go vet ./...` |
| P1-T02 | Implement multi-tenant/RBAC stubs in internal/rbac/policy.go: func CheckProjectPermission(ctx context.Context, projectKey, action string, instanceId string) (bool, error); stub returns true (TODO:RBAC); integrate into runtime/server.go as middleware; add project_policy.pb.go proto | Cruvero Plan Architect v2 | none | implementation + evidence (files: internal/rbac/policy.go, runtime/server.go updated, protos/project_policy.proto compiled) | `go test ./internal/rbac/... ./internal/runtime/... -v && go vet ./internal/rbac ./internal/runtime && curl -X POST http://localhost:8080/health` |

## Phase-Specific Prompt
```text
You are executing Phase 1: Core Services & Interfaces Segment A Segment A Segment A.
Overview: Execute the first scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE1 Scaffold+Config
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Output only phase-scoped changes with validation evidence and explicit assumptions.
Record every key decision in UTC under WORK_NOTES.
```

## Phase-Specific Audit Prompt
```text
Audit Phase 1: Core Services & Interfaces Segment A Segment A Segment A against acceptance criteria, risk controls, and deterministic behavior.
Return findings ordered by severity with explicit pass/fail gate decision and required remediations.
```

## Exit Criteria Checklist
- [ ] Repo cloned/adapted matches acceptance criteria #1 (structure: cmd/gateway, internal/runtime, pkg/config; no k8s remnants; git diff --name-only shows only phase files)
- [ ] All validation commands pass with >=80% coverage and zero vet errors
- [ ] RBAC stub integrated: go test ./internal/rbac/... reports 100% pass; health endpoint responds 200 OK
- [ ] Dependency edge satisfied: PHASE1 Scaffold+Config → PHASE1A SonarManager (risk: 0.1) – config.go parses SONAR_URL/SONAR_TOKEN envs successfully via unit test
- [ ] No scope drift: git log --oneline --since="7 hours ago" shows only P1-T01/P1-T02 commits
- [ ] Quality gates clean: `make lint` (or golangci-lint run) zero issues

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | integration check fails | phase-audit-1 | reconcile contracts and retest | all phase validation commands pass; PHASE1 → PHASE1A edge verifiable via config tests |
| Scope drift | non-phase changes appear | phase-audit-1 | update PLAN + re-scope before proceeding | only phase-scoped deliverables remain; exit checklist #5 confirmed |
| Quality regression | validation gate fails | release quality gate | patch and rerun full checks | clean quality signal; go test coverage >=80%, vet/lint zero errors |

## Estimated Swarm Duration
- 7h swarm time