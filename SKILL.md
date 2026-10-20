---
name: pstack
description: "Use when doing rigorous engineering work (pstack)."
version: 1.0.0
author: Lauren Tan (poteto) — Hermes port by 天音 Amane
license: MIT (upstream) / port notes private
metadata:
  hermes:
    tags: [pstack, engineering, principles, playbooks, verification, delegation, review]
    related_skills: [amane-work-mode, project-workflow, openrouter-model-workers, github-code-review, hermes-agent]
---

# pstack（Hermes 版）

上游：`github.com/cursor/plugins/tree/main/pstack` — Lauren Tan（poteto；React core、Netflix／Meta／Cursor、而家 SpaceX principal engineer）。MIT。
本 port 嘅上游快照寫喺 `references/SOURCE.json`（commit、日期、版本、件數），上游原文**逐字保留**，冇改寫。

**三層**：原則（點判斷）→ playbook（按任務類型嘅流程）→ skill（實際手）。入口係 `poteto-mode`（上游叫 unified entry），落到 Hermes 即係「讀邊份、跟邊個 playbook」。

## When to use

- 改 code／重構／修 bug／加功能／跑效能／寫測試／review 嘅時候（唔係傾偈）
- 要「第二對眼」：多模型對審、找盲點、質疑設計
- 要為一個 project 建**可重複嘅驗證**（唔再靠人肉試）
- 想知「呢段 code 點行」（`how`）／「點解當初咁寫」（`why`）

## 讀邊份（Hermes 路徑）

| 想要 | 讀 |
|---|---|
| 原則（24） | `skill_view(name='pstack', file_path='references/principles/principle-<kebab>.md')` |
| 流程（23） | `references/playbooks/<name>.md` |
| 手（31） | `references/skills/<name>/SKILL.md`（子檔照原路徑，例如 `references/skills/interrogate/references/rubric.md`） |
| Cursor 名 → Hermes 工具 | `references/mapping.md` |

上游寫 `/how`、`/tdd`、`Task`、`AskQuestion`、`bugbot` 呢啲名字，一律照 `references/mapping.md` 譯。

## ★ 屋企規矩優先（讀之前先讀呢節）

pstack 係**工具**，`SOUL.md` 同 `amane-work-mode` 係**教條**。撞嘅時候屋企贏。三個已知撞位：

1. **`principle-never-block-on-the-human` vs「意圖未清就停低問」**
   → 呢條原則**只准**用喺「可以用實驗／查檔自己答到嘅技術問題」（例：邊個 layout 好、呢個數字點嚟）。**唔准**用嚟繞過主人嘅批准，亦唔准用嚟把「佢未答」當成「默認繼續」。批准、價錢、動佢部機嘅事，照舊等佢一句。
2. **playbook 係步驟，唔係罐頭**
   → 步驟照跟；但**唔准**把 pstack 嘅評分表／四分類／checklist 搬去對話、生活、brainstorm 回合（主人 2026-10-08 明令「唔要罐頭」）。模板只留喺 code review 同工程交付。
3. **「每次回覆列出用咗邊條原則」＝唔搬**
   → 上游要 agent 喺回覆點名原則，因為 Cursor 係 IDE 介面。喺 Telegram 咁做係噪音。改成：默認唔列；主人問「點解」、或者係工程報告時，才用一行列出。

另外兩條硬界線：**派工**照 `amane-work-mode` §5（我係 GM，唔親手寫 feature code）；**驗收**只認真證據（真機 screencap／logcat／測試輸出），唔認「佢話做好咗」。

## 分流（睇情況讀邊份）

- 非平凡改動／架構決定／「係咪真係咁」→ `skills/how`
- 寫任何 code → 先寫低 **data shape**，再按 `principle-model-the-domain` 揀結構
- 跨 function 邊界 → `skills/architect`（平行探索設計，先寫 rationale）
- 要平行掃／分區探索／互相比賽 → `skills/swarm`；設計或 code 二選一、要嫁接各家所長 → `skills/arena`
- 設計有爭議 → `skills/interrogate`（多模型對審）**先於** 交付
- 修 bug → `playbooks/bug-fix.md`；有便宜測試位 → `skills/tdd`
- 重構 → `playbooks/refactoring.md` ＋ `principle-laziness-protocol`
- 效能 → `playbooks/perf-issue.md` ＋ `principle-explain-the-number`
- 交付前 → `principle-prove-it-works`；UI／服務 → `skills/create-verification-skill`
- 寫 docs／README／PR 描述 → `skills/technical-writing`
- 回覆或文件嘅文字質地 → `skills/unslop`（＝上游 deslop 嘅對等）
- 交 commit 前 → `skills/no-comments`（只刪「講你已經睇得出」嘅註釋）
- 隔幾日返嚟／接手別人嘅 project → `playbooks/session-pickup.md`、`skills/recall`
- 主人聽唔明你講乜 → `skills/bro`（用人話再講一次）

## 五個最值錢嘅 workflow（Hermes 化）

### 1. `tdd`（修 bug 版）
寫一個會失敗嘅測試 → **親眼見佢 fail**（跑一次，確認 fail 嘅理由係對嘅）→ 最小修正 → 跑返測試見綠。
冇便宜測試位（要 harness、mock、真機）就**唔好硬造**：改用最接近嘅驗證（真機 screencap、logcat、真問一句 API）。

### 2. `interrogate`（多模型對審，唔改 code）
1. 定範圍（`git diff main...HEAD` 或者指定檔案）
2. 用一段寫清 intent
3. 一次 `delegate_task` 派多個 reviewer，每個 pin 唔同 model（屋企名冊見 `openrouter-model-workers`）
4. 收貨：**四分類**——必改／你決定／記住／忽略；**唔准自己改 code**，結論交主人

### 3. `create-verification-skill`（為 project 建驗證）
答四條：**surface**（用戶摸到乜）／**run**（點起）／**drive**（點程式化操作）／**observe**（留咩證據）。
挑 3–5 個主功能，寫明「叫做到」嘅定義 → 自己先跑一次 → 連**故意把已修好嘅 bug 放返落去**，確認驗證真係捉得到。

Hermes 落地位（實跑過，唔係推演）：
- 產生一個 profile 級 skill：`skills/verify-<app>/SKILL.md`（無 project-local 掃描，所以**一個 project 一個**，名要帶 app 名）。
- 附屬檔**只准**放 `references/`／`templates/`／`scripts/`／`assets/`——上游寫嘅 `features/` 頂層目錄**入唔到** Hermes，改成 `references/features/`（一行一個功能，四條 H2：Sub-features／How to get to it (user POV)／Driving it with the harness／Gotchas）。
- 新建 skill 嘅 `description` 硬限 **60 字**（長過就 `create` 直接失敗）；詳細觸發條件寫入 body 嘅 `## When to Use`。
- **Doctor 要喺環境唔齊時老實 FAIL 再連鎖 SKIP**，唔可以扮健康；用 exit code 0/1 令 cron 用得到。呢個「壞嘅時候識報壞」本身就係驗證嘅一部分。
- 完成定義三件：綠（真跑）＋故意放回 bug 見紅（紅嘅理由要係對嘅嗰條）＋復原後 `git diff` 空。

### 4. `prove-it-works`
唔可以「compile 過」或者「命令冇 error」就報完成。對真產物驗：真瀏覽器（`browser_exec` 量 DOM／像素）、真機（screencap／logcat）、真 API（回一個真答案）。

### 5. `no-comments` ＋ `unslop`
只刪「講返 code 做乜」嘅註釋，保留因外部限制而寫嘅（第三方行為、奇怪 workaround）。文字側由 `unslop` 收緊：短句、去廣告腔、唔好每句都變結論。

## 24 條原則（一行一條；用嗰條就讀佢 leaf）

**核心**：`laziness-protocol`（刪得就唔好加）／`foundational-thinking`（先定 core type）／`redesign-from-first-principles`／`attack-the-premise`（兩次修都死同一個前提＝前提錯）／`subtract-before-you-add`／`minimize-reader-load`／`outcome-oriented-execution`／`experience-first`／`exhaust-the-design-space`（做 2–3 個原型比較）／`build-the-lever`（寫工具，唔好人手做）
**架構**：`model-the-domain`／`boundary-discipline`／`type-system-discipline`／`make-operations-idempotent`／`migrate-callers-then-delete-legacy-apis`／`separate-before-serializing-shared-state`
**驗證**：`prove-it-works`／`fix-root-causes`／`sequence-verifiable-units`／`test-behavior-not-implementation`／`explain-the-number`（信一個數字之前，先找佢嘅上限）
**委派**：`guard-the-context-window`（大批嘢交子代理，主線只留摘要）／`never-block-on-the-human`（見上面衝突一）／`encode-lessons-in-structure`

（`ls references/principles/` 見齊 24 個檔，檔名＝原則名。）

## 23 個 playbook

authoring-a-skill／autonomous-run／autopilot-full／autopilot-stack／babysit／bug-fix／eval／feature／hillclimb／investigation／multi-phase-plan／opening-a-pr／orchestrate／pause-safely／perf-issue／prototype／refactoring／runtime-forensics／session-pickup／shipping／trace-forensics／visual-parity／worktree-cleanup

## 32 個 skill

architect／arena／automate-me／benchmark-checklist／blast-radius／bro／correct／create-formal-verification-skill／create-verification-skill／figure-it-out／how／interrogate／in-your-own-words／ketchup／maintain-formal-verification-skill／maintain-verification-skill／make-bot-ui／no-comments／poteto-help／poteto-mode／recall／reflect／setup-pstack／sharpen／show-me-your-work／swarm／tdd／teach／technical-writing／typescript-best-practices／unslop／why

`setup-pstack`／`poteto-help` 係 Cursor 安裝器同說明，Hermes 用唔到佢哋嘅命令——要睇「點用」讀 `references/skills/poteto-help/references/recipes.md`。

## 更新上游

**本 port 已經有公開 repo**：`github.com/mkrjhk2/pstack`（root ＝ `SKILL.md` ＋ `references/` ＋ `README.md` ＋ `PROVENANCE.md` ＋ 上游 MIT 全文）。另一個 host 想用就直接 clone 落 `$HERMES_HOME/skills/software-development/pstack`，**唔需要重新 port 一次**；只有要跟上游新 commit 才做下面嘅步驟。

```bash
cd "$TMPDIR" && curl -sL -o p.tgz https://codeload.github.com/cursor/plugins/tar.gz/refs/heads/main && tar xzf p.tgz
# 之後重跑 SOURCE.json 記低嘅搬檔步驟（principles／playbooks／skills 三組）
```
搬完一定要更新 `references/SOURCE.json`（新 commit、日期、件數），件數對唔上就係搬漏。
完整嘅通用步驟（一個 request 拉 tarball、逐層放 references、寫 mapping、house-rules 節、provenance、驗收）見 `references/porting-recipe.md`——再 port 任何第三方 skill pack 都照呢份做。

**port 完之後要追得上游，唔係一次性**：`HERMES_HOME/scripts/skill_upstream_sync.py` 讀所有 `references/SOURCE.json`（＋`skills/.upstreams.json` 嘅 watch list）用 GitHub compare API 報「落後幾多個 commit、改咗邊幾個檔」；`--watch <owner/repo>` 加監視、`--update <skill>` 記新 commit、落後時 exit 1（可以掛 cron）。實測：port 完兩日上游已經行前 10 個 commit，靠肉眼永遠唔會知。
同時 `HERMES_HOME/scripts/save_all_skills.py` 做唯讀備份＋使用報告（skill 係 agent 自己嘅 project memory，唔見咗就等於教訓唔見咗）。

**port 之前先問一句「呢個 skill 有冇上游」**（分三類）：① vendored（有 SOURCE.json → 追 commit）② bundled（Hermes 自帶 → 交 `hermes update`，**手改會被覆蓋**，教訓要寫落獨立 skill）③ authored（自己寫 → 冇上游，「更新」＝加教訓）。分錯類就會出現「以為有得更新，其實改完下次 update 被洗」.

## 驗證狀態（2026-10-17 建 port 時實測）

- 檔案：原則 24／playbook 23／skill 檔 87（含子 references）＝三層 134，加 `mapping.md`／`porting-recipe.md` 共 **136 個 md**，全部對得上上游件數（同步到上游 `88d47514b8`／v0.15.29，2026-10-20）。
- `skill_view` 讀得到 SKILL.md 同 `references/` 內任何一份（本 port 用呢個方式自測）。
- **`tdd` 紅綠循環已實測**（2026-10-17，Hermes terminal）：寫測試 → 跑 → 確認 fail 嘅理由對（`'--a--b--' != 'a-b'`）→ 最小修正 → 綠；再把 bug 放回去 → 再紅 → 還原 → 綠。四步都係真跑出嚟，唔係推演。
- **未驗**：`interrogate`／`swarm`／`arena` 嘅多 model 派工（要燒 API call，未跑）。
- **`create-verification-skill` 已喺真 project 跑過一次**（Ayame，2026-10-19）：產出 `skills/verify-ayame/` ＋ `references/features/` 五個功能；契約測試 197 PASS，故意把 `policy` 嘅 `ASK` 改 `AUTO` → 3 紅（含專守嗰條）→ 復原 → 197 PASS。**未驗**：doctor 嘅真機五項同真功能 drive（當時部機斷線）。

## Pitfalls

- **唔好把 24 條原則當 checklist 逐條打勾**——上游用法係「用嗰條就讀嗰條」，一次任務通常 1–3 條。全部讀一次＝燒 context，違反 `guard-the-context-window`。
- 上游文字成日寫 `/slash` 命令同 Cursor UI 動作（plugin marketplace／`/loop`／bugbot）。**照 `references/mapping.md` 譯，唔好扮 Hermes 有嗰個命令**。
- 上游假設有測試套件同可 script 嘅 UI；我哋好多 project 嘅驗證係**真機**（Ayame／Rokid）。`tdd` 嗰節講明冇便宜測試位就唔好硬造，照用真機證據。
- 上游 `principle-never-block-on-the-human` 嘅精神（唔好乾等）值得留，但**唔可以**變成繞過主人批准嘅藉口。
- **「故意放返 bug」嘅紅綠循環，復原標準係 `git diff` 空，唔係 sha256。** Windows repo（`core.autocrlf=true`）之下 `sed -i` 會改行尾，`git checkout --` 之後檔案字節同原本唔同、但 git 認為乾淨——用 hash 比對會誤報「復原失敗」，令人以為整壞咗個 repo。驗完固定做兩件：`git diff --stat`（要空）＋再跑一次測試（要綠）。
