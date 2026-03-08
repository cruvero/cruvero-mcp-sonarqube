# Phase 2: Core Services & Interfaces Segment A Segment A Segment B

## Overview
Execute the second scoped segment of this workstream with deterministic outputs and validation.

**Why Now**: This phase implements PHASE2 CoreReadTools (projects and measures read tools), which directly depends on PHASE1A SonarManager → PHASE2 CoreReadTools (risk: 0.2). It must precede PHASE2A IssuesHotspotsRead → PHASE3 WriteTools (risk: 0.1 upstream) to provide foundational read interfaces for issues/hotspots, write operations, and Quality Gates/Profiling tools, enabling bulk ops and project scoping in later phases.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P2-T01 | Validate config envs (SONAR_URL, SONAR_TOKEN, BulkThreshold=50) in internal/config/config.go: Load() func parses envs with defaults | Go Developer | phase-1 audit pass | func Load(ctx context.Context) (*Config, error); unit tests | `go test -v ./internal/config -run TestLoad` |
| P2-T02 | Implement projects read tool in pkg/sonarqube/tools/projects.go: NewProjectsTool(m *sonarqube.Manager) *ProjectsTool; List(ctx context.Context, opts *SearchOpts) ([]*model.Project, *model.Paging, error) via /api/projects/search | Go Developer | P2-T01 | tool struct + List/SearchProjects funcs; model structs | `go test -v ./pkg/sonarqube/tools/projects -run TestListProjects && go vet ./pkg/sonarqube/tools/projects` |
| P2-T03 | Implement measures read tool in pkg/sonarqube/tools/measures.go: NewMeasuresTool(m *sonarqube.Manager) *MeasuresTool; ComponentMeasures(ctx, componentKey string, metricKeys []string) ([]*model.Measure, error) via /api/measures/component | Go Developer | P2-T02 | tool struct + ComponentMeasures/GetMeasures funcs; model structs | `go test -v ./pkg/sonarqube/tools/measures -run TestComponentMeasures && go test ./pkg/sonarqube/tools/... -cover` |
| P2-T04 | Integrate tools with SonarManager in internal/sonarqube/manager.go: embed tools in Manager (e.g., Projects() *ProjectsTool); add TestManagerTools integration | Go Developer | P2-T03 | Manager methods + e2e tests against http://sonar.dev.gchinfo.com/ | `SONAR_URL=http://sonar.dev.gchinfo.com SONAR_TOKEN=$TOKEN go test -v ./internal/sonarqube -run TestManagerProjectsList` |
| P2-T05 | Add tool interfaces in pkg/sonarqube/interfaces.go: ProjectsReader, MeasuresReader for future dependency injection | Go Developer | P2-T04 | interfaces + tool impls satisfaction | `go test -v ./pkg/sonarqube/... && go mod tidy && golangci-lint run ./pkg/sonarqube/...` |

## Phase-Specific Prompt
```text
You are executing Phase 2: Core Services & Interfaces Segment A Segment A Segment B.
Overview: Execute the second scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE1A SonarManager, PHASE2 CoreReadTools.
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Implement: projects tool (pkg/sonarqube/tools/projects.go: List via /api/projects/search), measures tool (pkg/sonarqube/tools/measures.go: ComponentMeasures via /api/measures/component), integrate via internal/sonarqube/manager.go: Manager.Projects(), config validation (internal/config/config.go).
Output only phase-scoped changes with validation evidence (go test outputs, coverage >80%) and explicit assumptions.
Record every key decision in UTC under WORK_NOTES.
Reference dependency: PHASE1A SonarManager → PHASE2 CoreReadTools (risk: 0.2).
```

## Phase-Specific Audit Prompt
```text
Audit Phase 2: Core Services & Interfaces Segment A Segment A Segment B against acceptance criteria, risk controls, and deterministic behavior.
Verify: PHASE2 CoreReadTools impl (projects/measures read funcs calling Sonar HTTP APIs), config envs (SONAR_URL/TOKEN/BulkThreshold), integration tests pass on http://sonar.dev.gchinfo.com/, no scope drift beyond PHASE1A/PHASE2.
Return findings ordered by severity with explicit pass/fail gate decision and required remediations.
Check dependency satisfaction: PHASE1A → PHASE2 (risk: 0.2).
```

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | SonarManager API changes break tools (e.g., /api/projects/search fail) | phase-audit-2 | reconcile contracts (internal/sonarqube/manager.go) and retest | all phase validation commands pass; PHASE1A → PHASE2 edge verified |
| Scope drift | non-phase changes (e.g., write tools or issues/hotspots) appear | phase-audit-2 | update PLAN + re-scope before proceeding | only phase-scoped deliverables (pkg/sonarqube/tools/projects, measures) remain |
| Quality regression | unit/integration tests fail or coverage <80% | release quality gate | patch funcs/tests and rerun full checks | clean quality signal: go test ./pkg/sonarqube/... -cover, go vet, golangci-lint |

## Exit Criteria Checklist
- [ ] All Detailed Task List validations pass (go test -v outputs, coverage >80%)
- [ ] Dependency edge PHASE1A SonarManager → PHASE2 CoreReadTools satisfied (risk: 0.2 verified via integration tests)
- [ ] No regressions: `make test && make lint && go test ./... -cover`
- [ ] Phase-Specific Audit Prompt returns explicit PASS with no high/medium severity findings
- [ ] Tools query test server successfully: `SONAR_TOKEN=$TOKEN curl "http://sonar.dev.gchinfo.com/api/projects/search?ps=1" | jq .paging.total > 0`
- [ ] Decisions logged in docs/WORK_NOTES/phase2-decisions.md

## Estimated Swarm Duration
- 8h swarm time