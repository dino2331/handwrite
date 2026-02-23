# PRD: PDHL (Parkinson’s Digital Handwriting Lab) v1.4 FINAL — Spec-Complete

- Owner: BDIL
- Date: 2026-02-22
- Target: Android Tablet (Galaxy Tab + S-Pen), landscape only, fully offline
- UI language: Korean (`strings.xml`), internal code/keys in English

## Fixed Tech Stack
- Kotlin + AGP stable, Min SDK 26
- Jetpack Compose + Material3
- AndroidView Custom View for stylus capture (`MotionEvent` history + hover)
- MVVM + Clean-ish (`data/domain/ui`)
- Hilt, Coroutines/Flow, Room
- Storage: `files/pdhl/` + JSONL.gz
- Export: zip + `metrics_long.csv` + `summary.csv` + `summary.xlsx`

## Mandatory Build/Scope Rules
- `packageName`: `com.pdrehab.handwritinglab`
- single `app` module (internal package modularization)
- No server/cloud sync, no ad/tracking SDK
- No medical diagnosis/treatment claims in UI
- OCR/handwriting recognition model out-of-scope

## Participant/Admin
- Participant mode is no-login and must be directly accessible
- Participant ID: 8-char alnum uppercase (`^[A-Z0-9]{8}$`)
- Unknown ID auto-creates participant and assigns fixed address
- Admin PIN (`1234`) only gates export/debug, not tasks

## Core Interaction Rules
- Every task screen has fixed `GuideHeaderBar`
- Every task starts with required `InstructionOverlay`
- Ink default black; accent purple `#522B47`; header mint `#8DAA9D`
- Next button policy:
  - applies to T01~T08, T13~T15
  - always visible, disabled until condition
  - RIGHT_EDGE: recent pen-down x >= `0.92 * canvasWidth`
  - ALL_BOXES_12: 12 boxes all filled
  - click Next => clear canvas, `pageIndex++`, `countNextScreen++`, timer not reset

## Address Dataset Policy
- Source: `app/src/main/assets/addresses_ko_12_nospace_100.json`
- 100 addresses, 8 provinces diversified
- 12 chars fixed, no spaces, includes 3-digit number
- difficulty proxy fixed: `strokeCount = hangul*6 + digits*1`, all 57

## Result / History
- Show task result once after each task’s 2 trials
- Histogram population:
  - other participants only
  - latest session per participant only
  - task primary metric, aggregate (`trialIndex=0`) only
  - hide chart when `n < 20`
- History trend compares each session vs participant’s first session (delta)

## Export (Required)
Export zip must include:
- DB copy
- `metrics_long.csv`
- `summary.csv`
- `summary.xlsx`
- session raw/features/events/meta
- protocol/address assets copies

## Storage Layout
- Root: `files/pdhl/`
- DB: `files/pdhl/db/pdhl.db`
- Session: `files/pdhl/sessions/{sessionId}/...`
- Export: `files/pdhl/exports/PDHL_export_{participantCode}_{yyyyMMdd}_{sessionId}.zip`

## Data Model Highlights
- `ParticipantEntity`, `SessionEntity`, `TaskInstanceEntity`, `MetricValueEntity`, optional `TaskSummaryEntity`
- `MetricValueEntity` is single source for result/trend/export (including aggregate `trialIndex=0`)
- unique index: `(sessionId, taskId, trialIndex, metricKey)`

## Algorithms (Fixed)
- Stroke segmentation by DOWN/UP(CANCEL)
- Cluster segmentation with gap threshold and spatial/line-break rules
- `SIZE_REDUCTION_PCT` for writing tasks; `COMPLETION_TIME_MS` for TG3
- Micrographia active/confirmed in TG4 only
- Histogram: 10 bins, P5~P95 clamp, own-value marker

## Routes (Fixed)
`/splash`, `/participant/entry`, `/participant/home/{participantCode}`, `/session/new`,
`/calibration/pressure`, `/calibration/size`, `/task/run/{taskInstanceId}`,
`/task/result/{sessionId}/{taskId}`, `/session/summary/{sessionId}`,
`/participant/history/{participantCode}`, `/participant/history/session/{sessionId}`,
`/participant/history/trend/{participantCode}`, `/admin/login`, `/admin/tools`

## Migration
- Room version 4
- 1→2→3→4 with post-backfill (participantCode normalization, address assignment, metric backfill)

## Test DoD
- Unit tests >= 20
- Android smoke test >=1: participant create -> session -> calibrations -> T01 trials -> result screen

---

## Appendix A / B Source of Truth
- `app/src/main/assets/protocol_v1_4_final.json` (exact)
- `app/src/main/assets/addresses_ko_12_nospace_100.json` (exact)

