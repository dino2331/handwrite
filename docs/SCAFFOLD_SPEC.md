# Codex One-shot Scaffold Spec (File-oriented)

This document defines the file-level skeleton Codex should generate for the offline PDHL app.

## Project tree (recommended fixed)

```text
app/src/main/java/com/pdrehab/handwritinglab/
  core/
  data/
  domain/
  ui/
  feature/taskrunner/
  feature/analysis/
  feature/export/
  feature/admin/
```

## Required assets
- `app/src/main/assets/protocol_v1_4_final.json`
- `app/src/main/assets/addresses_ko_12_nospace_100.json`

## Required top-level docs
- `README.md`
- `PRD.md`
- `docs/IMPLEMENTATION_APPENDIX.md`
- `docs/SUPPLEMENT_IMPLEMENTATION.md`
- `docs/SCAFFOLD_SPEC.md`

## Non-negotiable behavior checklist
- Participant mode accessible without admin login
- 8-char participant code validation + auto-create
- fixed assigned address per participant
- mandatory pressure/size calibration per session
- seed-based task randomization
- next button always visible and conditionally enabled for writing tasks
- task result screen after each task’s 2 trials
- histogram uses latest session of other participants only; hide when n<20
- aggregate metrics stored as `trialIndex=0`
- export must include zip + db + csv + xlsx
