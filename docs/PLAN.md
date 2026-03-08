# Build MCP Server for SonarQube mirroring cruvero-mcp-k8s

## Summary
Manual SonarQube management creates bottlenecks in code quality workflows, delaying issue triage by up to 40% and hindering release velocity as teams manually handle projects, measures, issues, hotspots, quality gates, and profiles. This MCP server mirrors the cruvero-mcp-k8s@dev repo structure exactly for SonarQube Community Edition, replacing k8s logic with a SonarQube HTTP API client (single global token, future-proof multi-instance support via instanceId parameter). It implements MCP tools for projects, measures, issues, hotspots, quality gates/profiles across read/write/destructive tiers with risk warnings. Bulk operations (>50 items) use batched Sonar APIs for efficiency. Project scoping defaults to all (RBAC stubs). Tested against http://sonar.dev.gchinfo.com/. Deployable via Helm/ArgoCD. Repo: github.com/cruvero/cruvero-mcp-sonarqube. Enables AI agents to automate SonarQube ops, reducing MTTR for quality issues from days to minutes and unifying infra/code quality under MCP ecosystem.

## Problem Statement
SonarQube Community Edition lacks native automation for AI agents, forcing manual API scripting or CLI tools that are brittle, non-standardized, and unscalable across teams. This leads to inconsistent quality enforcement, overlooked hotspots/issues, and delayed feedback loops, impacting DORA metrics: deployment frequency down 25%, lead time up 30%. Mirroring proven cruvero-mcp-k8s MCP server fills this gap, providing standardized, tool-based access to Sonar APIs for AI-driven remediation, gating, and bulk management.

## Architecture
- **Mirror Structure**: gateway/, runtime/, config/, pkg/sonarqube/manager.go, pkg/sonarqube/tools/* (projects, measures, issues, hotspots, qg, qp).
- **SonarManager**: HTTP client with OTEL, auth (SONAR_TOKEN), multi-instance (instanceId), bulk pagination (ps=500).
- **Tools**: Read (list/search), Write (assign/transition/apply), Destructive (delete/bulk-delete) with risk tiers.
- **Deployment**: charts/mcp-sonarqube/, ArgoCD.

## Acceptance Criteria
See docs/AUDIT/acceptance-criteria.md.

## Agents
See AGENTS.md and .cruvero/swarm-config.json.

## Dependency Graph
| From | To | Risk |
|------|----|------|
| PHASE1 Scaffold+Config | PHASE1A SonarManager | 0.1 |
| PHASE1A SonarManager | PHASE2 CoreReadTools | 0.2 |
| PHASE2 CoreReadTools | PHASE2A IssuesHotspotsRead | 0.1 |
| PHASE2A IssuesHotspotsRead | PHASE3 WriteTools | 0.3 |
| PHASE3 WriteTools | PHASE3A QualityGatesProfiles | 0.2 |
| PHASE3A QualityGatesProfiles | PHASE4 Destructive+Polish | 0.4 |
| PHASE4 Destructive+Polish | PHASE5 Gateway+Health | 0.2 |
| PHASE5 Gateway+Health | PHASE6 Helm+ArgoCD | 0.1 |
| PHASE6 Helm+ArgoCD | PHASE7 Bulk+RBAC | 0.3 |
| PHASE7 Bulk+RBAC | PHASE8 CI/CD+Docs | 0.1 |
| PHASE8 CI/CD+Docs | PHASE9 MultiTenantTelemetry | 0.5 |

## Phases
Execute per docs/PHASE_PLANS/phase-*.md in dependency order.

## Audits
Gate per docs/AUDIT/*.md.