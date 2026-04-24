# Implementation Spec: zhipu-complaint Claude Code Plugin

## Overview

This is a **Claude Code slash-command plugin** for generating formal complaint letters
against Zhipu AI (智谱华章科技有限公司). The plugin is **conversation-first**: Claude
guides the user through a structured interview and iteratively produces a Markdown
complaint letter. There is no Python, no YAML config, no shell scripts.

---

## Design Principles

1. **No code execution.** All logic lives in prompt instructions. Claude is the engine.
2. **Conversation-driven intake.** Claude asks one group of questions at a time, waits
   for answers, then proceeds. Never dumps a long form at the user.
3. **Iterative output.** After the first draft, Claude asks for feedback and applies
   targeted edits. Each revision is versioned (v1, v2, …).
4. **Legally grounded.** Complaint letters must cite specific articles from:
   - 《消费者权益保护法》
   - 《民法典》合同编
   - 《网络交易监督管理办法》(when applicable)
5. **Fact-only.** No fabrication, no exaggeration. If a fact is unclear, Claude must ask.

---

## Target File Structure

All files below must be created or replaced. Files **not** listed here must be deleted.

```
zhipu-complaint/
├── .claude-plugin/
│   └── marketplace.json          ← update source field from "./zhipu-complaint" to "."
├── .claude/
│   └── commands/
│       ├── complaint.md          ← NEW: /complaint slash command
│       └── complaint-update.md   ← NEW: /complaint-update slash command
├── CLAUDE.md                     ← REPLACE existing content
├── README.md                     ← REPLACE existing content
└── LICENSE                       ← keep existing, no change needed
```

**Items to delete:**
- `zhipu-complaint/zhipu-complaint/` (empty nested directory)
- Any `skills/`, `commands/`, `scripts/` directories not under `.claude/`
- Any `.py`, `.yaml`, `.sh` files

---

## File Specifications

### 1. `CLAUDE.md`

**Purpose:** Loaded automatically by Claude Code when the plugin is active. Defines
Claude's role, collection checklist, and output standard.

**Required sections (in order):**

#### 1.1 Role Declaration
- Claude acts as a professional consumer rights paralegal assistant.
- Specialization: disputes with Zhipu AI platform (API subscriptions, billing, broken
  promises on plan changes).
- Tone: professional, assertive, fact-based, never fabricated.

#### 1.2 Conversation Protocol
- Collect information through dialogue before writing anything.
- Ask no more than 2 questions per turn.
- Never assume facts not provided by the user.
- If a user provides vague information (e.g., "they cheated me"), ask follow-up
  questions to get specific dates, amounts, and statements.

#### 1.3 Information Collection Checklist
Claude must confirm all items are filled before generating the first draft:

| # | Field | Example |
|---|-------|---------|
| 1 | User identity | Name or account ID used for the complaint |
| 2 | Product / service | API subscription, which plan |
| 3 | Timeline | Key dates with what happened on each |
| 4 | Company's promise | Exact wording of what was promised, source (announcement / email / UI) |
| 5 | Actual outcome | What really happened vs. the promise |
| 6 | Quantified loss | Amount paid, rights lost, project delays, etc. |
| 7 | User demands | What resolution is expected (refund / restore rights / apology / compensation) |
| 8 | Evidence on hand | Screenshots, receipts, notifications, etc. |
| 9 | Escalation intent | Whether user plans to file with 12315, regulators, media, etc. |

#### 1.4 Complaint Letter Structure
Every generated letter must follow this exact section order:

1. **Header block** — complainant info, respondent, subject, date
2. **Section 1: 事实经过** — chronological facts, objective tone
3. **Section 2: 违规分析** — mapped to specific legal articles
4. **Section 3: 具体诉求** — numbered list of demands
5. **Section 4: 保留权利** — escalation actions if unresolved within N working days
6. **Section 5: 证据清单** — numbered list of attachments

#### 1.5 Output Standards
- Format: Markdown, suitable for direct paste into email or print.
- Version label: add `<!-- v1 -->` comment at top of each generated draft.
- After output, always ask: "需要调整哪部分？（措辞 / 法律依据 / 诉求 / 其他）"

---

### 2. `.claude/commands/complaint.md`

**Purpose:** Slash command `/complaint`. Starts a fresh complaint letter generation
session from zero.

**Required behavior (encode this as instructions to Claude):**

**Step 1 — Opening**
Greet the user briefly, explain the process: "I'll ask you a few questions to understand
your situation, then draft a formal complaint letter. Let's start."

**Step 2 — Background (Round 1)**
Ask simultaneously:
- Which Zhipu AI product/service is involved?
- Roughly when did the problem start?

**Step 3 — Core Facts (Round 2)**
After receiving Round 1 answers, ask:
- What did Zhipu AI promise? (Quote their exact wording if possible, and note where the
  promise appeared.)
- What actually happened? How does it differ from the promise?

**Step 4 — Loss & Demands (Round 3)**
After receiving Round 2 answers, ask:
- What is your specific, quantified loss? (e.g., ¥X paid, N months of subscription
  rights lost, project delayed by X days)
- What resolution do you want from Zhipu AI?

**Step 5 — Evidence & Escalation (Round 4)**
After receiving Round 3 answers, ask:
- What evidence do you have? (List types: screenshots, receipts, email, etc.)
- Have you already contacted Zhipu AI support? What was their response?

**Step 6 — Identity**
Ask:
- How should the complaint be signed? (Real name, account ID, or anonymous placeholder?)

**Step 7 — Confirmation**
Before writing, summarize all collected facts in a brief bullet list and ask:
"以上信息是否准确？有需要补充或修正的吗？"

**Step 8 — Draft Generation**
Only after user confirms → generate the full complaint letter following the structure
defined in `CLAUDE.md`. Mark as `<!-- v1 -->`.

**Step 9 — Iteration Prompt**
After the draft, ask: "需要调整哪部分？（措辞 / 法律依据 / 诉求 / 语气 / 其他）"
Apply targeted edits and increment version number.

---

### 3. `.claude/commands/complaint-update.md`

**Purpose:** Slash command `/complaint-update`. Used when the user already has a draft
and wants to add new facts, update demands, or change tone.

**Required behavior (encode this as instructions to Claude):**

**Step 1 — Context Check**
Ask the user to paste their current complaint letter draft (if it's not already visible
in the conversation).

**Step 2 — Update Intent**
Ask what needs to change. Present these options as examples (not a forced menu):
- 追加新的违规事实（new facts discovered）
- 修改诉求（change demands）
- 补充证据（new evidence available）
- 调整语气（softer / stronger / more formal）
- 增加升级行动（add 12315 filing, regulatory complaint, social media, etc.）
- 其他

**Step 3 — Targeted Edit**
Apply changes **only to affected sections**. Do not rewrite sections that were not
mentioned. If new information conflicts with existing content, ask the user to clarify
before making any change.

**Step 4 — Output**
Output the updated full letter with incremented version number (e.g., `<!-- v2 -->`).
Add a brief **change summary** at the top as an HTML comment:
```
<!-- v2: 追加"出尔反尔"事实段落，更新Section 4补偿条款诉求 -->
```

**Step 5 — Follow-up**
Ask: "还有其他需要调整的吗？"

---

### 4. `README.md`

**Purpose:** Public-facing documentation for the GitHub repo. No technical setup beyond
`/plugins add`.

**Required sections:**

#### 4.1 Project Header
- Repo name: `zhipu-complaint`
- One-line description: Claude Code plugin for consumer rights complaints against Zhipu AI
- Badges (optional): license

#### 4.2 What This Does
2-3 sentences: This plugin turns Claude Code into a conversational complaint letter
assistant. Through a structured interview, Claude gathers your facts, applies relevant
Chinese consumer protection law, and generates a ready-to-send Markdown complaint letter.
Supports iterative refinement.

#### 4.3 Installation
```bash
# Install from GitHub
/plugins add https://github.com/yonh/zhipu-complaint

# Or install locally
/plugins add /path/to/zhipu-complaint
```

#### 4.4 Usage
Show both slash commands with a brief description:
- `/complaint` — Start a new complaint session. Claude will guide you through the process.
- `/complaint-update` — Add new facts, update demands, or refine an existing draft.

#### 4.5 What to Prepare
A short bullet list of information the user should have ready:
- Key dates and timeline
- What was promised (screenshots help)
- What actually happened
- Your specific demands
- Evidence you have on hand

#### 4.6 Output Format
Mention that output is Markdown, versioned, and suitable for email / print / social
media posts.

#### 4.7 Legal Basis
Note that the plugin references Chinese consumer protection law (消费者权益保护法,
民法典 contract provisions) and that users should verify legal citations for accuracy
before submitting formal complaints.

#### 4.8 License
MIT

---

## Success Criteria

The implementation is correct when:

1. **File structure matches exactly** — only the files listed in the target structure exist.
2. **No executable code** — no `.py`, `.sh`, `.yaml`, or other runnable files.
3. **`CLAUDE.md` is self-contained** — reading it alone gives Claude enough context to
   assist a user even without running a slash command.
4. **`/complaint` command** covers all 9 steps, in order, without skipping the
   confirmation step (Step 7) before generating the draft.
5. **`/complaint-update` command** enforces targeted edits only — it must not regenerate
   sections that were not changed.
6. **Version numbering** is applied consistently: v1 for initial draft, v2+ for each
   subsequent update.
7. **All legal citations** in `CLAUDE.md` are real, correct article numbers.
8. **README.md** contains no references to Python, YAML, or scripts.
