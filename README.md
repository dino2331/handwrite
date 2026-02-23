# PDHL Spec Bundle (Offline, Local-only)

This repository currently contains a **spec-complete documentation bundle** for PDHL v1.4 FINAL so Codex can generate the app in one shot.

## Included sources of truth
- `PRD.md` (final product requirements)
- `app/src/main/assets/protocol_v1_4_final.json` (Appendix A exact protocol)
- `app/src/main/assets/addresses_ko_12_nospace_100.json` (Appendix B exact address pool)
- `docs/IMPLEMENTATION_APPENDIX.md` (DAO SQL + export implementation guidance)
- `docs/SUPPLEMENT_IMPLEMENTATION.md` (UI/taskrunner/storage clarifications)
- `docs/SCAFFOLD_SPEC.md` (file-level scaffold structure)

## Scope
- Fully offline Android tablet application
- No server/cloud integration
- Local file + Room DB architecture

## Storage conventions (target app behavior)
- Root: `files/pdhl/`
- DB: `files/pdhl/db/pdhl.db`
- Sessions: `files/pdhl/sessions/{sessionId}/...`
- Exports: `files/pdhl/exports/PDHL_export_{participantCode}_{yyyyMMdd}_{sessionId}.zip`
