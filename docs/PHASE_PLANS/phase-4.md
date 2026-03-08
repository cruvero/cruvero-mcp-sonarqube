# Phase 4: Core Services & Interfaces Segment B Segment A Segment A

## Overview
**Why Now:** This phase polishes the SonarQube manager with multi-instance support (instanceId param) and establishes CI/CD pipelines, which must precede deployment phases (e.g., PHASE4 → PHASE5 Helm/ArgoCD Deploy, risk: 0.1 from dependency graph) and any production usage. It builds on PHASE3A QualityGatesProfiles → PHASE4 Destructive+Polish (risk: 0.2), ensuring stable auth/OTEL/logging for destructive ops like bulk-delete >50 items against http://sonar.dev.gchinfo.com/.

**Exit Criteria Checklist:**
- [ ] All `go test -cover ./...` suites pass with >80% coverage
- [ ] `go vet ./...` && `golangci-lint run` report zero issues
- [ ] Integration tests validate against `http://sonar.dev.gchinfo.com/` (SONAR_URL/SONAR_TOKEN envs)
- [ ] BulkThreshold=50 config validated in tests
- [ ] CI workflows pass locally (`act -j ci`), Helm charts lint/template cleanly
- [ ] Phase-audit passes with zero high-severity findings
- [ ] WORK_NOTES updated with UTC decisions; docs/PHASES.md phase 4 complete

Execute the first scoped segment of this workstream with deterministic outputs and validation.

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P4-T01 | Implement SonarQube manager polish in `runtime/internal/sonarqube/manager.go`: `struct SonarManager{client *http.Client, logger *zap.SugaredLogger, tracer trace.Tracer, baseURL string, instanceID string}`; `func New(baseURL, token, instanceID string) (*SonarManager, error)` with `otelhttp.NewTransport()` for spans, `zap.NewProduction()` logging, Basic Auth (`Authorization: Basic `+base64.StdEncoding.EncodeToString([]byte(token+":")))`; `func (m *SonarManager) Health(ctx context.Context) error`; multi-instance via `baseURL + "/api/v2?instanceId=" + m.instanceID`; tests in `manager_test.go` mock http.RoundTripper. Update `config/config.go` `SonarConfig{URL, Token, InstanceID string, BulkThreshold int=50}`. | Go Developer | PHASE3A QualityGatesProfiles → PHASE4 (risk: 0.2), phase-3 audit pass | `runtime/internal/sonarqube/manager.go`, `manager_test.go`, `config/config.go` updates; OTEL traces in `make otel-export` | `go test -v -cover ./runtime/internal/sonarqube/... && go vet ./runtime/internal/sonarqube/... && SONAR_URL=http://sonar.dev.gchinfo.com SONAR_TOKEN=sqpat_xxx go run cmd/healthcheck/main.go` |
| P4-T02 | Mirror CI/CD from cruvero-mcp-k8s: copy `.github/workflows/build-push.yml`, `release.yml`, `deploy-argocd.yml`; adapt goreleaser for `cruvero-mcp-sonarqube` (docker buildx, helm-push); add `charts/mcp-sonarqube/values.yaml` (sonar.url=$SONAR_URL, sonar.token=secret, bulkThreshold=50, rbac.stub=true); `argo/helmrelease-sonarqube-mcp.yaml`; complete `docs/PHASES.md` Phase 4 section, `README.md` deploy instr. | Helm Operator | P4-T01, phase-3 audit pass | `.github/workflows/*.yml`, `charts/mcp-sonarqube/`, `argo/*.yaml`, updated `docs/*.md` | `goreleaser build --snapshot --clean && act -j build && helm lint charts/mcp-sonarqube && helm template charts/mcp-sonarqube --set sonar.url=http://sonar.dev.gchinfo.com && make docs-lint` |

## Phase-Specific Prompt
```text
You are executing Phase 4: Core Services & Interfaces Segment B Segment A Segment A.
Overview: Execute the first scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE4 Destructive+Polish (manager multi-instance auth/OTEL/logging, CI/CD mirror).
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Implement exact files/functions/packages as in Detailed Task List (e.g., runtime/internal/sonarqube/manager.go New(), config/config.go SonarConfig).
Output only phase-scoped changes with validation evidence (go test output, curl responses) and explicit assumptions.
Record every key decision in UTC under WORK_NOTES.
Test against http://sonar.dev.gchinfo.com/ with SONAR_URL/SONAR_TOKEN/BulkThreshold=50.
```

## Phase-Specific Audit Prompt
```text
Audit Phase 4: Core Services & Interfaces Segment B Segment A Segment A against acceptance criteria (SonarQube manager multi-instance, CI/CD mirror), risk controls (PHASE3A→PHASE4 risk:0.2), dependency edges, and deterministic behavior (validation cmds pass).
Verify exit criteria checklist: test coverage, vet/lint clean, integration vs sonar.dev.gchinfo.com.
Return findings ordered by severity (high/medium/low) with explicit pass/fail gate decision and required remediations (e.g., fix manager auth if 401 curl fails).
```

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | PHASE3A QG tools fail to init SonarManager (e.g., instanceID missing); integration check fails | phase-audit-4 | Reconcile runtime/internal/sonarqube imports in tools/*; re-run `go test ./pkg/tools/...` | All phase validation commands pass; `go test ./...` integrates PHASE3A tools cleanly |
| Scope drift | Non-phase changes (e.g., new tools) appear; Helm deploys untested manager | phase-audit-4 | Git revert non-PHASE4; update PLAN.md + re-scope before proceeding | Only phase-scoped deliverables remain (git diff vs main); `helm template` matches config envs |
| Quality regression | OTEL spans missing; validation gate fails (test coverage <80%) | release quality gate | Patch manager.go spans/logger; rerun full checks incl. `make full-ci` | Clean quality signal: `go test -cover`, `golangci-lint run`, curl health 200; exit criteria checklist 100% |

## Estimated Swarm Duration
- 7h swarm time