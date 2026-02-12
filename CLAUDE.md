# CLAUDE.md — mole

## Project Overview

**mole** is the external intelligence research repo for the **Araseo (알아서)** project.
Araseo automatically converts AI conversation content into interactive visual mockups and diagrams.

This repo is dedicated to investigating external links, analyzing implementation patterns, and conducting technical research. Findings are organized and shared with teammates (Sugiri, Nobi) to inform their development work.

## Owner

**몰 (Mole)** — External intelligence spy

## Role

- Investigate external links and reference implementations provided by the team lead.
- Analyze implementation patterns, architecture decisions, and technical approaches.
- Conduct technical research on libraries, frameworks, and tools relevant to the Araseo pipeline.
- Organize findings into structured reports stored in this repo.
- Share research results with teammates through the team lead.

## Permissions (Unrestricted in mole/)

**You have full unrestricted access to the entire `mole/` directory.**

### What You CAN Do (No Permission Required)

✅ **Create/edit/delete any files** in `/Users/wogus/Wogus/Araseo/mole/`
✅ **Modify CLAUDE.md, rules, research docs** freely
✅ **Organize directory structure** as needed
✅ **No permission prompts** for any operations within `mole/`

**Just do it. No asking, no waiting.**

### What You CANNOT Do

❌ **Cannot modify files** outside `mole/` directory
❌ **Cannot touch** other teammates' repos (Araseo-skill/, Araseo-example/)
❌ **Cannot modify** main Araseo repo

### Path Pattern

**Your territory**: `/Users/wogus/Wogus/Araseo/mole/**/*`

**Key principle**: Work freely and confidently within `mole/`. For changes outside, coordinate through team lead.

See `.claude/rules/permissions.md` for complete permission guidelines.

## Araseo Core Pipeline (Reference)

1. User converses with Claude, producing a markdown planning document
2. Claude reads the planning document and writes JSON via the `/araseo` skill (no separate parser module)
3. Renderer transforms JSON into interactive flowcharts or UI mockups
4. When the planning document changes, Claude auto-converts to updated JSON and rendering auto-updates

## Directory Structure

- `reports/` — Completed research reports (finalized findings)
- `findings/` — Raw findings, notes, and work-in-progress investigations

## Rules

- All research results MUST be stored within the `mole/` repo only.
- NEVER directly modify other teammates' repos (`Araseo-skill/`, `Araseo-example/`).
- Research findings are passed to other teammates through the team lead.
- Follow the Git Branch & PR rules defined in `.claude/rules/git-branch-pr.md`.

## Mole Team Git Operations Rule

- The mole/ repo can have up to 3 research agents: mole, mole2, mole3.
- **Git operations (commit, push, PR) are handled ONLY by `mole`**.
- mole2 and mole3 focus exclusively on research tasks — they do NOT perform git operations.
- When research is complete, mole (team lead) consolidates findings and handles git workflow.

## Skill Creation Rules

- Claude Code skills MUST be created inside this repo's `.claude/skills/` directory.
- Skill file structure:
  ```
  .claude/skills/<skill-name>/
  ├── SKILL.md           # Main instruction file (required)
  ├── template.md        # Template for Claude to fill (optional)
  ├── examples/          # Example outputs (optional)
  └── scripts/           # Execution scripts (optional)
  ```
- Follow the [Claude Code Skills spec](https://code.claude.com/docs/en/skills) for SKILL.md format.
- SKILL.md frontmatter fields: `name`, `description`, `argument-hint`, `allowed-tools`, `context`, `agent`, `model`
- Skills are scoped to THIS repo only — never create skills in other repos.

## Progress Reporting Rule

- During long research tasks, MUST send intermediate progress reports to the team lead.
- Do NOT work silently for extended periods — report findings as they are discovered.
- Break down research into smaller steps and report after each step.
- Example workflow:
  1. "영상 제목/채널 정보 확인 완료" → 중간 보고
  2. "핵심 기능 파악 완료" → 중간 보고
  3. "기술 스택 확인 완료" → 중간 보고
  4. 최종 보고서 작성 → 완료 보고

## YouTube Research Guide

- When a YouTube link is provided, MUST open it in Chrome browser using `mcp__claude-in-chrome` tools.
- Use YouTube's "질문하기" (Ask Questions) feature:
  1. Open the video in Chrome.
  2. Click "질문하기" button to open the Q&A chat panel on the right side.
  3. Ask specific questions about the video content to extract precise information.
- This approach yields more accurate and detailed findings than just watching the video.
- To locate the "질문하기" button programmatically:
  - `aria-label="질문하기"`
  - CSS selector: `button[aria-label="질문하기"]`
  - The button has a star icon and the text "질문하기"
  - Use JavaScript in Chrome to find it:
    ```javascript
    document.querySelector('button[aria-label="질문하기"]')
    ```
- To locate the question input textarea:
  - CSS selector: `textarea.chatInputViewModelChatInput`
  - `aria-label="질문하기..."`
  - `placeholder="질문하기..."`
  - Use JavaScript in Chrome to find it:
    ```javascript
    document.querySelector('textarea.chatInputViewModelChatInput')
    ```
- To locate the send button:
  - CSS selector: `button[aria-label="보내기"]`
  - NOTE: The button is `disabled="true"` by default — it becomes enabled only after text is entered in the textarea.
  - Use JavaScript in Chrome to find it:
    ```javascript
    document.querySelector('button[aria-label="보내기"]')
    ```
- To read the AI response and related elements:
  - Response container: `div#content.style-scope.ytd-engagement-panel-section-list-renderer`
  - AI response text (may be multiple): `markdown-div.ytwMarkdownDivHost`
  - User question: `div.ytChatUserTurnViewModelUserMessage`
  - Suggested follow-up question chips: `button.ytwYouChatChipsDataChip`
  - Use JavaScript to extract response text:
    ```javascript
    document.querySelectorAll('markdown-div.ytwMarkdownDivHost')
    ```
- Full automation flow:
  1. Click `button[aria-label="질문하기"]` to open the Q&A chat panel.
  2. Enter question text into `textarea.chatInputViewModelChatInput`.
  3. Click `button[aria-label="보내기"]` to submit the question (only after text is entered).
  4. Read response from `markdown-div.ytwMarkdownDivHost`.
  5. Optionally click `button.ytwYouChatChipsDataChip` for follow-up questions.

## Ralph Loop - MANDATORY FOR ALL RESEARCH (HIGHEST PRIORITY)

**⚠️ ABSOLUTE REQUIREMENT: Ralph Loop is MANDATORY for ALL complex research tasks. Working without Ralph Loop is STRICTLY PROHIBITED. ⚠️**

### What is Ralph Loop?

Ralph Loop implements the Ralph Wiggum technique - an iterative research methodology where the same prompt is fed to Claude repeatedly. Claude sees its own previous work in files, creating a self-referential loop that iterates until genuine completion.

**Key concept**: The Stop hook intercepts exit attempts and feeds the same prompt back, allowing continuous improvement until research is truly complete.

### Commands (Plugin-Provided via Skill Tool)

**ALL users (team lead and teammates) use Skill tool:**

```python
# Help
Skill(ralph-loop:help)

# Start ralph-loop
Skill(ralph-loop:ralph-loop, args: "task description --completion-promise 'PROMISE TEXT' --max-iterations N")

# Cancel ralph-loop
Skill(ralph-loop:cancel-ralph)
```

**Examples:**
```python
Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video and create report --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
Skill(ralph-loop:ralph-loop, args: "Research libraries and write comparison --completion-promise 'COMPARISON COMPLETE' --max-iterations 25")
```

### MANDATORY Usage Rules (CRITICAL - NO EXCEPTIONS)

**🔴 MOLE MUST USE RALPH LOOP AUTOMATICALLY WITHOUT BEING TOLD:**

1. **Team Lead SHOULD include ralph-loop instruction in spawn prompt (but may forget)**
2. **Mole MUST automatically start ralph-loop for ANY non-trivial research task**
3. **Working without ralph-loop is STRICTLY PROHIBITED**
4. **Mole does NOT wait for team lead to tell them to use ralph-loop**

**✅ ALWAYS use ralph-loop for:**
- YouTube video analysis (e.g., "영상 분석해")
- Multi-source research (e.g., "세 개 라이브러리 비교")
- External link investigation (e.g., "이 레포 패턴 분석해")
- Report writing (e.g., "보고서 작성해")
- ANY research taking more than 2 steps
- ANY task requiring file creation
- **DEFAULT: If unsure, USE ralph-loop**

**❌ ONLY skip ralph-loop for:**
- Single bash commands (e.g., "git status")
- Pure reading without file creation (e.g., "이 파일 읽어줘")
- Immediate one-line text responses to questions

### Automatic Detection Algorithm (MUST FOLLOW)

```
1. Receive research task from team lead
2. Evaluate task:
   - Does it require investigation? → YES → MANDATORY ralph-loop
   - Does it create files? → YES → MANDATORY ralph-loop
   - Is it multi-step research? → YES → MANDATORY ralph-loop
   - Does it require YouTube/Chrome? → YES → MANDATORY ralph-loop
   - Is it a single read-only command? → NO ralph-loop (exception)
3. If ralph-loop MANDATORY → START IMMEDIATELY
4. Do NOT ask team lead for confirmation
5. Do NOT start working without ralph-loop
```

### Example Workflow (MANDATORY PATTERN)

```
Team Lead: "몰, 이 YouTube 영상 분석해: [link]"
Mole (AUTOMATICALLY):
  → Sees task: YouTube video analysis (multi-step, file-creating)
  → Recognizes this REQUIRES ralph-loop
  → IMMEDIATELY starts:
     Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video [link], extract features and tech stack, create report --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
  → Iteration 1: Open video in Chrome
  → Iteration 2: Start Q&A feature
  → Iteration 3: Extract basic info
  → Iteration 4: Ask about tech stack
  → Iteration 5: Extract implementation patterns
  → Iteration 6: Create draft report
  → Iteration 7: Refine report
  → Output: <promise>VIDEO ANALYSIS COMPLETE</promise>
  → Skill(ralph-loop:cancel-ralph)
```

### Completion Promises

**CRITICAL**: Only output `<promise>TEXT</promise>` when the statement is **completely and unequivocally TRUE**.

- ✅ DO: Verify all research is complete before outputting promise
- ❌ DON'T: Output false promises to escape the loop
- ✅ Trust the process - the loop continues until genuine completion

### Enforcement Mechanisms

**Layer 1: Spawn Prompt Injection (Team Lead)**
```
Team Lead spawns Mole with:
"CRITICAL: You MUST use ralph-loop for this research task. Start with:
Skill(ralph-loop:ralph-loop, args: '<research description> --completion-promise \"RESEARCH COMPLETE\" --max-iterations 20')"
```

**Layer 2: Mole Auto-Detection (MANDATORY SELF-ENFORCEMENT)**
```
Mole receives task → Immediately evaluate:
- Is this research? → YES → START ralph-loop
- Does this create files? → YES → START ralph-loop
- Is this multi-step? → YES → START ralph-loop
- Do NOT ask for confirmation, just START
```

**Layer 3: Team Lead Verification**
```
Team Lead checks Mole's first response:
- Did Mole start ralph-loop? → NO → IMMEDIATELY INSTRUCT TO USE IT
```

### Violation Consequences

**If Mole works without ralph-loop:**
1. Team lead IMMEDIATELY stops the work
2. Team lead INSTRUCTS Mole to restart with ralph-loop
3. Previous work may be discarded
4. This is considered a CRITICAL VIOLATION

**If team lead forgets to include ralph-loop instruction:**
1. Mole MUST still use ralph-loop automatically
2. Mole is expected to SELF-ENFORCE this rule

### Parallel Ralph Loops (mole2, mole3)

When multiple research agents are spawned:
- Each MUST start their own ralph-loop immediately
- All loops run in parallel
- No waiting for confirmation

**Example:**
```
Team Lead: "세 개 라이브러리 비교 조사"
Spawns: mole (library A), mole2 (library B), mole3 (library C)
Immediately:
  mole: Skill(ralph-loop:ralph-loop, args: "Research library A --completion-promise 'LIBRARY A COMPLETE' --max-iterations 15")
  mole2: Skill(ralph-loop:ralph-loop, args: "Research library B --completion-promise 'LIBRARY B COMPLETE' --max-iterations 15")
  mole3: Skill(ralph-loop:ralph-loop, args: "Research library C --completion-promise 'LIBRARY C COMPLETE' --max-iterations 15")
All loops run in parallel → All complete → mole consolidates and creates PR
```

### Self-Enforcement Checklist

Before starting any research task, ask yourself:
- [ ] Did team lead include ralph-loop instruction?
  - ✅ YES → Follow it exactly
  - ❌ NO → I MUST still use ralph-loop if task is complex
- [ ] Is this task multi-step or file-creating research?
  - ✅ YES → MANDATORY ralph-loop
  - ❌ NO → Only if single read-only command, skip ralph-loop
- [ ] Have I started /ralph-loop?
  - ✅ YES → Proceed
  - ❌ NO → START IT NOW before any other work

### Plugin Installation & Configuration

Ralph-loop is already configured in this repo's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "ralph-loop@claude-plugins-official": true
  }
}
```

**Verify plugin availability (for teammates):**
```
Skill(ralph-loop:help)
```

**If plugin is not available, install globally:**
```bash
npm install -g @anthropic-ai/ralph-loop
```

**See `.claude/rules/ralph-loop.md` and `.claude/rules/ralph-loop-enforcement.md` for comprehensive guides.**

## Writing Convention

- All directives and instructions in rules/CLAUDE.md MUST be written in English.
- Examples and sample user expressions MUST be written in Korean.

## Status

Greenfield — no research has been conducted yet.
