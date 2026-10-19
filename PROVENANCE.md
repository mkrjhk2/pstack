# Provenance — pstack

## pstack

- **Source**: https://github.com/cursor/plugins — path `pstack`
- **Upstream author**: Lauren Tan (poteto)
- **Licence**: MIT, © 2026 Lauren Tan. Full text: `pstack/references/UPSTREAM-LICENSE.txt`
- **Commit we ported from**: `0b8e952052d482ee6de06deebf14d590d77b1b34`
  (2026-10-18, upstream version `0.15.26`) — recorded machine-readably in `pstack/references/SOURCE.json`
- **Ported on**: 2026-10-17 (Amane, laptop instance)
- **Imported verbatim**: `references/principles/` (24 files), `references/playbooks/` (23),
  `references/skills/` (86 files incl. per-skill references). Upstream text is unmodified.

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

Recorded commit is behind upstream `HEAD`; check with `compare/<commit>...HEAD` filtered to the
`pstack/` path. Re-port = copy the three directories again, keep `SKILL.md`'s house-rules section,
then update `SOURCE.json` (file counts must match, otherwise files were missed).

