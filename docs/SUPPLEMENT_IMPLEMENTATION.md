# PRD Supplement (UI/TaskRunner/Storage Clarification)

## 1) Session definition
- Participant identification = 8-char code entry.
- Session = one full task run (start from "과제하기" and ends after TG1~TG4).

## 2) Screen-level behavior
- `/splash`: storage/stylus checks + DB migration/backfill
- `/participant/entry`: uppercase normalize + auto-create participant if missing
- `/participant/home`: `과제하기`, `내 기록 보기`, small admin button
- `/session/new`: create session (seed auto, optional admin override)
- `/calibration/pressure`: 2-sec MVC, P95
- `/calibration/size`: write `강강강`, require >=3 clusters
- `/task/run/{taskInstanceId}`: required InstructionOverlay before capture
- `/task/result/{sessionId}/{taskId}`: primary metric + histogram (n>=20)
- `/participant/history/*`: list/session/trend with delta vs first session
- `/admin/*`: PIN gates export/debug only

## 3) TaskRunner state machine
`Idle -> Running -> Completed -> Saving -> Saved`
- No raw capture in Idle.
- Saving runs on IO dispatcher.
- On save failure: event log + continue with NA.

## 4) Guide geometry
- `TWO_HORIZONTAL_LINES`: y at 35%, 65% of canvas height
- `TOP_DOTS`: 12 dots at y=30%
- `BOXES_ROW_12`: centered 12 square boxes, box hit-test assigns `boxId`

## 5) Next button rules
- Always visible for writing tasks
- Per-page latch
- RIGHT_EDGE: latest down sample x >= 0.92W
- ALL_BOXES_12: all 12 boxes filled at least once
- On click: canvas clear + `pageIndex++` + `countNextScreen++`

## 6) Events log schema
`files/pdhl/sessions/{sessionId}/logs/events.jsonl`
- Includes `TASK_STARTED`, `TASK_ENDED`, `NEXT_PAGE_CLICKED`, feedback and export events.

## 7) Conflict resolution policy
If any prior note conflicts, this supplement follows **v1.4 FINAL**:
- participant mode is no-login
- box count for T04/T08 is fixed to 12
