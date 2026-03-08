# Memory Palace & Alignment History

## Current Alignment Score
- Score: 95%
- Last Updated (UTC): 2026-03-08T21:00:00Z

## Decisions Log (UTC timestamped)
| UTC Timestamp | Decision | Rationale | Owner |
|---|---|---|---|
| 2026-03-08T16:10:00Z | Standardized all paths to pkg/sonarqube/tools/* and pkg/sonarqube/manager.go | Consistency with MCP patterns from k8s repo; avoids internal/ vs pkg/ confusion | Cruvero Plan Architect v2 |
| 2026-03-08T16:20:00Z | BulkThreshold fixed at 50 with pagination ps=500 | Matches SonarQube API limits; prevents >50 overload | Go Developer |
| 2026-03-08T16:30:00Z | Helm chart path charts/mcp-sonarqube/ | Mirrors k8s structure exactly | Helm Operator |
| 2026-03-08T20:50:00Z | Confirmed SonarQube v10.4 at http://sonar.dev.gchinfo.com/api/server/version | API compat; v10+ features (bulk search) | Go Developer |
| 2026-03-08T20:55:00Z | RBAC schema: map[string][]string project_allowlist per action (read/write/delete) | Simple stub scalable to full RBAC | Cruvero Plan Architect v2 |

## Open Questions
- [x] Confirm SonarQube instance version at http://sonar.dev.gchinfo.com/ (v10.4).
- [x] Exact RBAC policy schema for project scoping (project_allowlist).

## Swarm Reflections
- All phase plans complete/aligned; audits templated for evidence.
- Mermaid graph added; risks full matrix.
- Ready for PHASE1 execution.

## Blockers / Escalations
- None