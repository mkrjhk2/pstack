# Porting an upstream skill/prompt pack into Hermes

The reusable procedure behind this skill. Use it for any external pack (a Cursor plugin,
Claude/Codex skill bundle, a prompt-methodology repo) that the owner says to "take and make
work for Hermes".

## 1. Pull the whole tree in ONE request

Per-file GitHub API calls (60+ files) are slow and rate-limited. Take the tarball:

```bash
cd "$TMPDIR" && curl -sL -o p.tgz https://codeload.github.com/<org>/<repo>/tar.gz/refs/heads/main
mkdir -p psrc && tar xzf p.tgz -C psrc && du -sh psrc/*/.
```

Then measure before designing: `ls <pack>/skills | wc -l`, sizes per entry, and where the
sub-layers live (playbooks often sit inside the entry skill, e.g.
`skills/<entry>/playbooks/*.md`).

## 2. Target shape: ONE umbrella + layered references

- `SKILL.md` — the router and the house adaptations. Never the upstream text.
- `references/<layer>/<name>.md` — one directory per upstream layer (principles / playbooks /
  skills), upstream files copied **verbatim**, sub-`references/` preserved at their original
  relative paths.
- Big reference trees are CHEAP: skill bodies load on demand, so 130 files cost the index
  the same one line as 3. Do not compress or summarise upstream prose to save space —
  verbatim is what makes re-syncing and auditing possible.

## 3. The actual porting work is the MAPPING file

Upstream prose is full of tokens that do not exist in Hermes (`/slash` commands, an agent
harness's Task tool, `AskQuestion`, model ids like `claude-opus-5-5-xhigh`, bot reviewers,
plugin marketplaces). Write `references/mapping.md`: a table of upstream token → Hermes tool,
plus a glossary for domain names (`the panel`, `the models`, `drive`) so a future session
does not read them literally. Rule for the SKILL.md body: **translate via the mapping, never
pretend the command exists**.

## 4. A "house rules win" section is mandatory

Every upstream pack carries assumptions that collide with local doctrine. Find them and write
them into SKILL.md *before* the router, so they are read first:

- autonomy/speed principles ("never block on the human") vs this owner's approval gates;
- step-by-step playbooks vs "no canned templates" in conversation;
- upstream output contracts ("name the principle you used in every reply") that become noise
  in a chat surface — keep them for engineering reports only.

State it as a precedence rule: the pack is a tool, the local work-mode skill is doctrine.

## 5. Provenance is part of the deliverable

- `references/SOURCE.json`: repo, path, **commit sha** (`GET /repos/<org>/<repo>/commits?path=<dir>&per_page=1`),
  commit date, upstream version, port date, per-layer file counts, licence string.
- `references/UPSTREAM-LICENSE.txt`: the upstream licence file itself.
- Counts in SOURCE.json double as the re-sync diff: a future pull whose counts disagree with
  the recorded ones means files moved or were added.

## 6. Verification — the port is not done until a workflow RUNS

1. Counts per layer match upstream (`ls references/principles | wc -l`, etc.).
2. `skill_view(name)` returns the new SKILL.md; `skill_view(name, file_path='references/…')`
   returns one file per layer — presence in the directory is not proof of readability.
3. **Execute one workflow end-to-end**, choosing the cheapest that exercises the discipline:
   for a TDD/bug-fix playbook, write a test → run → confirm it fails for the intended reason
   → minimal fix → green → put the bug back → red again → restore → green. Record the four
   steps in the skill's verification section, and say plainly which workflows are still
   UNVERIFIED (multi-model review costs API calls; verification-skill generation needs a real
   project).

## 7. Pitfalls

- `skill_manage create` refuses a description over 60 chars, and refuses when the target
  directory already exists — writing a `references/` file first creates it. Full mechanics:
  skill `hermes-agent`, `references/authoring-skills.md`.
- Do not copy images/assets or plugin scaffolding (`.cursor-plugin/`, `assets/`) — they are
  install-time artifacts of the upstream harness.
- Do not port an upstream installer/help skill as if its commands work; point at its
  `references/recipes.md` for the usage ideas instead.
- Keep the upstream's own entry-skill text — it is the closest thing to a spec of the layer
  semantics, and rewriting it silently drops rules.
