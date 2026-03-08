# Phase 6: Helm+ArgoCD

## Overview
Fork Helm chart, implement ArgoCD deploy.

## Why Now
Follows PHASE5 (risk 0.1); precedes PHASE7 (risk 0.3).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P6-T01 | charts/mcp-sonarqube/: fork from mcp-k8s, Chart.yaml name/version, values.yaml (SONAR_URL/TOKEN as vault refs, BulkThreshold=50) | Helm Operator | phase-5 | Chart files + values-dev.yaml | `helm lint charts/mcp-sonarqube && helm template . --values values-dev.yaml | kubeval` |
| P6-T02 | argocd/applications/mcp-sonarqube-app.yaml: Application spec (repo, namespace mcp-sonarqube, target main) | Helm Operator | P6-T01 | ArgoCD yaml | `argo-app lint mcp-sonarqube-app.yaml` |