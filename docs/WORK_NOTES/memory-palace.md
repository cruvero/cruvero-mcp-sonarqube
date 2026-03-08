# Memory Palace & Alignment History

## Current Alignment Score
- Score: 80%
- Last Updated (UTC): 2026-03-08T17:00:00Z

## Decisions Log (UTC timestamped)
| UTC Timestamp | Decision | Rationale | Owner |
|---|---|---|---|
| 2026-03-08T16:10:00Z | Standardized all paths to pkg/sonarqube/tools/* and pkg/sonarqube/manager.go | Consistency with MCP patterns from k8s repo; avoids internal/ vs pkg/ confusion | Cruvero Plan Architect v2 |
| 2026-03-08T16:20:00Z | BulkThreshold fixed at 50 with pagination ps=500 | Matches SonarQube API limits; prevents >50 overload | Go Developer |
| 2026-03-08T16:30:00Z | Helm chart path charts/mcp-sonarqube/ | Mirrors k8s structure exactly | Helm Operator |

## Open Questions
- [ ] Confirm SonarQube instance version at http://sonar.dev.gchinfo.com/ (assume v10+)?
- [ ] Exact RBAC policy schema for project scoping?

## Swarm Reflections
- Phase docs now complete; audits ready for evidence.
- Path standardization reduces medium risk.

## Blockers / Escalations
- None