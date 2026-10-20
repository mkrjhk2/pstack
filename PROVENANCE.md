# Provenance — pstack

## pstack

- **Source**: https://github.com/cursor/plugins — path `pstack`
- **Upstream author**: Lauren Tan (poteto)
- **Licence**: MIT, © 2026 Lauren Tan. Full text: `pstack/references/UPSTREAM-LICENSE.txt`
- **Commit we ported from**: `88d47514b82e7c44f00f2b2a72ff6dd12158038c`
  (2026-10-20, upstream version `0.15.29`) — recorded machine-readably in `references/SOURCE.json`
  (previous: `0b8e952052`／v0.15.26, 2026-10-18)
- **Ported on**: 2026-10-17; re-synced 2026-10-20 (Amane, laptop instance)
- **Imported verbatim**: `references/principles/` (24 files), `references/playbooks/` (23),
  `references/skills/` (87 files incl. per-skill references). Upstream text is unmodified.

### What we changed

| file | change |
|---|---|
| `SKILL.md` | rewritten for Hermes: three-layer explanation (principles → playbooks → skills), reading table, and a **house-rules section** listing three known conflicts between upstream doctrine and this household's rules (`never-block-on-the-human` vs. asking for approval; playbooks as recipes not canned checklists; not naming principles in every chat reply) |
| `references/mapping.md` | new — Cursor ↔ Hermes translation (`Task` → `delegate_task`, `AskQuestion` → `clarify`, `/skill:<name>` → `skill_view(file_path=…)`, browser preview → `browser_exec`, model panel → `delegation.provider/model`) |
| `references/porting-recipe.md` | new — the general steps for porting any third-party skill pack into Hermes |
| `references/SOURCE.json` | new — provenance record (repo, path, commit, date, version, licence, file counts) |
| `SKILL.md` § 驗證狀態 | new — what has actually been exercised (tdd red/green cycle, verbatim file counts) and what has **not** (multi-model `interrogate`/`swarm`/`arena`; `create-verification-skill` before 2026-10-19) |

### What we did not port

Cursor-specific mechanics: `/loop`, plugin marketplace install commands, bugbot integration, and the
instruction to name principles in every reply (noise on a chat surface). Upstream text that mentions
them is left in place because it lives inside verbatim files; `mapping.md` explains the substitution.

### Upstream freshness

Re-synced 2026-10-20 from `0b8e952052` → `88d47514b8` (14 upstream commits; 6 files landed:
`sharpen`, `poteto-help` (+`references/recipes.md`), `setup-pstack`, `create-formal-verification-skill` updated,
`maintain-formal-verification-skill` added). `docs/guide/` and `poteto-mode/scripts/` remain un-ported by design.
Recorded commit now equals upstream `HEAD` for this path; to check again, check with `compare/<commit>...HEAD` filtered to the
`pstack/` path. Re-port = copy the three directories again, keep `SKILL.md`'s house-rules section,
then update `SOURCE.json` (file counts must match, otherwise files were missed).

