# Phase 5: Core Services & Interfaces Segment B Segment A Segment B

## Overview
Execute the second scoped segment of this workstream with deterministic outputs and validation.

**Why Now:** This phase delivers the Gateway service with health endpoints and a deployable Helm chart, which must precede end-to-end integration testing, ArgoCD rollout, and production deployment phases. It establishes the runtime interface layer required for tools like SonarManager to expose HTTP APIs securely. References dependency edges: PHASE4 → PHASE5 Gateway+Health (risk: 0.2), PHASE5 → PHASE6 Helm+ArgoCD (risk: 0.1).

**Exit Criteria Checklist:**
- [ ] `go test ./gateway/... -v -cover` passes with >80% coverage and no failures
- [ ] `helm lint ./helm/cruvero-mcp-sonarqube` succeeds without warnings
- [ ] `helm template ./helm/cruvero-mcp-sonarqube --values values-dev.yaml | kubeval --strict` validates cleanly
- [ ] Local Gateway runs: `go run ./cmd/gateway/main.go` and `curl -f http://localhost:8080/healthz` returns 200 OK
- [ ] Vault secrets render correctly: `helm template ... | grep SONAR_TOKEN` shows vaultPath reference
- [ ] Phase audit passes with no high-severity findings
- [ ] All config envs (SONAR_URL, SONAR_TOKEN) injected via Helm values and validated in Gateway config pkg

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P5-T01 | Fork Helm chart from cruvero-mcp-k8s/charts to ./helm/cruvero-mcp-sonarqube; update Chart.yaml (name: cruvero-mcp-sonarqube, version: 0.1.0); add values.yaml with sonar.url=http://sonar.dev.gchinfo.com, sonar.token={{ vaultPath }}, bulkThreshold=50; template Deployment with envFrom vaultSecret for runtime/config; implement Gateway in ./cmd/gateway/main.go using gateway pkg (github.com/cruvero/cruvero-mcp-sonarqube/gateway) with /healthz endpoint calling runtime.Health() from ./runtime/health.go; add Makefile targets for helm-test | Helm Operator, Go Developer | phase-4 audit pass, PHASE4 → PHASE5 (risk: 0.2) | ./helm/cruvero-mcp-sonarqube/Chart.yaml, ./cmd/gateway/main.go, ./gateway/health.go, Makefile with `helm-lint helm-template-test go-test-gateway`; deploy evidence (dry-run logs) | `make helm-lint && make helm-template-test && go test ./gateway/... -v && curl -f http://localhost:8080/healthz \| jq .status` |

## Phase-Specific Prompt
```text
You are executing Phase 5: Core Services & Interfaces Segment B Segment A Segment B.
Overview: Execute the second scoped segment of this workstream with deterministic outputs and validation.
Scope nodes: PHASE5 Gateway+Health, PHASE6 Helm+ArgoCD
Required reading: docs/PLAN.md, docs/AUDIT/risk-matrix.md, docs/WORK_NOTES/memory-palace.md.
Output only phase-scoped changes: ./helm/cruvero-mcp-sonarqube/*, ./cmd/gateway/*, ./gateway/*, ./runtime/health.go, Makefile updates; include validation evidence (command outputs, curl responses) and explicit assumptions (e.g., Vault provider=aws-secretsmanager).
Record every key decision in UTC under WORK_NOTES.
```

## Phase-Specific Audit Prompt
```text
Audit Phase 5: Core Services & Interfaces Segment B Segment A Segment B against acceptance criteria, risk controls, and deterministic behavior.
Verify: Helm chart structure mirrors cruvero-mcp-k8s non-k8s parts; Gateway /healthz checks SonarManager connectivity (ping SONAR_URL); Vault secrets for SONAR_TOKEN; bulkThreshold env; RBAC stubs in gateway middleware.
Return findings ordered by severity with explicit pass/fail gate decision and required remediations.
```

## Risks / Gates
| Risk | Trigger | Gate | Mitigation | Success Criteria |
|---|---|---|---|---|
| Dependency mismatch | integration check fails | phase-audit-5 | reconcile contracts and retest | all phase validation commands pass |
| Scope drift | non-phase changes appear | phase-audit-5 | update PLAN + re-scope before proceeding | only phase-scoped deliverables remain |
| Quality regression | validation gate fails | release quality gate | patch and rerun full checks | clean quality signal |

## Estimated Swarm Duration
- 9h swarm time
```