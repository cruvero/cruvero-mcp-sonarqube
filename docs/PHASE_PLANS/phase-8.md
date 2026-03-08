# Phase 8: CI/CD+Docs

## Overview
Mirror CI/CD workflows, complete docs/phases.

## Why Now
Precedes PHASE9 (risk 0.1).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P8-T01 | .github/workflows/ci.yml mirror k8s; doccov | Helm Operator | phase-7 | Workflows | `act -j ci` |
| P8-T02 | Update all docs/AUDIT/PHASE_PLANS | Cruvero Plan Architect v2 | P8-T01 | Aligned docs | `git diff -- docs/` |