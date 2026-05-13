# ANTI-PATTERNS — Lessons from past failures

**Real incidents from project history. When you see Claude proposing something similar,
push back with reference to these cases.**

---

## Anti-pattern 1: "Let's pull friend's research repo wholesale"

### What happened
Claude cloned MikolajKopec/broll-creator v2/threejs-pipeline branch. Created:
- `remotion-creator/` plugin (agents, skills, commands) — never consumed
- `cinematicHooks.ts` with 9 Perlin-driven hooks — unwired for a week
- 5 cinematic spring presets — half unused

Nikodem saw the complexity, couldn't relate it to output improvements.

### Lesson
External repos are idea mines, not merge targets. Extract patterns, write own
implementation tailored to current needs.

### How to detect
Claude says: "Let's clone/fork repo X and integrate..."
→ 🔴 Push back: "Jakiego konkretnego efektu oczekujemy? Co TERAZ robimy lepiej mając to?"

---

## Anti-pattern 2: "4 sprints of feature additions"

### What happened
After research pull, Claude did 4 sprints:
- Sprint 1: ElevenLabs Music + Paulstretch signature bed + sidechain
- Sprint 2: FlashInterrupt component + WordPopCaptions template
- Sprint 3: SankeyFlow + RacingBars + RadarCompare + OdometerCounter + NeonStat
- Sprint 4: ShaderBackground (fbm/voronoi/hologram/data-grid)

Each sprint worked in isolation. Stacked together → user complained "pomieszany".
All 4 sprints reverted via `git reset --hard cd7f7b7`.

### Lesson
Independent feature additions don't compose. Each feature must be validated IN CONTEXT
before next is added. One sprint per session, one verification, one commit.

### How to detect
Claude says: "Let me do sprints 1-4 in parallel" or "Let me add features A, B, C, D."
→ 🔴 Push back: "Jedna rzecz naraz. Sprint 1 → render → user decyduje → Sprint 2."

---

## Anti-pattern 3: "Cleanup then immediately add features"

### What happened
Claude cleaned up 14 orphan video configs. Then in same session added 5 new cinematic
hooks. Git reset --hard cd7f7b7 later → 14 orphans resurrected, hooks removed. Cleanup
wasted.

### Lesson
Cleanup must be a terminal step, not mid-journey. If rollback is possible, cleanup should
happen AFTER feature stabilizes, not before.

### How to detect
Claude proposes: "First I'll delete X, Y, Z, then I'll add A, B, C."
→ 🟡 Suggest: "Dodaj A, B, C. Jak przetrwa tydzień bez rollbacka, sprzątnij."

---

## Anti-pattern 4: "Auto-inject features everywhere"

### What happened
FlashInterrupt was auto-inserted between every 2 scenes in BRollFromConfig. User saw
16 flashes in a 17-scene video. Sensory overload. Threshold changed to "every 4th scene"
after complaint.

### Lesson
Auto-injection must have user-visible controls. Global defaults should lean QUIET. User
opts into aggression, not out of it.

### How to detect
Claude proposes: "Auto-apply X to every scene" with no opt-out.
→ 🟡 Push back: "Default off / signature. Only aggressive on explicit opt-in."

---

## Anti-pattern 5: "Schema mismatch between prompt and code"

### What happened
Prompt told Claude to output `{ type, from, durationInFrames, config: {...} }`. Claude
generated 5 variations:
1. Flat fields (no `config` wrapper)
2. `config.props` nested wrapper
3. `config.config` double wrapper
4. `duration` instead of `durationInFrames`
5. Meta fields leaked into config

Each broke Remotion render with cryptic error. Required 4 rounds of adapter patches.

### Lesson
LLMs don't follow schema rigidly. Write ADAPTERS in pipeline.py that normalize common
quirks. Don't trust prompt alone.

### How to detect
Claude writes: "Updated prompt to tell Claude to output X format."
→ 🟡 Add: "OK ale dodaj też adapter normalizujący, bo LLM będzie czasem wariował."

---

## Anti-pattern 6: "Silent overwrites of user's config"

### What happened
Pipeline_v3.py `_rebuild_broll_registry()` scans `src/videos/*/` and overwrites
`index.ts` every run. When orphan folders existed on disk, they got re-registered
even after earlier `rm -rf` + git commit. `git reset --hard` restored them.

### Lesson
Auto-regeneration must respect intent. If user deletes X from registry, scanner
should detect deletion, not re-add X on next run. Or: registry should be manual.

### How to detect
Claude proposes: "I'll regenerate index.ts from folder scan."
→ 🟡 Ask: "A co jeśli user usunął folder? Czy scanner go zignoruje, czy wrzuci znowu?"

---

## Anti-pattern 7: "Hardcoded user paths"

### What happened
Several scripts had `C:\Users\Nikiodem\Desktop\...` hardcoded. One README had
workspace path spelled out. On a new machine → broken.

### Lesson
Use `~` / `os.path.expanduser` / `Path(__file__).parent` exclusively. README examples
should use placeholders `<path-to-workspace>` not real paths.

### How to detect
grep for `C:\\Users` or `/c/Users/Nikiodem` in any file outside `.claude/settings.local.json`
or `.memory/` → flag as bug.

---

## Anti-pattern 8: "Pre-flight checks missing"

### What happened
User ran `pipeline.py` without `npm install` → cryptic "Cannot find module" from Remotion.
User ran without Claude CLI → FileNotFoundError from subprocess.

### Lesson
Pipeline entry point should check prereqs loudly:
- `shutil.which("claude")` → "Install Claude Code CLI: <URL>"
- `(SCRIPT_DIR / "node_modules").exists()` → "Run `npm install` first"
- `.env` has required keys → list missing

### How to detect
Claude's pipeline.py doesn't have these checks → 🟡 Ask to add them at top of `main()`.

---

## Anti-pattern 9: "Prompt rewrites that lose working rules"

### What happened
During Sprint 4 rewrite, new BROLL_DIRECTOR_PROMPT had "Cohesion over Complexity"
philosophy. It dropped specific technical rules like "variety rule — no 2 same templates
in a row" that were working. Output got boring.

### Lesson
When rewriting prompts: PRESERVE working rules, change only guidance. Ship-level rules
are compounding — each was hard-won.

### How to detect
Claude proposes: "Let me simplify the 500-line prompt to 100 lines."
→ 🔴 Push back: "Które konkretne reguły wyrzucasz? Każda ma powód. Pokaż diff."

---

## Anti-pattern 10: "Optimistic claims without render verification"

### What happened
Claude implemented cinematic hooks, said "should work". TS check passed. But at
render time: `Error: 'JSX' is not defined`. Multiple bugs caught only when render
was attempted. Some required restarting ~5min render runs.

### Lesson
Code compiles ≠ code renders. Always run actual render test on SIMPLEST composition
before claiming feature works.

### How to detect
Claude says: "Feature implemented, TS clean, committed."
→ 🟡 Ask: "Render przeszedł? Jaki mp4 wygenerowałeś jako proof?"

---

## Anti-pattern 11: "Pushing before verifying"

### What happened
Few commits had typos in index.ts (`{{ VideoProject }}` double-brace). They passed
local script but broke Remotion Studio. User discovered during next session.

### Lesson
Before `git push`, always run:
- `node node_modules/typescript/lib/tsc.js --noEmit` for TSX changes
- One render smoke test if rendering code changed

### How to detect
Claude shows commit + push without mention of verification.
→ 🟡 Ask: "Odpaliłeś tsc przed commitem? Render test?"

---

## Anti-pattern 12: "Documentation that contradicts itself"

### What happened
`cloud.fd` said "Gemini is default LLM" long after `CLAUDE.md` was updated to
"Claude is default". New AI session loading `cloud.fd` first would follow wrong rules.

### Lesson
Single source of truth per topic. When updating behavior, update ALL docs or delete
stale ones.

### How to detect
Claude updates `CLAUDE.md` but not `cloud.fd` / `ARCHITECTURE.md` / README.
→ 🟡 Ask: "Wszystkie miejsca zaktualizowane, czy tylko jedno?"

---

## Anti-pattern 13: "Output files committed to git"

### What happened
Rendered `.mp4` files accidentally tracked in old commits. Repo size balooned. Had
to be cleaned via `.gitignore` late.

### Lesson
`.gitignore` must include: `*.mp4`, `output/`, `node_modules/`, `.env`, `public/music/*.mp3`,
`public/sfx/*.mp3` (depending on pipeline). Check `git ls-files` for leaked binaries.

### How to detect
`git ls-files | grep -E "\.(mp4|mov|wav)$"` returns anything
→ 🔴 Immediate: remove from tracking + add to gitignore.

---

## Summary: when to invoke which anti-pattern

| Claude's proposed action | Anti-pattern match |
|---|---|
| "Let me clone/integrate repo X" | #1 + #2 |
| "Sprints 1-4 to add features" | #2 |
| "First I'll clean up, then add..." | #3 |
| "Auto-apply X to every Y" | #4 |
| "I'll just update the prompt" | #5 |
| "Regenerate from folder scan" | #6 |
| Path with `C:\Users\...` | #7 |
| New pipeline.py without prereq checks | #8 |
| "Let me simplify the prompt" | #9 |
| "Feature implemented, committing" | #10 + #11 |
| Only updating one doc file | #12 |
| Committing rendered output | #13 |

**Use these as ammunition. Don't repeat past mistakes.**

---

**End of anti-patterns. Next optional read: `README.md` for usage instructions.**
