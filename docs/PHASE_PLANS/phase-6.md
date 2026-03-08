# Phase 6: Core Services & Interfaces Segment B Segment B

## Overview
Execute the second scoped segment of this workstream with deterministic outputs and validation.

## Why Now
Bulk handling and gateway services must precede PHASE7 Bulk+RBAC (dependency edge PHASE6 → PHASE7, risk: 0.2) to enable scalable API pagination (>50 threshold via config BulkThreshold=50) before RBAC scoping, and PHASE8 CI/CD+Docs (edge PHASE6 → PHASE8, risk: 0.1) for stable health checks in Helm/ArgoCD deploys. This unlocks deterministic core services before multi-tenant telemetry in PHASE9.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P6-T01 | Implement bulk handling for >50 items in `pkg/sonarqube/manager.go` (add `BulkSearch` methods: `BulkSearchProjects(ctx context.Context, query string, threshold int) ([]Project, error)`, `BulkSearchIssues(ctx, component string, threshold int) ([]Issue, error)` using SonarQube `/api/projects/search?p=1&ps=500` pagination loop until total < threshold or complete; integrate `config.BulkThreshold`; unit test edge cases >50, =50, <50 | Helm Operator | phase-5 audit pass (PHASE5 → PHASE6 edge, risk: 0.1) | `pkg/sonarqube/manager.go` + tests in `pkg/sonarqube/manager_test.go` + evidence | `go test ./pkg/sonarqube/... -v -run TestBulkSearch -cover` && `curl -f -H "Authorization: Basic $(echo -n $SONAR_TOKEN: | base64)" "$SONAR_URL/api/projects/search?ps=100" \| jq .total` |
| P6-T02 | Implement gateway registration/heartbeat/health checks in `internal/gateway/service.go` (add `RegisterTool(ctx, toolDef ToolDef)`, `Heartbeat(ctx) error`, `Healthz(w http.ResponseWriter)` with MCP protocol JSON-RPC calls to runtime; expose `/healthz` endpoint returning 200 JSON `{"status":"ok"}`; stub OTEL spans | Helm Operator | phase-5 audit pass (PHASE5 → PHASE6 edge, risk: 0.1) | `internal/gateway/service.go` + tests in `internal/gateway/service_test.go` + evidence | `go test ./internal/gateway/... -v -run TestRegisterTool -run TestHeartbeat -run TestHealthz` && `curl -f http://localhost:8080/healthz \| jq .status` |

## Phase-Specific Prompt
```text
You are executing Phase 6: Core Services & Interfaces Segment B Segment B.
Overview: Execute the second scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE7 Bulk+RBAC, PHASE8 CI/CD+Docs, PHASE9 MultiTenantTelemetry
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Output only phase-scoped changes with validation evidence and explicit assumptions (e.g., SonarQube API ps=500 max, BulkThreshold=50 from config).
Implement exactly in pkg/sonarqube/manager.go (BulkSearch funcs), internal/gateway/service.go (RegisterTool/Heartbeat/Healthz).
Record every key decision in UTC under WORK_NOTES.
```

## Phase-Specific Audit Prompt
```text
Audit Phase 6: Core Services & Interfaces Segment B Segment B against acceptance criteria, risk controls, and deterministic behavior.
Verify bulk pagination covers >50 items (TestBulkSearch), gateway endpoints return 200 (TestHealthz), dependency edges PHASE5→PHASE6/PHASE6→PHASE7 satisfied.
Return findings ordered by severity with explicit pass/fail gate decision and required remediations.
```

## Exit Criteria
- [ ] All Detailed Task List validation commands pass with >80% coverage (`go test ./... -coverprofile=coverage.out && go tool cover -func=coverage.out`)
- [ ] No errors from `go vet ./... && go mod tidy`
- [ ] Bulk handling tested against http://sonar.dev.gchinfo.com/ with SONAR_URL/SONAR_TOKEN envs (threshold=50 triggers pagination)
- [ ] Gateway healthz/heartbeat functional (`curl -f http://localhost:8080/healthz`)
- [ ] Phase-Specific Audit Prompt returns PASS
- [ ] No non-phase changes in git diff (scope drift check)

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | integration check fails | phase-audit-6 | reconcile contracts and retest | all phase validation commands pass |
| Scope drift | non-phase changes appear | phase-audit-6 | update PLAN + re-scope before proceeding | only phase-scoped deliverables remain |
| Quality regression | validation gate fails | release quality gate | patch and rerun full checks | clean quality signal |

## Estimated Swarm Duration
- 13h swarm time