# NEW LAPTOP BOOTSTRAP — SZHAKAL + Doradca

Purpose: exact target layout and order of operations for moving the setup to a new laptop.

---

## 1. Target folder layout on Desktop

Create:

```text
Desktop/
├── Codex Projekty/
│   └── Doradcy/
│       └── SZHAKAL-DORADCA/
└── Claude Projekty/
    └── SZHAKAL/
```

Meaning:

- `SZHAKAL-DORADCA/` = this advisory/context repo for Codex
- `SZHAKAL/` = root folder containing the real execution repos used with Claude

## 2. Clone order

### First: advisory repo for Codex

Clone the advisory repo into:

```text
Desktop/Codex Projekty/Doradcy/SZHAKAL-DORADCA/
```

This repo should contain:

- role files
- project context
- advisory rules
- decision framework
- anti-patterns
- working memory
- session logs
- migration/checklist docs
- Claude handoff prompt

### Second: real execution repos for Claude

Clone the real repos under:

```text
Desktop/Claude Projekty/SZHAKAL/
```

Expected repos:

- `Scenariusze/`
- `DataStories/`
- `CodeTutorials/`
- `ViralShorts/`

Optional neighbors (non-core):

- `Animacje/`
- `Animacje 2/`
- `Posty daily/`

## 3. New-machine setup sequence

### Codex side

Start Codex with context from:

```text
Desktop/Codex Projekty/Doradcy/SZHAKAL-DORADCA/
```

Load in order:

1. `00_ROLE.md`
2. `01_PROJECT_CONTEXT.md`
3. `02_ADVISORY_RULES.md`
4. `03_DECISION_FRAMEWORK.md`
5. `04_ANTI_PATTERNS.md`
6. `05_WORKING_MEMORY.md`
7. latest session logs as needed

### Claude side

For each cloned real repo:

1. `pip install -r requirements.txt`
2. install JS deps if repo has them (`npm install`)
3. `git config core.hooksPath .githooks`
4. copy `.env.example` -> `.env`
5. fill required keys
6. verify `claude --version`

For `Scenariusze/` also verify:

- `ffmpeg -version`
- any extra system deps described in its local `CLAUDE.md`

## 4. First-run sanity checks on the new machine

### `Scenariusze`

Use cheap isolated commands first:

```bash
python pipeline_v3.py --help
python pipeline_v3.py --category CIEKAWOSTKI --only-context
```

Then escalate only if needed.

### Sibling repos

Cheap checks:

```bash
python pipeline.py --help
```

and, if needed:

```bash
python pipeline.py <sample-input> --only-metadata
```

or equivalent supported safe path.

## 5. Important migration guardrails

- Do not assume root workspace docs are current unless they were refreshed before migration.
- Always inspect `git status` in `Scenariusze` first; it tends to be the live/dirty repo.
- Never mass-stage with `git add -A` in `Scenariusze`.
- Protected local user changes historically lived in:
  `broll-engine/src/videos/pszczoly-iddychanie-pod-woda/`

## 6. Success condition

The migration is successful when:

- advisory repo is cloned and readable by Codex
- all 4 execution repos are cloned
- dependencies install cleanly
- hooks are enabled
- envs are in place
- `Scenariusze` help + cheap category-scoped run work
- Claude and Codex can continue from memory instead of from zero

---

End of bootstrap checklist.
