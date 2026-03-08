# Phase 3: Core Services & Interfaces Segment A Segment B

## Overview
Execute the second scoped segment of this workstream with deterministic outputs and validation.

**Why Now:** This phase must precede later phases (e.g., PHASE4 BulkOps via dependency edge PHASE3A QualityGatesProfiles → PHASE4 BulkTools risk: 0.2, and PHASE5 Integration via PHASE3 → PHASE5 risk: 0.3) because it completes write/destructive tools for issues/hotspots/QG/QP, enabling bulk ops (>50 threshold per acceptance criteria #4) and full RBAC/project scoping (criteria #7). It builds directly on PHASE2A IssuesHotspotsRead → PHASE3 WriteTools (risk: 0.3) read contracts in `internal/sonarqube/tools/read.go`.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P3-T01 | Implement PHASE2A IssuesHotspotsRead completion in `internal/sonarqube/tools/read.go` (ListIssuesBySeverity(), ListHotspotsByStatus()); PHASE3 WriteTools in `internal/sonarqube/tools/write.go` (AssignIssue(client *sonarqube.Manager, projectKey string, issueKey string, assignee string) error, TransitionHotspot(issueKey string, transition string) error, SelectQualityProfile(projectKey string, profileKey string) error, ApplyQualityGate(projectKey string, gateId string) error, BulkWrite(ctx context.Context, op string, items []interface{}) error); PHASE3A QualityGatesProfiles in `internal/sonarqube/tools/qgqp.go` (CreateQualityGate(name string) string, DeleteQualityGate(gateId string) error, BulkDeleteIssues(projectKey string, issueKeys []string) error); add risk tiers (mcp.RespondWithHint("WARN: Destructive op on production instance") for delete/bulk-delete) and register tools in `internal/runtime/tools.go` (tools.WriteTools()) | Go Developer | PHASE2A audit pass (edge PHASE2 CoreReadTools → PHASE2A risk: 0.1) | files `internal/sonarqube/tools/{write.go,qgqp.go}` + godoc examples + WORK_NOTES/phase3-decisions.md | `go test ./internal/sonarqube/tools/... -v -timeout 3m && go vet ./internal/sonarqube/tools/... && make lint` |
| P3-T02 | Unit/integration tests: mocks in `internal/sonarqube/tools/write_test.go` (testBulkWriteThreshold(51)), live tests vs http://sonar.dev.gchinfo.com/ in `internal/sonarqube/tools/e2e_test.go` (TestAssignIssueLive(t *testing.T), requires SONAR_TOKEN_TEST); coverage >90% on phase pkgs | Go Developer | P3-T01 | test files + coverage report in coverage.out | `SONAR_URL=http://sonar.dev.gchinfo.com SONAR_TOKEN=testtoken go test ./internal/sonarqube/tools/... -v -timeout 5m -coverprofile=coverage.out -coverpkg=./internal/sonarqube/... && go tool cover -func=coverage.out \| grep tools | [ $(awk '{sum+=$3} END {print sum/NR}' ) -gt 90 ]` |

## Phase-Specific Prompt
```text
You are executing Phase 3: Core Services & Interfaces Segment A Segment B.
Overview: Execute the second scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE2A IssuesHotspotsRead, PHASE3 WriteTools, PHASE3A QualityGatesProfiles
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Implement exact files/functions: internal/sonarqube/tools/write.go (AssignIssue, BulkWrite), qgqp.go (DeleteQualityGate), register in runtime/tools.go.
Output only phase-scoped changes with validation evidence (go test output) and explicit assumptions (e.g., BulkThreshold=50 from config.Envs).
Record every key decision in UTC under WORK_NOTES/phase3-decisions.md.
Test vs SONAR_URL=http://sonar.dev.gchinfo.com (non-destructive).
```

## Phase-Specific Audit Prompt
```text
Audit Phase 3: Core Services & Interfaces Segment A Segment B against acceptance criteria #3 (all tools read/write/destructive), #4 (bulk >50), risk controls (tiers/hints), and deterministic behavior (SonarManager contracts).
Verify dependency edges: PHASE2A → PHASE3 (read structs match write args), PHASE3 → PHASE3A.
Return findings ordered by severity with explicit pass/fail gate decision and required remediations (e.g., "FAIL: missing BulkWrite test").
```

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | SonarManager contracts in pkg/sonarqube/manager.go:Auth() fail write calls (edge PHASE2A → PHASE3 risk: 0.3) | phase-audit-3 | reconcile structs (e.g., Issue.Key type string) and retest | `go test ./internal/sonarqube/tools/write...` passes; edge validated |
| Scope drift | non-phase changes (e.g., bulk>50 impl or k8s remnants) appear | phase-audit-3 | update PLAN.md + re-scope before proceeding | git diff vs phase-2 shows only tools/write.go, qgqp.go, tests |
| Quality regression | godoc missing or coverage <90% | release quality gate | patch funcs/docs and rerun checks | `make lint && go tool cover -func=coverage.out \| grep -v "<90"` clean |

## Exit Criteria
- [ ] All Detailed Task List validation commands pass without errors
- [ ] Dependency edges confirmed: PHASE2A IssuesHotspotsRead → PHASE3 WriteTools, PHASE3 WriteTools → PHASE3A QualityGatesProfiles (structs match, tests link)
- [ ] Phase coverage >90% (`go tool cover -func=coverage.out`), no vet/lint warnings
- [ ] 100% non-destructive on dev Sonar (no real delete calls in e2e_test.go)
- [ ] Phase-Specific Audit Prompt returns PASS with no high-severity findings
- [ ] Acceptance criteria #3 (tools read/write/destructive with tiers) met for phase scope

## Estimated Swarm Duration
- 13h swarm time