# SZHAKAL — Full Project Context for Decision-Support AI Agent

> **Purpose of this document:** You are about to advise Nikodem on decisions related to
> his SZHAKAL video pipeline workspace. This document is the cold-start context dump —
> everything you need to know about the project, its history, current state, constraints,
> and decision criteria. Read it entirely before giving strategic advice.
>
> **Last audit:** April 2026. Lines marked [AUDIT] reflect the most recent deep audit
> state. Lines marked [HISTORY] are chronological context.

---

## 1. TL;DR — What is SZHAKAL?

**SZHAKAL** is a Polish YouTube/TikTok/Reels/Shorts channel about technology, AI, and
science, run by **Nikodem** (GitHub: `dekoder12345`). He built a fully automated video
pipeline that takes text inputs and produces polished animated videos. Over 15+ sessions
the workspace grew from one pipeline to four, each specialized for a different video style.

**Everything runs locally on Windows (C:\Users\Nikiodem\Desktop\Claude Projekty\SZHAKAL\).**

---

## 2. Owner profile — Nikodem

- **Communication style:** Very direct, zero fluff, Polish language. Hates long summaries
  and recaps. Wants to see results, not explanations.
- **Technical level:** Comfortable with Python/TypeScript/Remotion/git at an intermediate
  level. Understands concepts but delegates implementation details to AI.
- **Preferences:**
  - Polish text in all video outputs. English OK in code comments/filenames.
  - Dark theme preferred for NEWSY (news) videos, light for INNOWACJE/CIEKAWOSTKI.
  - Apple-inspired clean aesthetic for non-news content.
  - NEVER use color `#FF8C00` (orange) — hard rule.
  - Prefers decisive recommendations over "here are 5 options, pick one."
- **Decision pattern:** Asks "A or B?" and picks fast. Don't propose more than 2-3 options.
- **Machine:** Windows 11 workstation. cp1250 encoding issues common in stdout (already
  handled in all pipelines via `sys.stdout.reconfigure(encoding='utf-8')`).

---

## 3. Project history — how we got here

### Phase 1: Single pipeline (Scenariusze)
Started as one Remotion-based B-Roll generator for tech news videos. Input: raw .txt on
Desktop. Output: horizontal split-screen B-Roll synced with talking-head video. Called
`pipeline_v3.py` (v3 was the surviving architecture after Kling/Imagen was replaced
with Veo 3.1 + Higgsfield Soul 2).

Key decisions made:
- [HISTORY] Gemini 2.5 Flash for JSON config generation (Faza 2).
- [HISTORY] Claude Code Max CLI for scenariusz generation (Faza 1) — free via subscription.
- [HISTORY] Higgsfield Soul 2 + Veo 3.1 for photorealistic AI Przebitki (Faza 3) — opt-in only.

### Phase 2: Dual H+V rendering + color schemes (Mar 2026)
- Added vertical 1080×1920 rendering in parallel to horizontal 1080×872, from SAME config.
- Templates use `useIsVertical()` hook — auto-adapt layout.
- Introduced 8 color schemes (blue/red/green/purple/cyan/amber/rose/mono) + dark/light themes.
- Polish text enforcement added to BROLL_DIRECTOR_PROMPT.

### Phase 3: Claude as default LLM + retry logic (Apr 2026)
- Switched JSON config generation from Gemini to Claude CLI. Gemini moved to `--use-gemini` fallback.
- Added retry loop for Gemini 429/503 (parses `retry_in Ns` from response).
- Claude produces stricter template rotation, better Polish, thematic SFX picks.

### Phase 4: MikolajKopec research pull + 4 sprints (Apr 2026)
- Pulled cinematic hooks spec from friend's repo (`MikolajKopec/broll-creator` v2/threejs-pipeline).
- Added: camera drift, parallax, breathing, light sweep, perlin motion + 5 cinematic spring presets.
- Ran 4 sprints adding: audio layer (ElevenLabs Music + Paulstretch signature bed + sidechain),
  flash interrupts + word-pop captions, sankey/racing/radar data viz, shader backgrounds.
- **Result was chaotic** — too many effects stacked, "pomieszany" feel. User asked to revert.
- Git reset --hard cd7f7b7 → removed all research-driven code. Kept only: H+V rendering,
  color schemes, Polish text, Claude default, retry logic.
- Side effect: ~25 orphan video configs in `broll-engine/src/videos/` got resurrected by the reset.

### Phase 5: 3 new sibling pipelines (Apr 2026)
User wanted diverse video styles without polluting main pipeline. Created:
- **DataStories** — clean data-driven (Apple style, 8 templates, Inter font)
- **CodeTutorials** — dark IDE tutorials (13 languages syntax highlight, 8 templates)
- **ViralShorts** — Hormozi/MrBeast vertical (7 templates, Anton display font, yellow highlight)

Each is a separate git repo on GitHub under `dekoder12345` org:
- `szhakal-scenariusze-broll-remotion` (was `szhakal-pipeline`, renamed)
- `szhakal-datastories`
- `szhakal-codetutorials`
- `szhakal-viralshorts`

### Phase 6: Audio in all 3 new pipelines (Apr 2026)
Each new pipeline got:
- Per-config ElevenLabs Music (corporate cinematic / lofi / trap per pipeline)
- Curated SFX copied from Scenariusze `public/sfx/` (16-21 files each)
- Scene-type auto SFX mapping in `SoundLayer.tsx`
- Sidechain ducking (music -40-50% under SFX hits)

### Phase 7: Architecture docs + auto-sync rule (Apr 2026)
- Created `SZHAKAL/ARCHITECTURE.md` with 5 Mermaid diagrams.
- Pushed to GitHub as `Scenariusze/docs/ARCHITECTURE.md` — renders natively on GitHub.
- Added `CLAUDE.md` in each pipeline with "auto-sync ARCHITECTURE.md on structural changes" rule.

### Phase 8: Full audit (current, Apr 2026)
Principal System Architect audit identified:
- ~25 orphan video configs need removal
- Missing `requirements.txt` / `.env.example` / prereq checks
- `pipeline_v3.py` is a 1164-line god object
- 3 new pipelines duplicate claude_client/music_gen/json_adapter code
- No global `.memory/` gitignore → Claude Code internal storage tracked in many places
- `cloud.fd` and `CLAUDE.md` split authority — should merge
- `remotion-creator/` plugin (from MikolajKopec pull) never consumed, still in repo

---

## 4. Current state of all 4 pipelines

### 4.1 Scenariusze (MAIN)
- **GitHub:** https://github.com/dekoder12345/szhakal-scenariusze-broll-remotion
- **Entry:** `pipeline_v3.py` (1164 lines — needs split)
- **Remotion project:** `broll-engine/` (subfolder)
- **Use case:** Tech news B-Roll alongside talking-head video
- **Dimensions:** 1080×872 (H) + 1080×1920 (V), dual render from same config
- **Templates:** 6 core (hero-reveal, code-reveal, comparison, data-viz, concept-map, timeline)
- **Audio:** SFX from `public/sfx/` (13 legacy WAV + 143 ElevenLabs MP3). No music layer in this pipeline.
- **Input scan:** Desktop folders `NEWSY/`, `INNOWACJE/`, `CIEKAWOSTKI/`, `TUTORIALE/`. Each
  subfolder needs `scenariusz.txt`. Pipeline finds it and renders to `{projekt}/broll/`.
- **Known:** 13 active Desktop projects (6 rendered, 7 awaiting scripts). Registry
  `src/videos/index.ts` lists 32 but 25 are orphans [AUDIT].

### 4.2 DataStories
- **GitHub:** https://github.com/dekoder12345/szhakal-datastories
- **Entry:** `pipeline.py`
- **Use case:** Data-driven standalone videos (yearly reports, rankings, stats)
- **Dimensions:** 1920×1080 default, optional `--vertical` 1080×1920
- **Templates:** 8 (intro, big-stat, bar-race, line-trend, breakdown-pie, comparison-bars, insight-text, outro)
- **Audio:** ElevenLabs corporate cinematic music + 16 SFX (stinger/impact/counter/click)
- **Input:** `.txt` or `.csv` file path as CLI arg
- **Verified:** End-to-end test `rynek_pracy_it.txt` → 4.3 MB 45s video

### 4.3 CodeTutorials
- **GitHub:** https://github.com/dekoder12345/szhakal-codetutorials
- **Entry:** `pipeline.py`
- **Use case:** Programming tutorials (async/await, refactoring, terminal demos)
- **Dimensions:** 1920×1080 default, optional `--vertical`
- **Templates:** 8 (lesson-title, code-static, code-typewriter, code-diff, terminal, narration, bullets, outro)
- **Languages supported:** typescript, javascript, python, rust, go, bash, sql, tsx, jsx (json/yaml/html/css have empty keyword lists — would need expansion)
- **Audio:** ElevenLabs lofi hip-hop + 18 SFX (ui-typing/clicks/bells). Lighter ducking than other pipelines.
- **Input:** `.md` lesson description
- **Verified:** End-to-end test `async_await.md` → 7.2 MB 90s video

### 4.4 ViralShorts
- **GitHub:** https://github.com/dekoder12345/szhakal-viralshorts
- **Entry:** `pipeline.py`
- **Use case:** Vertical Hormozi/MrBeast-style shorts without talking head
- **Dimensions:** 1080×1920 only (vertical-first)
- **Templates:** 7 (hook, captions, punch, big-number, vs-split, tip, cta)
- **Audio:** ElevenLabs trap/hip-hop 808 + 21 SFX (impact-boom/drop-bass/stinger-epic). Aggressive ducking.
- **Fonts:** Anton display (condensed black) + Inter 900
- **Background:** Always `#000` black
- **Accent:** `#fde047` yellow (Hormozi signature)
- **Verified:** End-to-end test `karaluch_fakty.txt` → 4.2 MB 28s video

---

## 5. Tech stack (set in stone — do not propose alternatives)

| Layer | Tool | Notes |
|---|---|---|
| LLM (JSON config) | Claude Code Max CLI (`claude.cmd -p`) | $0 via user's Max subscription. NEVER suggest Anthropic API direct. Gemini 2.5 Flash only as `--use-gemini` fallback in Scenariusze. |
| Renderer | Remotion 4.0.422 | React 19 + Tailwind v4. NO CSS transitions, NO Math.random (use sr()), NO Framer Motion, NO Tailwind animate-*/transition-*/duration-*. All animation via `useCurrentFrame` + `interpolate`/`spring`. |
| Music | ElevenLabs Music API (`POST /v1/music`) | $0.08/min at Creator tier. Per-pipeline style presets. Cached by md5(prompt) hash. |
| SFX | Curated .mp3 in `public/sfx/` | 143 ElevenLabs + 13 legacy WAV in main. 16-21 curated subset in each sibling pipeline. |
| Python | 3.13+ | Windows paths. cp1250 stdout fixed globally. |
| Node | Whatever ships with current npm | Remotion CLI invoked as `node node_modules/@remotion/cli/remotion-cli.js` (not `npx` — fails on spaces in path). |
| Fonts | @remotion/google-fonts | Inter (all), JetBrainsMono (CodeTutorials), Anton (ViralShorts). Loaded at render time — requires internet or cached. |

---

## 6. JSON config schema (shared pattern across all pipelines)

```
{
  "width": 1080,  // or 1920 for landscape
  "height": 872,  // or 1080 / 1920
  "fps": 30,
  "durationInFrames": <sum of scene durations>,
  "theme": "light" | "dark",  // pipeline-specific default
  "accentColor": "#...",
  "scenes": [
    {
      "type": "<template-name>",
      "from": <cumulative frame>,
      "durationInFrames": <frames>,
      "config": { ... template-specific ... }
    }
  ],
  "sound": {  // optional, not in Scenariusze
    "musicFile": "music/<slug>_<hash>.mp3",
    "musicVolume": 0.15-0.25,
    "masterVolume": 0.8-0.9,
    "disabled": false
  }
}
```

### Adapter quirks (LLM produces weird variations)
Pipeline.py in each new pipeline normalizes 5 known Claude quirks:
1. Flat scene fields (no `config` wrapper) → wraps them
2. `config.props` nested → merges with siblings
3. `config.config` double-wrapper → unwraps
4. `duration` / `lengthFrames` / `frames` aliases → renames to `durationInFrames`
5. Meta fields (`from`, `durationInFrames`) leaked into config → moves back to scene level

**If user reports render errors after Claude JSON generation, check these 5 cases first.**

---

## 7. Hard rules (NEVER violate)

1. All video text (titles, subtitles, labels, captions) in **POLISH**. Code source lines may be English.
2. NEVER color `#FF8C00` (orange) anywhere. `#f59e0b` amber is allowed as palette option.
3. NEVER `Math.random()` in TSX — use `sr()` (seeded random from `utils.ts`).
4. NEVER CSS transitions / Framer Motion / Tailwind `animate-*`/`transition-*`.
5. NEVER Sonnet for code generation — ALWAYS Opus 4+ (hardcoded rule in CLAUDE.md).
6. Scene `from` must equal cumulative sum of prior `durationInFrames` — no gaps, no overlaps.
7. Top-level `durationInFrames` must equal sum of all scene durations.

---

## 8. Repo conventions

- **Main branch:** `main` (Scenariusze), `master` (3 new pipelines — inconsistency, TODO unify)
- **Commit style:** `feat(X): ...`, `fix(X): ...`, `chore(X): ...`, `docs(X): ...` with trailing
  `Co-Authored-By: Claude Opus 4.6 (1M context) <noreply@anthropic.com>` (added by Claude).
- **Force push:** Only on personal-only repos, never force-push main unless user explicitly asks.
- **`.gitignore`:** Each pipeline ignores `node_modules/`, `output/`, `public/music/*`, `.memory/` (partially — see audit).

---

## 9. Known issues from audit (April 2026)

**CRITICAL (blocks fresh clone):**
- No `requirements.txt` anywhere — Python deps only in prose in CLAUDE.md
- No `.env.example` (only in unrelated `Animacje/` project)
- No Claude CLI prereq check — pipeline crashes cryptically if not installed
- Hardcoded `C:\Users\Nikiodem\Desktop` path in `SZHAKAL/README.md`

**HIGH (cleanup):**
- 25 orphan video configs in `Scenariusze/broll-engine/src/videos/` (ghosts from reset)
- `pipeline_v3.py` = 1164 lines god object (needs split into faza_*.py + _claude.py)
- `cloud.fd` outdated and duplicates CLAUDE.md — should merge
- `remotion-creator/` plugin never consumed — delete
- 3 new pipelines duplicate ~300 lines each of claude_client / music_gen / json_adapter

**MEDIUM (tech debt):**
- 7 unused R3F/Three.js packages in Scenariusze/broll-engine/package.json (~30-50MB)
- `.memory/` Claude Code internal storage appears in 4+ locations as untracked
- Scenariusze/docs/SZHAKAL_README.md duplicates SZHAKAL/README.md
- Branch naming inconsistency (`main` vs `master`)

---

## 10. Desktop folder structure (user's input source)

```
C:\Users\Nikiodem\Desktop\
├── NEWSY\                    ← active news content folders
├── INNOWACJE\                ← innovation content
├── CIEKAWOSTKI\              ← curiosities
├── TUTORIALE\                ← tutorials
├── Wspolprace\               ← sponsored content (e.g. "Golden Coil - Min Cyfryzacji")
└── Claude Projekty\
    └── SZHAKAL\              ← workspace root
        ├── README.md         ← workspace overview
        ├── ARCHITECTURE.md   ← 5 Mermaid diagrams
        ├── Scenariusze\      ← MAIN pipeline repo
        ├── DataStories\
        ├── CodeTutorials\
        └── ViralShorts\
```

Each Desktop/NEWSY (etc.) subfolder represents one video project. Expected structure:
- Source `.txt` (raw notes)
- `scenariusz.txt` (generated by Claude CLI from source)
- `broll/` (generated by pipeline)
  - `broll_config.json`
  - `video_broll.mp4`
  - `video_broll_vertical.mp4`
- `ai_renders/` (optional, only if `--with-przebitki`)

---

## 11. Decision criteria — how to advise Nikodem

When Nikodem asks for advice, prioritize:

1. **Simplicity over cleverness.** He prefers "just works" over elegant abstractions.
2. **Speed of iteration.** Short-commit-push-verify loop. No giant refactors unless he asks.
3. **Preserve what works.** If something renders correctly, don't touch it.
4. **Be decisive.** Pick A/B, don't present 5 options.
5. **Polish text in UI.** Claude's prompts may be English but video output must be Polish.
6. **Keep Claude CLI as LLM.** It's free for him. Never suggest paid APIs.

**Red flags to push back on:**
- Requests to add yet another pipeline (workspace already has 4 siblings — diminishing returns)
- Adding features to Scenariusze's god-object without splitting first
- Pulling upstream research that hasn't been validated in production (learned lesson from MikolajKopec sprint chaos)
- Over-engineering audio (too many layers caused "pomieszany" complaints)

**Green lights:**
- Cleanup/dead code removal
- Docs consolidation
- Dedup between 3 new pipelines (extract to `szhakal_core/`)
- Pre-flight validation in pipelines
- Making workspace bulletproof for fresh-clone on new machine
- Architecture diagram updates when things change

---

## 12. Where the living docs are

| File | Purpose | Authority level |
|---|---|---|
| `SZHAKAL/ARCHITECTURE.md` | 5 Mermaid diagrams — workspace flows | **PRIMARY** visual reference |
| `SZHAKAL/README.md` | User-facing quick start table | **PRIMARY** for "how to use" |
| `SZHAKAL/Scenariusze/CLAUDE.md` | AI agent instructions + rules | **PRIMARY** for AI sessions in Scenariusze |
| `SZHAKAL/Scenariusze/cloud.fd` | Old source of truth | OUTDATED — will be merged into CLAUDE.md |
| `SZHAKAL/{DataStories,CodeTutorials,ViralShorts}/CLAUDE.md` | Per-pipeline AI rules | PRIMARY for that pipeline |
| `SZHAKAL/{DataStories,CodeTutorials,ViralShorts}/prompt.md` | System prompt fed to Claude CLI | **CRITICAL** — changing it changes generation behavior |
| `SZHAKAL/{DataStories,CodeTutorials,ViralShorts}/README.md` | User quickstart per pipeline | PRIMARY for CLI usage |

---

## 13. Things AI agent should ALWAYS do

- Update `Scenariusze/docs/ARCHITECTURE.md` Mermaid when changing: template types, schema fields,
  shared services, adapter logic, folder structure. DO NOT update for bugfixes/renames/SFX swaps.
- Verify Mermaid GitHub syntax: `{name}` → `&lt;name&gt;`, diamond text with quotes → wrap outer `"..."`.
- Run TS check before committing TSX changes: `node node_modules/typescript/lib/tsc.js --noEmit`.
- Use relative paths in examples, not `C:\Users\Nikiodem\...`.
- Use Polish in user-facing video content. English OK in code/commits.

## 14. Things AI agent should NEVER do

- Suggest Anthropic API instead of Claude CLI.
- Suggest using `npx remotion render` (fails on paths with spaces — use `node node_modules/@remotion/cli/remotion-cli.js`).
- Propose adding R3F/Three.js to Scenariusze (removed by user, not wanted).
- Edit `cloud.fd` as source of truth — it's deprecated.
- Rename `pipeline_v3.py` to `pipeline.py` in Scenariusze (keeps version suffix for history).
- Force-push main branches without explicit user approval.
- Generate code without verifying it compiles.

---

## 15. Quick command reference

```bash
# Main pipeline — scan Desktop/NEWSY etc., generate scenariusz + B-Roll
cd "~/Desktop/Claude Projekty/SZHAKAL/Scenariusze"
python pipeline_v3.py                    # full: Faza 1 + 2
python pipeline_v3.py --only-broll       # just Faza 2
python pipeline_v3.py --use-gemini       # fallback LLM
python pipeline_v3.py --with-przebitki   # enable Faza 3 (rare)

# Standalone pipelines
cd "~/Desktop/Claude Projekty/SZHAKAL/DataStories"
python pipeline.py input.txt --target-sec 45 --vertical

cd "~/Desktop/Claude Projekty/SZHAKAL/CodeTutorials"
python pipeline.py lesson.md --target-sec 90 --vertical

cd "~/Desktop/Claude Projekty/SZHAKAL/ViralShorts"
python pipeline.py script.txt --target-sec 28
```

---

## 16. Current active decision points (as of audit)

Nikodem is currently deciding whether to:
- Run cleanup action plan (6 etapy) — delete orphans, add requirements.txt, split god-object,
  dedup 3 pipelines, consolidate docs, add git pre-commit hooks.
- Which etap priority? User will decide etap-by-etap or all-at-once.
- Whether to extract `szhakal_core/` shared lib (would require refactoring all 3 sibling
  pipelines + risk regression — high effort, medium payoff).

When Nikodem asks for recommendations on these, lean toward:
- **DO etap 1 (cleanup) first** — safe, high-impact, low risk.
- **DO etap 2 (portability)** — critical for fresh-machine test.
- **Defer etap 3 (god-object split)** — current code works, only split when actively modifying Scenariusze.
- **Defer etap 4 (dedup)** — convenient but introduces coupling risk between 3 otherwise-independent pipelines.
- **DO etap 5 (docs consolidation)** — `cloud.fd` vs `CLAUDE.md` duplication is active noise.
- **DO etap 6 (pre-commit hooks)** — prevents ARCHITECTURE.md drift.

---

**End of context. You now know everything needed to advise Nikodem effectively.**
