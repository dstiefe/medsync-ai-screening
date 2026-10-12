# medsync-ai-screening

Claude plugin for clinical-trial screening. The clinician's own Claude does the reasoning; this plugin supplies the skills, the connection to the trials MCP server, and the trial wiki.

**Test project: synthetic patients only, no personal identifiers.**

## Install

    /plugin marketplace add dstiefe/medsync-ai-screening
    /plugin install medsync-ai-screening@medsync-ai-screening

Point it at your trials server by setting `MEDSYNC_TRIALS_URL` (default `http://127.0.0.1:8000/mcp/trials`).
This repo holds only the plugin: no protocol text, no patient data, no credentials.

## Layout

| Path | What |
|---|---|
| `.claude-plugin/plugin.json` | Plugin manifest |
| `.mcp.json` | Points Claude at the trials MCP server (localhost for now) |
| `skills/new-screening` | Exclusions first, then inclusions, ask for gaps, record result |
| `skills/ask-about-trial` | Answer protocol questions from the wiki |
| `skills/my-trials` | List the clinician's trials |
| `skills/my-patients` | List patients, show one screening, confirm a completed one |

The server and the trial data live in a separate project (`trials-mcp-server`); this repo is only the plugin.

## Status

- [x] Skeleton, three skills
- [x] Trials server built separately; database schema, EVEREST criteria and wiki loaded
- [x] `record_screening`, patient tables, `new-screening` skill that checks every criterion (server-enforced)
- [ ] Real login (OAuth) so the server knows who is calling
- [ ] Test on the EVEREST SCR-TEST-01 sample documents
