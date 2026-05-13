# DECISION FRAMEWORK — Common scenarios with pre-computed recommendations

**For the N most likely scenarios, here are recommendations Nikodem expects. Use these
as baseline — deviate only if context demands.**

---

## Scenario 1: Claude proposes adding a new template type

### Questions to ask:
- Is the new template SUFFICIENTLY DIFFERENT from existing 6-10 templates in target pipeline?
- Does the current JSON schema cover it, or is new field needed?
- Does at least ONE scenariusz need this NOW?

### Default recommendation:
- 🟡 If template overlaps >60% with existing → push back, extend existing template.
- 🟢 If template is genuinely new visual pattern AND use case is real → approve, but:
  - Must add to `types.ts` with full typing
  - Must add auto-SFX mapping in `SoundLayer.tsx` (if pipeline has audio)
  - Must update `prompt.md` with example
  - Must update `docs/ARCHITECTURE.md` Mermaid

### Red flag:
- Proposing a template "for future use" → 🔴 reject. Ship on demand.

---

## Scenario 2: Claude proposes extracting shared code to `szhakal_core/`

### Questions to ask:
- How many places have near-identical code? (Must be ≥3)
- Do they drift currently? (Evidence of divergent bug fixes?)
- Is the shared code stable, or still evolving?

### Default recommendation:
- 🟡 If only 2 pipelines share: defer. Wait for 3rd duplication signal.
- 🟢 If 3+ pipelines have identical `claude_generate` / `extract_json` / adapter logic AND
  one has been fixed while others weren't → approve, but:
  - Must be tested standalone (importable without breaking existing pipelines).
  - Must not introduce new dependency install step.
  - Nikodem must understand new layer (document in CLAUDE.md).

### Red flag:
- Extracting "framework" before pattern stabilizes → 🔴 reject.

---

## Scenario 3: Claude proposes pulling external research / repo

### Questions to ask:
- Has Nikodem asked for this explicitly?
- Is there a CURRENT scenariusz that NEEDS this capability?
- What's the uninstall cost if it doesn't pan out?

### Default recommendation:
- 🔴 **Default is NO.** Past precedent: MikolajKopec pull → cinematic hooks unused, 4 sprints
  of sibling features, entire thing reverted.
- 🟡 Exception: Nikodem explicitly says "zajrzyj do repo X, chcę zobaczyć co mają" → OK to
  explore and summarize, but do NOT integrate without separate approval.

### When advising Nikodem on whether to pull:
"Nie polecam. Ostatni raz z MikolajKopec rozjechało nam 4 sprinty. Jak coś konkretnego
chcesz — weź pojedynczy komponent manualnie."

---

## Scenario 4: Claude proposes refactoring `pipeline_v3.py` (god object)

### Questions to ask:
- Is Nikodem actively adding features to this file?
- Does current bug/feature require understanding the whole 1164-line file?

### Default recommendation:
- 🟡 **If Nikodem isn't currently touching Faza 3 (AI Przebitki):** split into:
  - `pipeline_v3.py` (CLI entry only, ~100 lines)
  - `_claude.py` (claude_generate + retry + extract_json)
  - `faza_scenariusze.py` (Faza 1)
  - `faza_broll.py` (Faza 2 + registry rebuild)
  - `faza_przebitki.py` OR delete (move to `experimental/`)
- 🔴 Don't do it preemptively. Wait for a "I want to add X to pipeline" trigger.

### If approved, enforce:
- Each new file must have docstring explaining its role
- Imports stay clean (no circular)
- Entry point `pipeline_v3.py` CLI args unchanged
- All existing commands work after split (regression test)

---

## Scenario 5: Claude proposes adding audio to pipeline X

### Questions to ask:
- Is X lacking audio currently?
- Is the music style already defined, or does Claude need to invent one?

### Default recommendation:
- 🟢 If pipeline is audio-less and Nikodem wants it → approve. Follow pattern from
  DataStories/ViralShorts/CodeTutorials (already implemented):
  - Copy `generate_music.py` from similar pipeline
  - Adjust prompt style to match niche
  - Copy curated SFX subset to `public/sfx/`
  - Create `SoundLayer.tsx` with scene-type mappings
  - Add `sound` field to `types.ts`
  - Wire into main `*FromConfig.tsx`
- 🟡 If pipeline already has audio → only tweak presets, don't rebuild layer.

### Red flag:
- Adding ambient bed + signature bed + music + signatureBed + multiple SFX layers → 🔴
  Past precedent: Scenariusze ended up with 4 overlapping audio layers, user complained
  "pomieszany". Stick to: 1 music track + per-scene SFX.

---

## Scenario 6: Claude proposes touching `docs/ARCHITECTURE.md`

### Questions to ask:
- Is the change reactive (Claude just changed code) or proactive (no code change yet)?
- Does change match one of the 5 diagrams, or invent a 6th?

### Default recommendation:
- 🟢 Reactive update after structural code change → approve + verify Mermaid syntax:
  - `{name}` inside bracket labels → `&lt;name&gt;`
  - Diamond `{text}` with quotes → wrap outer `"..."`: `X{"tekst 'z' quotami"}`
- 🟡 Proactive "let's add a new diagram" → reject unless user asked.
- 🔴 Deleting existing diagram → reject.

### Verification:
Ask Claude: "Paste the Mermaid block to me. I want to eyeball it for syntax."
Look for: unmatched braces, single quotes inside diamond, reserved words.

---

## Scenario 7: Claude proposes deleting files

### Default recommendation:
- 🟢 **ORPHAN video configs** in `Scenariusze/broll-engine/src/videos/` → approve delete.
  (These have no matching Desktop folder.)
- 🟢 **Duplicate docs** (e.g. `Scenariusze/docs/SZHAKAL_README.md` copy of `SZHAKAL/README.md`)
  → approve delete.
- 🟢 **Never-consumed `remotion-creator/` plugin** → approve delete.
- 🟡 **`cloud.fd`** → approve delete ONLY after content merged into `CLAUDE.md` (verify
  merged before deletion).
- 🔴 **`.env` files** → NEVER delete. Never commit them either.
- 🔴 **`pipeline_v3.py`** itself → reject even if Claude plans to rename. User expects this name.

### Rule: Any delete must be preceded by verification Claude read the file and confirmed it's cruft.

---

## Scenario 8: Claude proposes a commit

### Checks:
- Message format: `<type>(<scope>): <subject>` ✓
- `Co-Authored-By: Claude Opus ...` trailer ✓
- No `.env`, no `.mp4`, no `output/`, no `node_modules/` in staged changes ✓
- `docs/ARCHITECTURE.md` updated if structural code changed ✓
- TS check passed for TSX changes ✓

If all ✓ → 🟢 approve.
If any ✗ → 🟡 ask Claude to fix before commit.

---

## Scenario 9: Nikodem asks "A czy B?"

### Your output:
```
Idź w B.

Bo: <1-sentence reason>

Czego unikać przy B: <gotcha>
```

Do NOT list pros/cons of both unless he explicitly asks. Nikodem decides fast.

### Meta-rule: When genuinely 50/50:
```
Praktycznie równe. Domyślnie wybrałbym <X> bo <tie-breaker>.
Ale jeśli Twoja intuicja mówi <Y> — nie jest źle.
```

---

## Scenario 10: Nikodem proposes something dumb

Example: "A może dodajmy Next.js frontend do tego?"

### Response:
```
🔴 Nie.

Projekt to pipeline generowania wideo, nie web app. Next.js dodałby:
- Kolejny stack do utrzymania
- Nowe bugi w deployment
- Brak use case — wszystko odpalasz z CLI

Jak chcesz GUI, Remotion Studio już masz: `npm start` w broll-engine/.
```

Be respectful but clear. Nikodem appreciates honesty.

---

## Quick reference — verdicts summary

| Scenario | Default verdict |
|---|---|
| New template (real use case, different) | 🟢 Approve with discipline |
| New template (speculative) | 🔴 Reject |
| Extract shared code (3+ dup) | 🟢 Approve |
| Extract shared code (2 dup) | 🟡 Defer |
| Pull external repo | 🔴 Reject (past failures) |
| Refactor god object (reactive) | 🟢 Approve with split plan |
| Refactor god object (preemptive) | 🟡 Defer |
| Add audio to silent pipeline | 🟢 Approve follow-pattern |
| Add 4th audio layer | 🔴 Reject (chaos) |
| Update ARCHITECTURE.md (reactive) | 🟢 Approve |
| Delete orphans | 🟢 Approve |
| Delete `cloud.fd` pre-merge | 🟡 Wait |
| Delete `.env` | 🔴 NEVER |
| Force push main | 🔴 Require explicit user approval |
| Big bang multi-phase refactor | 🔴 Reject, split into etaps |
| Add Next.js / React framework | 🔴 Reject |

---

**End of decision framework. Next read: `04_ANTI_PATTERNS.md`.**
