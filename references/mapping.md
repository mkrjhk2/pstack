# pstack → Hermes 對映表

上游係 Cursor plugin（`cursor/plugins/pstack`，MIT，Lauren Tan）。佢嘅步驟名、命令名、sub-agent 名全部係 Cursor 嘅。喺 Hermes 要讀成下面呢張表。

## 工具對等

| 上游（Cursor） | Hermes 對等 | 注意 |
|---|---|---|
| `Task` tool / subagent（`subagent_type: generalPurpose`） | `delegate_task`（`tasks` 陣列，每個一個獨立 context） | 子代理**唔可以** `clarify`／發問，所以要喺 `context` 一次講齊背景、輸出格式、要驗嘅嘢 |
| 同一個訊息 spawn 多個 reviewer | 一次 `delegate_task` 傳多個 tasks（本機上限 12 並行） | 每個 task 嘅 `goal` 要自成一體 |
| 指定 reviewer model（`claude-opus-5-5-xhigh` / `grok-4.7-xhigh-fast`） | `delegate_task` 繼承父 model；要換 model 用 **OpenRouter worker**（見 skill `openrouter-model-workers`） | 屋企名冊：`grok-4.7`＝判斷／評審、`grok-build-0.1`＝寫 code、`deepseek-v4-pro`＝後備 |
| `.cursor/rules/pstack-models.mdc` 嘅 model panel | `config.yaml` 嘅 `delegation.provider` / `delegation.model`，或者 worker 腳本自己指定 | 唔好為咗呢件事改全機設定 |
| `AskQuestion` | `clarify`（一次最多 5 條，有選項） | **屋企規矩優先**：只問「實驗／查檔答唔到、而且真係得主人答」嘅嘢（見 SKILL.md 衝突一） |
| `/skill:<name>` / `/poteto-mode` | `skill_view(name='pstack', file_path='references/skills/<name>/SKILL.md')` | playbook 就讀 `references/playbooks/<name>.md` |
| Cursor 內建 browser preview | `browser_exec`（真瀏覽器；`page_info()`／`js()`／截圖） | 驗 UI 要用它可以量度嘅證據：DOM 幾何、hit-test、像素採樣 |
| `cursor-team-kit` 嘅 `control-ui` / `control-cli` | 就係 `browser_exec` / `terminal`（pty＝`pty=True`） | 上游嗰兩個 plugin 唔需要搬 |
| `deslop`（cursor-team-kit） | `references/skills/unslop/SKILL.md` | 上游自己都話 `unslop` 係同族 |
| `/loop until X` | `cronjob_manage`（定時）或 `terminal(background=True, notify=True)` | 長時間跑要 `persist_on_release` 先頂得過 session 結束 |
| Cursor 嘅 babysit / bot review（bugbot） | `gh` CLI ＋ skill `github-code-review`／`github-pr-workflow` | 上游 `playbooks/babysit.md` 嘅分類邏輯照用，命令換成 `gh` |
| Project-local skill（`.cursor/skills/verify-<app>/`） | 放喺 profile skills 目錄：`skills/<category>/verify-<app>/SKILL.md` | Hermes 只掃 profile skills 目錄，冇 project-local 掃描 → 檔名要帶 app 前綴 |
| worktree | `git worktree add`（`terminal`） | 冇變 |
| 「name each principle that shaped a decision」 | **默認唔列**（見 SKILL.md 衝突三） | 只喺工程報告或者主人問「點解」時列 |

## 名詞對照（免得照字面讀錯）

- **principle leaf**＝`references/principles/principle-*.md`，24 個，一個原則一份。
- **playbook**＝`references/playbooks/*.md`，23 個，按任務類型。
- **skill**＝`references/skills/<name>/SKILL.md`，31 個，實際手。部分 skill 另有 `references/` 子檔（例如 `architect/references/runner-prompt.md`、`interrogate/references/rubric.md`），照原路徑搬咗。
- **the panel / the models**＝跑 multi-model 嘅那組模型；喺 Hermes 即係幾個 pin 唔同 model 嘅 worker。
- **deslop**＝上游 `cursor-team-kit` 嘅 skill，本 port 冇搬，用 `unslop` 代替。
- **drive**＝Cursor 嘅 subagent 驅動工具，本 port 冇對等，改用 `delegate_task` + 明確 brief。
