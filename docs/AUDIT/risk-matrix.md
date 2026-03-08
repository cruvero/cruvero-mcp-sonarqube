# Risk Matrix

| Risk | Likelihood | Impact | Trigger | Mitigation | Gate | Owner |
|---|---|---|---|---|---|---|
| Phase 1: Repo cloning from cruvero-mcp-k8s@dev introduces structure mismatches (gateway/runtime/config dirs), blocking all downstream builds/deployments | Medium | High | File structure diff >5% or missing dirs (gateway, runtime, config) post-clone/adapt | git clone && diff -r cruvero-mcp-k8s@dev cruvero-mcp-sonarqube | grep -v k8s && go mod tidy -v && go build ./... | phase-audit-1 | Cruvero Plan Architect v2 |
| Phase 1A/2: Invalid SonarQube config envs (SONAR_URL, SONAR_TOKEN, BulkThreshold=50) cause manager init failures | Low | Medium | Env validation fails: curl -f -H "Authorization: Bearer $SONAR_TOKEN" $SONAR_URL/api/authentication/validate returns 401/404 | Validate with unit tests for env parsing; set BulkThreshold=50 in config | phase-audit-2 | Go Developer |
| Phase 2A/3: Incomplete tool impl (read/write for issues/hotspots) misses ACs | Medium | High | Missing endpoints in OpenAPI spec | go test -v -cover ./pkg/sonarqube/tools/... | phase-audit-3 | Go Developer |
| Phase 3A/4: Destructive tools (delete/bulk-delete) lack risk gates | High | High | BulkDelete called without confirm; no dry-run | Impl risk tiers/hints in tools; confirm prompts | phase-audit-4 | Go Developer |
| Phase 5: Gateway registration/heartbeat fails (TLS/OTEL) | Medium | Medium | SPIFFE cert mismatch; heartbeat timeout | Test TLS client/server; OTEL exporter fallback | phase-audit-5 | Go Developer |
| Phase 6: Helm chart misconfig/Vault secrets fail deploy | Low | High | helm lint fail; secret not injected | Fork k8s chart exactly; validate templates/values-dev.yaml | phase-audit-6 | Helm Operator |
| Phase 7: Bulk >50 overloads API without pagination | Medium | Medium | TestBulkUpdate(100) spans >10s or 429 | Use ps=500, paged search in manager | phase-audit-7 | Go Developer |
| Phase 8: CI/CD workflows fail doccov/audit gates | Low | Low | act -j ci syntax err; doc drift | Mirror k8s workflows; git diff -- docs/ pre-merge | phase-audit-8 | Helm Operator |
| Phase 9: Multi-tenant OTEL propagation drops traces | Medium | High | Tenant ctx lost in tools; exporter overload | OTEL propagation in manager/tools; sampling | phase-audit-9 | Go Developer |