# pstack（Hermes 版）

[Hermes Agent](https://github.com/NousResearch/hermes-agent) 用嘅 **pstack** port —— 上游係
[`cursor/plugins`](https://github.com/cursor/plugins) 嘅 `pstack`，作者 **Lauren Tan（poteto）**，MIT。

三層：**原則（點判斷）→ playbook（按任務類型嘅流程）→ skill（實際手）**。入口係 `poteto-mode`
（上游叫 unified entry），落到 Hermes 即係「讀邊份、跟邊個 playbook」。

## 裝

```bash
git clone https://github.com/mkrjhk2/pstack ~/projects/pstack
cp -r ~/projects/pstack "$HERMES_HOME/skills/software-development/pstack"
```

`SKILL.md` 要有合法 frontmatter 才會被索引；重開 session 或 `hermes skills list` 就見到。

## 用（Hermes 路徑）

| 想要 | 讀 |
|---|---|
| 原則（24） | `skill_view(name='pstack', file_path='references/principles/principle-<kebab>.md')` |
| 流程（23） | `references/playbooks/<name>.md` |
| 手（31 個，共 86 個檔） | `references/skills/<name>/SKILL.md` |

上游寫 `/how`、`/tdd`、`Task`、`AskQuestion`、`bugbot` 呢啲名字，一律照
`references/mapping.md` 譯成 Hermes 工具。

## 同上游唔同嘅三處（屋企規矩優先）

1. `principle-never-block-on-the-human` **只准**用喺「可以用實驗／查檔自己答到」嘅技術問題；唔准用來繞過批准
2. playbook 係步驟，唔係罐頭；評分表／四分類只留喺 code review 同工程交付
3. 唔會喺每次回覆點名用咗邊條原則（上游係 IDE 介面，chat 上係噪音）

完整改動清單、上游 commit、授權全文位置：**[PROVENANCE.md](PROVENANCE.md)**。
上游原文逐字保留（`references/` 內 133 份 md 冇改寫）。

## 授權

- 上游：MIT，© 2026 Lauren Tan — `references/UPSTREAM-LICENSE.txt`
- 本 port 新增部分：MIT，© 2026 天音 Amane
