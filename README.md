# MedSync AI Screening

Claude plugin for MedSync AI clinical-trial screening. The clinician's own Claude does the reasoning; this plugin supplies the skills, the connection to the trials MCP server, and the trial wiki.

**Test project: synthetic patients only, no personal identifiers.**

## Install

    /plugin marketplace add dstiefe/medsync-ai-screening
    /plugin install medsync-ai-screening@medsync-ai-screening

The plugin connects to `https://trials.medsync-ai.com/mcp`. The first time, Claude opens a MedSync AI sign-in page: use your MedSync AI account. You must be a member of a trial to connect.

In claude.ai you may also need to add the connector yourself: Settings, Connectors, Add custom connector, name `MedSync AI Trials`, address as above.
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
