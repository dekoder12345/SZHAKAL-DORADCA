# WORKING MEMORY — Durable memory after Apr 19-20 2026 stabilization

Purpose: this file stores the latest durable memory from the long Codex <-> Claude
collaboration sprint so future sessions do not need to reconstruct it from chat logs.
It is not a verbatim transcript. It is the distilled memory, decisions, outcomes, and
operating rules that should survive session resets.

Read this after `04_ANTI_PATTERNS.md`.

---

## 1. What happened in this stabilization wave

The workspace went through a full audit, multiple Codex reviews, several Claude
implementation sprints, a clean-bootstrap test on fresh clones, an exit scan, and one
extra sprint that added scenario QA tools.

The important outcome is: the "fresh clone / portability / preflight / docs / hooks"
wave is DONE and verified. Do not re-open that work without a concrete regression.

After that stabilization wave, a second local feature wave started in `Scenariusze`
and partially across sibling repos:

- automatic metadata generation for finished videos
- ETAP 0 ingest of local project context (text/audio/video/images)
- `CLAUDE.md` rewrite to reflect the new architecture
- a new pending requirement for category-scoped testing (`--category`)
- later a third wave expanded `Scenariusze` further with Instagram ingest, integrated
  verify/simplify phases, animated hooks, and editor handoff packaging

---

## 2. Durable operating rule agreed in this session

Nikodem explicitly prefers closed sprints:

- If work is approved, safe, and has clear scope, do it NOW in the same session.
- Do not leave approved work as "future TODO" if it can be completed immediately.
- Sprint format should be: plan -> implement -> verify -> commit -> push.
- Use browser + logged-in accounts when that gives stronger verification.
- Stop only on hidden risk, unclear consequence, destructive action, or genuinely missing context.
- Use selective staging. Avoid `git add -A` in dirty repos.

This rule should shape future advice and implementation planning.

---

## 3. Codex decisions that changed Claude's original plan

Codex approved the general cleanup/portability direction, but pushed back on scope creep.

Key decisions:

- Keep cleanup + portability + docs + hooks.
- Do NOT make `_rebuild_broll_registry()` auto-delete folders.
- Do NOT do `pipeline_v3.py` split preemptively. Only split when actively touching that file.
- Do NOT extract `szhakal_core/` preemptively. Keep sibling repos standalone unless there is
  a concrete repeated bug/maintenance trigger.
- `.env.example` should be per repo, not one shared root file.
- `requirements.txt` should be self-contained per repo.
- `.githooks/` should be versioned; do not rely on local-only `.git/hooks/`.
- Env validation must be conditional. Example: Gemini/Higgsfield only when those flags are used.
- Preflight checks must happen before heavy imports / before confusing crashes.
- Fresh-clone proof matters more than "looks correct in code".

---

## 4. Completed sprints and outcomes

### Sprint A — Cleanup safe

Completed and pushed:

- 26 orphan folders removed from `Scenariusze/broll-engine/src/videos/`
- `remotion-creator/` removed
- duplicate docs removed
- `.memory/` ignored across repos
- registry pruned without teaching the scanner to auto-delete future folders

Net effect: the audit ghosts were removed, but deletion logic was NOT baked into the app.

### Sprint B — Portability + preflight + docs + hooks

Completed and pushed across all 4 repos:

- `requirements.txt` added per repo
- `.env.example` added per repo
- `_preflight.py` added / wired before heavy imports
- friendly checks for `claude`, `node_modules`, Python deps, and conditional env vars
- versioned `.githooks/pre-commit` added per repo
- README setup blocks updated

Important nuance:

- `ANTHROPIC_API_KEY` is NOT required for normal Claude CLI usage.
- In `Scenariusze`, Gemini/Higgsfield env checks are gated by flags.
- `Scenariusze` checks `broll-engine/node_modules`.
- sibling repos check their own `node_modules`.

### Sprint C — Clean-machine bootstrap proof

Claude tested fresh clones in `%TEMP%` and found two real portability issues:

1. `.memory/` was still tracked in some sibling repos
2. node_modules check was too weak and allowed fake / half-installed Remotion setups

Both were fixed and re-tested on second fresh clones.

Important durable conclusion:

- bootstrap is now proven on fresh remote state, not only local working trees
- preflight now checks for the actual Remotion CLI entry point, not just a directory

### Sprint D — Exit scan

Claude ran a final workspace scan and fixed remaining drift:

- ignored generated music artifacts in `Scenariusze`
- widened a TS type in `DataViz.tsx` to eliminate legacy composition errors
- documented optional `ELEVENLABS_API_KEY` usage for SFX helper flow in `Scenariusze`

Exit-scan conclusion: all four repos reached "safe to close session" status.

### Sprint E — Scenario QA helper tools

Added in `Scenariusze/`:

- `verify_scenario.py`
- `explain_plainly.py`
- `_scenario_utils.py`

These are OPTIONAL manual tools. They are NOT automatically wired into `pipeline_v3.py`.

They were tested on a real scenario about ASKAP J1424 and caught a real factual error
about linear vs circular polarization.

### Sprint F — Metadata generation across the workspace

Nikodem requested that every pipeline should also output:

- social media description with paragraphs and max 5 hashtags
- one YouTube SEO title
- YouTube tags as comma-separated tags (not hashtags)

Durable result:

- metadata generation now exists in all 4 repos
- sibling repos have `metadata_gen.py`
- pipelines expose metadata rerun/skip flags
- `Scenariusze` treats this as **Faza 4**
- `Scenariusze` also has standalone `generate_metadata.py`

This is now part of the intended final deliverable, not an optional future brainstorm.

### Sprint G — ETAP 0 context ingest in Scenariusze

Nikodem wanted local project folders to be scanned before scenario generation:

- read `.txt` / `.md` for context
- transcribe audio/video via OpenAI speech-to-text
- extract information from images
- write Polish context bundle into the project folder

Codex explicitly recommended:

- do NOT build a 5th pipeline repo
- add **ETAP 0** at the beginning of `Scenariusze`

Local code/docs state now shows:

- `context_ingest.py`
- ETAP 0 flags in `pipeline_v3.py`:
  - `--skip-context`
  - `--only-context`
  - `--refresh-context`
- OpenAI/ffmpeg requirements documented in `CLAUDE.md`
- `context/` artifacts with manifest/hash caching

This should be treated as an intentional architectural extension of `Scenariusze`.

### Sprint H — Local guide rewrite + doc catch-up

`Scenariusze/CLAUDE.md` was rewritten because the old file no longer matched reality.

The rewritten guide now describes:

- ETAP 0
- Faza 4 metadata
- standalone helpers
- setup commands and system deps
- protected local files
- pre-commit rules

This made `CLAUDE.md` much closer to the actual operating state of the repo.

### Sprint I — Pipeline expansion beyond the original 4 phases

By May 2026, the local `Scenariusze` pipeline had evolved further.

It now includes:

- `ETAP -1` — ingest/download/classification flow from desktop social inbox (`Tematy`, earlier
  referred to as IG inbox)
- `ETAP 0` — local context ingest
- `FAZA 1` — scenario generation
- `FAZA 1.5` — verify / fact-check / auto-fix support
- `FAZA 1.7` — simplify / ELI5 support
- `FAZA 2` — B-roll generation
- `FAZA 4` — metadata
- `FAZA 5` — animated hook generation
- `FAZA 6` — package for editor / handoff

This means the old mental model "Scenariusze = 3 fazy" is no longer accurate for current
local work.

---

## 5. Current verified workspace state

As of the end of this session:

- all 4 repos are synced with GitHub (`local HEAD == origin HEAD`)
- bootstrap / preflight / hooks / requirements / env examples are in place
- TypeScript checks pass on current app state after exit-scan fixes
- Remotion compositions bundle cleanly in all 4 repos
- fresh-clone bootstrap has been verified
- `verify_scenario.py` and `explain_plainly.py` exist and work in `Scenariusze`
- metadata generation exists across all 4 repos
- `Scenariusze` locally includes ETAP 0 context ingest before scenario generation
- `Scenariusze` now also locally includes integrated verify/simplify phases, hook generation,
  editor handoff packaging, and category-scoped execution

Do not frame the project as "fresh-clone broken" anymore. That was true during the audit,
but it is no longer the current state.

---

## 6. How scenario verification and simplification work

Important: these tools are MANUAL, not automatic.

Current behavior:

- generating a scenario does NOT automatically fact-check it
- generating a scenario does NOT automatically simplify difficult terms
- B-roll generation does NOT automatically wait for those tools unless explicitly asked

Correct recommended order when Nikodem wants safer output:

1. generate `scenariusz.txt`
2. run `verify_scenario.py`
3. run `explain_plainly.py`
4. show corrected / simplified outputs to Nikodem
5. only after explicit approval, update final `scenariusz.txt`
6. run B-roll pipeline

Metadata relation:

- metadata generation is intended to happen at the end of the pipeline
- if the scenario is corrected/simplified and then accepted, metadata should be based on the
  accepted final text, not the stale pre-correction version

Category-scoped relation:

- `Scenariusze` now has a real `--category` flag limiting all phases to one category
- do not treat `--category` as pending anymore; that drift was later closed

If fact-check finds problems:

- report is written to `reports/factcheck/<slug>.md`
- suggested corrected text is written to `reports/factcheck/<slug>_corrected.txt`
- original `scenariusz.txt` is NOT silently overwritten

If simplifier runs:

- report is written to `reports/plain/<slug>.md`
- simplified text is written to `reports/plain/<slug>_uproszczony.txt`
- original `scenariusz.txt` is NOT silently overwritten

---

## 7. Known local state that must NOT be reverted casually

There are pre-existing user local unstaged changes in:

- `Scenariusze/broll-engine/src/videos/pszczoly-iddychanie-pod-woda/`

These were explicitly called out as user state, not part of the stabilization/tooling sprints.
Future agents must not revert, stage, or "clean up" those changes unless Nikodem explicitly asks.

At this save point, there are also local non-user-guide/progress changes worth checking before
commits:

- `Scenariusze/CLAUDE.md`
- `Scenariusze/context_ingest.py`

Do not assume they are already synced with origin; verify git status first.

As of the May 13 snapshot, also assume `Scenariusze` is a live dirty worktree:

- branch `main` is ahead of `origin/main`
- there are many untracked generated `broll-engine/src/videos/*` directories
- there are many generated `reports/factcheck/*` and `reports/plain/*` outputs

Treat these carefully:

- some may be durable project artifacts
- some may be generated outputs that should be ignored, archived, or cleaned
- do NOT mass-stage or mass-delete them without classification

---

## 8. What remains deferred on purpose

These are NOT current blockers:

- splitting `pipeline_v3.py`
- extracting shared `szhakal_core/`
- deleting / merging `Scenariusze/cloud.fd`
- adding CI smoke workflows

There is one small practical next-step that is recommended and still pending:

- clean and reconcile the live dirty `Scenariusze` worktree before migration to a new laptop

Also note:

- the earlier docs/code drift around `--category` was later resolved; current local code exposes it
- root workspace docs (`SZHAKAL/README.md`, `PROJECT_MEMORY.md`) should be assumed stale until
  deliberately refreshed against the new `Scenariusze` reality

Default stance:

- do not create work for the sake of cleanliness
- if there is no concrete trigger, leave these deferred
- the only clearly safe optional next sprint identified in this session was lightweight CI smoke

---

## 9. Startup guidance for future sessions

When a new session starts:

- assume portability/preflight/hooks/docs are already done
- do not start with a broad re-audit unless the user asks
- check only what is relevant to the current task
- if task touches scenario quality, use the new helper scripts instead of inventing a new flow
- if task touches `Scenariusze`, remember the protected local `pszczoly-iddychanie-pod-woda` changes
- if task touches metadata, remember it now exists across all 4 repos
- if task touches ingest/context, remember ETAP 0 belongs in `Scenariusze`, not in a new repo
- if task is "test one category", use the real `--category` flag
- if task is "prepare migration/new laptop", prioritize docs/memory/git cleanliness over new features
- if task touches current `Scenariusze` git state, inspect ahead/untracked/generated outputs first

If Nikodem asks "can we do X?":

- prefer closed sprints
- prefer immediate execution for safe approved work
- keep scope narrow
- challenge preemptive refactors

---

## 10. Reference commits from this wave

Representative commits mentioned in the session:

- `d89b926` — Scenariusze cleanup safe
- `1791409` / `36b8a38` — Scenariusze preflight + docs/hooks
- `73fd1c6` — strict Remotion bootstrap check in Scenariusze
- `e40fa89` — exit-scan fixes in Scenariusze
- `05a4294` — scenario QA helper tools in Scenariusze

Later local wave (verify exact commits before citing publicly):

- metadata generation additions across repos
- ETAP 0 context ingest in `Scenariusze`
- `Scenariusze/CLAUDE.md` rewrite/catch-up
- `Scenariusze` later advanced with verify/simplify integration, IG/Tematy ingest, hook generation,
  editor handoff packaging, and category-scoped runs

Sibling repos also received matching portability/bootstrap/hook fixes during this wave.

---

For the exact Apr 21 follow-up reasoning, read `07_SESSION_LOG_2026-04-21.md`.
For the migration-era May snapshot, read `08_SESSION_LOG_2026-05-13.md`.

End of working memory.
