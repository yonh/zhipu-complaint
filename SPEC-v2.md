# Implementation Spec v2: Command Namespacing + Chinese Default

**Builds on:** `SPEC.md` (v1). Read that first. This document describes the delta only.

---

## Motivation

1. Generic command names like `/complaint` collide with other plugins. All commands must
   be namespaced under `zhipu-complaint` to avoid conflicts.
2. Chinese is the primary user language. Chinese command files are the **default**.
3. English variants are retained for international users, distinguished by an `-en` suffix.

---

## Command Naming Convention

| Command | File | Language | Status |
|---------|------|----------|--------|
| `/zhipu-complaint` | `zhipu-complaint.md` | Chinese 🇨🇳 | NEW — primary default |
| `/zhipu-complaint-update` | `zhipu-complaint-update.md` | Chinese 🇨🇳 | NEW — primary default |
| `/zhipu-complaint-en` | `zhipu-complaint-en.md` | English 🇬🇧 | RENAMED from `complaint.md` |
| `/zhipu-complaint-update-en` | `zhipu-complaint-update-en.md` | English 🇬🇧 | RENAMED from `complaint-update.md` |

---

## Target File Structure (delta from SPEC v1)

```
.claude-plugin/
└── marketplace.json              ← source must be "./zhipu-complaint" (not ".")
.claude/commands/                 ← for local development (working inside the repo)
├── zhipu-complaint.md
├── zhipu-complaint-update.md
├── zhipu-complaint-en.md
└── zhipu-complaint-update-en.md
zhipu-complaint/commands/         ← for marketplace install (must mirror .claude/commands/)
├── zhipu-complaint.md
├── zhipu-complaint-update.md
├── zhipu-complaint-en.md
└── zhipu-complaint-update-en.md
```

Files to **delete**:
- `.claude/commands/complaint.md`
- `.claude/commands/complaint-update.md`

All other files from SPEC v1 remain unchanged.

---

## File Specifications

### 1. `zhipu-complaint.md` (NEW — Chinese default)

**Command:** `/zhipu-complaint`

**Language requirement:** All instruction text and example dialogue must be in Chinese.
Legal article citations remain in Chinese (as in CLAUDE.md).

**Content:** Identical conversational flow as defined in SPEC v1 §2
(`complaint.md` — 9 steps), but:
- Title line: `# /zhipu-complaint — 生成智谱 AI 投诉信`
- All system instructions written in Chinese
- All example dialogue snippets (greeter, questions, confirmation prompt) in Chinese
- Step 8 reference: `按照 CLAUDE.md 中定义的投诉信结构`

No other behavioral changes from the v1 spec.

---

### 2. `zhipu-complaint-update.md` (NEW — Chinese default)

**Command:** `/zhipu-complaint-update`

**Language requirement:** All instruction text and example dialogue in Chinese.

**Content:** Identical flow as defined in SPEC v1 §3
(`complaint-update.md` — 5 steps), but:
- Title line: `# /zhipu-complaint-update — 更新已有投诉信`
- All system instructions in Chinese
- All example dialogue snippets in Chinese

No other behavioral changes from the v1 spec.

---

### 3. `zhipu-complaint-en.md` (RENAMED + content audit)

**Command:** `/zhipu-complaint-en`

**Source:** Rename existing `complaint.md` → `zhipu-complaint-en.md`.

**Content changes required:**
- Replace title: `# /complaint — ...` → `# /zhipu-complaint-en — Generate Zhipu AI Complaint Letter`
- Convert **all instruction text** to English. The current file mixes Chinese and
  English; the renamed English version must be fully English.
- All 9 steps retain identical structure and logic.
- Example dialogue snippets must be in English (translate from current Chinese).
- Legal article citations may remain in Chinese (they are proper nouns / law names) but
  the surrounding instructions must be in English.

---

### 4. `zhipu-complaint-update-en.md` (RENAMED + content audit)

**Command:** `/zhipu-complaint-update-en`

**Source:** Rename existing `complaint-update.md` → `zhipu-complaint-update-en.md`.

**Content changes required:**
- Replace title: `# /complaint-update — ...` → `# /zhipu-complaint-update-en — Update Existing Complaint Letter`
- Convert all instruction text to English (same rule as above).
- All 5 steps retain identical structure and logic.

---

### 5. `README.md` (REPLACE — Chinese default)

The primary README must be in Chinese to match the plugin's default language.
Replace the entire file content with a Chinese version that includes:

- **项目标题:** `# zhipu-complaint`
- **一句话描述:** Claude Code 插件，帮助用户生成针对智谱 AI 的正式消费者权益投诉信。
- **安装方式** (保留两种方式，命令不变，注释改为中文)
- **使用方式** — 4 命令对照表，列名用中文（命令 / 语言 / 说明）：

  | 命令 | 语言 | 说明 |
  |------|------|------|
  | `/zhipu-complaint` | 中文（默认） | 启动新投诉信会话，Claude 全程引导 |
  | `/zhipu-complaint-update` | 中文（默认） | 追加事实、更新诉求或完善已有草稿 |
  | `/zhipu-complaint-en` | English | Same as above in English |
  | `/zhipu-complaint-update-en` | English | Same as above in English |

- **使用前准备** — 中文说明用户需提前备好的信息（时间线、承诺截图、损失金额、诉求、证据）
- **输出格式** — 中文说明（Markdown、带版本号、含变更记录）
- **法律依据** — 中文说明，列出三部法规，保留英文注的原法规名，末尾保留免责提示
- **License** — MIT
- **English version:** See [README.en.md](./README.en.md)

---

### 6. `README.en.md` (NEW — English version)

Create a new file `README.en.md` containing the **current English content of `README.md`**
exactly as-is, with one addition: add a line at the top of the file (before the `#` title):

```
> 中文文档请参阅 [README.md](./README.md)
```

No other changes to the English content.

---

## Success Criteria

The implementation is correct when ALL of the following are true:

1. **No generic command names remain.** No files named `complaint.md` or
   `complaint-update.md` exist under `.claude/commands/`.

2. **Four command files exist**, named exactly as listed in the table above.

3. **Chinese files are fully in Chinese.** `zhipu-complaint.md` and
   `zhipu-complaint-update.md` contain no English prose in their instruction text
   (legal article names and code-style tokens are exempt).

4. **English files are fully in English.** `zhipu-complaint-en.md` and
   `zhipu-complaint-update-en.md` contain no Chinese prose in their instruction text
   (legal article names cited as proper nouns are exempt).

5. **Step count is preserved.** Chinese `/zhipu-complaint` has 9 steps; English
   `/zhipu-complaint-en` has 9 steps. Chinese `/zhipu-complaint-update` has 5 steps;
   English `/zhipu-complaint-update-en` has 5 steps.

6. **Forced confirmation (Step 7) is present** in both `/zhipu-complaint` and
   `/zhipu-complaint-en`. Neither may generate a draft before user confirmation.

7. **README Usage section** lists all 4 commands in a table with Chinese column headers
   (`命令 / 语言 / 说明`).

8. **`README.md` is in Chinese.** No English prose outside of the commands table's
   English rows and the law names. A link to `README.en.md` is present.

9. **`README.en.md` exists** and contains the original English content plus a Chinese
   doc link at the top.

10. **No other files are modified.** `CLAUDE.md`, `marketplace.json`, `LICENSE`,
    `SPEC.md`, `SPEC-v2.md` are untouched.
