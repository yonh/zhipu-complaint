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
.claude/commands/
├── zhipu-complaint.md            ← NEW (Chinese default, /zhipu-complaint)
├── zhipu-complaint-update.md     ← NEW (Chinese default, /zhipu-complaint-update)
├── zhipu-complaint-en.md         ← RENAMED + content fully in English
└── zhipu-complaint-update-en.md  ← RENAMED + content fully in English
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

### 5. `README.md` (update — command table)

Replace the existing Usage section with a table that lists all 4 commands:

| Command | Language | Description |
|---------|----------|-------------|
| `/zhipu-complaint` | Chinese (default) | Start a new complaint session guided by Claude |
| `/zhipu-complaint-update` | Chinese (default) | Add facts, update demands, or refine an existing draft |
| `/zhipu-complaint-en` | English | Same as above in English |
| `/zhipu-complaint-update-en` | English | Same as above in English |

Keep all other README sections unchanged.

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

7. **README Usage section** lists all 4 commands in a table with language labels.

8. **No other files are modified.** `CLAUDE.md`, `marketplace.json`, `LICENSE`,
   `SPEC.md`, `SPEC-v2.md` are untouched.
