# slackwater-tools — the modular machinery extracted from the Slackwater design repo

Extracted per the fleet's different-directions rule from
`SuperInstance/lucineer-system` branch `main` @ `7f6bdc1` (the Slackwater/Lucineer
design map room). The design docs, roundtable transcripts, deep-dives, and audio
assets stay archived on that branch; this repo carries only the **tools**, made
grabbable: clone and run.

**Verified before landing: 161/161 tests pass** (`pytest tests/ -q`, Python 3.14
local; CI runs 3.10/3.11/3.12). Security scrub on extraction: two hardcoded
DeepInfra keys (`v2_roundtable.py`, `cross_model_synthesis.py`) replaced with the
`loadkey` pattern. The leaked key was already dead (401) — rotated out on
2026-08-31 — but it does not travel forward with this code.

## The two kinds of tool here

### 1. Roundtable / ideation drivers — RUN them, never import them

These execute their whole job at module level (known footgun, by design: each is
a one-shot pipeline run, not a library):

| Script | What it does |
|---|---|
| `roundtable.py` | Consults 5 heavy models in parallel, each with a different strategic angle (Nemotron-Ultra systems / Gemini player-exp / Qwen3.7-Max implementation / Hermes brand-lore / Seed-2.0-Pro synthesis) |
| `v2_roundtable.py` | DeepInfra + MMX wide parallel roundtable on the expanded vision |
| `round2_ideation.py` | MOLT + game design + agent culture angles per model strength |
| `molt_ideation.py` | expand → ground → find research gaps → re-expand-with-constraints cycle |
| `ideation_loop.py` | 5-round generate → critique → synthesize → refine → pitch loop |
| `unification_roundtable.py` | unification pass over prior round outputs |
| `cross_model_synthesis.py` | models read each other's outputs, critique, produce a unified integration plan |
| `dramatic_personae.py` | dramatic character voice generation |
| `asset_pipeline.py` | concrete assets: sound-design spec (DeepSeek + MMX), Vectorize skills batch, Lucineer voice lines (Hermes) |
| `generate_hub.py` | generates the entire Slackwater Hub as `hub_build.json` build commands for the Lucineer CommandExecutor |

Drivers write outputs under `/home/eileen/projects/lucineer-system/...` by
default (their historical working dir). Point `BASE` elsewhere if you don't have
that checkout.

### 2. Libraries — import-safe, under test

| Module | What it does |
|---|---|
| `MOLT_REWARD_FUNCTION.py` | RL reward pipeline for Slackwater agent training. Measures craft, cooperation, continuity, conservation of cognitive energy. **Refuses to measure engagement traps, addiction metrics, attention extraction.** |
| `governor.py` | Governor Φ — shadow-mode flow-friction: computes Φ from perception tracks, logs, does not yet act |
| `energy_adapter.py` | reads player action cadence + idle time, computes what the system BPM *would* be; shadow-mode companion to slackwater-tempo |
| `lucineer/cns_telemetry.py` | single production emit path for USCP-v3 telemetry packets |

`tests/` covers the libraries (161 tests). `.github/workflows/ci.yml` runs them
on push/PR across Python 3.10–3.12.

### Prototypes & support

- `prototypes/bridge-protocol/src/HexLattice.lua` — hex-lattice prototype for the bridge protocol
- `loadkey.py` — DeepInfra key resolution: `mcp-deeinfra` .env first (long-standing
  directory typo, kept because tooling depends on it), `DEEPINFRA_API_KEY` env
  fallback, never crashes when the file is missing

## Provenance & doctrine

- Source tree: `SuperInstance/lucineer-system@7f6bdc1`, branch `main` (kept as a
  pure archive branch — the design corpus, 400k+ words of roundtable analysis,
  and audio assets live only there).
- Extraction + scrub + test verification: Lucineer, 2026-10-04, per Casey's
  different-directions order ("extract into the unique modular tools it
  contains").
- House law honored: keys read at use-time, never hardcoded; no branch deleted;
  the archive branch remains untouched.
