# Phase 3A: QualityGatesProfiles

## Overview
Implement read/write tools for quality gates and profiles.

## Why Now
Follows PHASE3 (risk 0.2); precedes PHASE4 (risk 0.4).

## Detailed Task List
| Task ID | Task | Owner/Agent | Depends On | Deliverable | Validation Command |
|---|---|---|---|---|---|
| P3A-T01 | pkg/sonarqube/tools/quality_gates.go: List(), Select(project, gate), Apply(project, gate) | Go Developer | phase-3 | quality_gates.go + risk tiers | `go test -v ./pkg/sonarqube/tools/quality_gates` |
| P3A-T02 | pkg/sonarqube/tools/quality_profiles.go: List(), Select(project, profile) | Go Developer | P3A-T01 | quality_profiles.go + tests | `go test -v ./pkg/sonarqube/tools/quality_profiles` |