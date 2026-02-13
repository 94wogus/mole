# Ralph Loop Enforcement Rules for Mole (MANDATORY)

**⚠️ CRITICAL: This document establishes MANDATORY enforcement mechanisms for ralph-loop usage in Mole's research work. These rules are ABSOLUTE and NON-NEGOTIABLE. ⚠️**

## Core Principle

**Ralph Loop is NOT optional. It is the DEFAULT mode of operation for ALL complex research tasks.**

**Working without ralph-loop when required is a CRITICAL VIOLATION.**

## Mandatory Usage Triggers

### Automatic Ralph Loop Triggers (MUST USE)

Mole MUST automatically start ralph-loop when task involves:

1. **YouTube Video Analysis**
   - ✅ ALWAYS use ralph-loop for YouTube research
   - Example: "이 영상 분석해" → IMMEDIATE ralph-loop start

2. **Multi-Source Research**
   - ✅ ALWAYS use ralph-loop for comparing multiple libraries/frameworks/tools
   - Example: "세 개 라이브러리 비교" → IMMEDIATE ralph-loop start

3. **External Link Investigation**
   - ✅ ALWAYS use ralph-loop for analyzing repos, docs, implementation patterns
   - Example: "이 레포 패턴 분석해" → IMMEDIATE ralph-loop start

4. **Report Writing**
   - ✅ ALWAYS use ralph-loop when creating structured reports
   - Example: "보고서 작성해" → IMMEDIATE ralph-loop start

5. **File Creation Tasks**
   - ✅ ALWAYS use ralph-loop if task creates/edits files in mole/
   - Example: "findings/ 에 결과 정리해" → IMMEDIATE ralph-loop start

6. **Multi-Step Research**
   - ✅ ALWAYS use ralph-loop if task requires more than 2 steps
   - Example: "링크 열고 분석하고 요약해" → IMMEDIATE ralph-loop start

### Rare Exceptions (ONLY Skip Ralph Loop)

Ralph loop is NOT required for:

1. **Single Bash Commands**
   - Example: "git status" - single command, no ralph-loop

2. **Pure Reading Without File Creation**
   - Example: "이 파일 읽어줘" - Read tool only, no ralph-loop

3. **Immediate One-Line Text Responses**
   - Example: "mole 레포 경로가 뭐야?" - Simple answer, no ralph-loop

**IF IN DOUBT → USE RALPH LOOP. This is the DEFAULT.**

## Enforcement Layers

### Layer 1: Team Lead Spawn Prompt (SHOULD Include)

Team lead SHOULD include ralph-loop instruction when spawning Mole:

**Example spawn prompt:**
```
"Analyze this YouTube video: [link]

CRITICAL REQUIREMENT: You MUST use ralph-loop for this task.
Start immediately with:
Skill(ralph-loop:ralph-loop, args: 'Analyze YouTube video [link], extract features and tech stack, create report --completion-promise \"VIDEO ANALYSIS COMPLETE\" --max-iterations 20')

Do NOT start working without ralph-loop. This is MANDATORY."
```

**BUT REMEMBER**: Even if team lead forgets, Mole MUST still use ralph-loop.

### Layer 2: Mole Auto-Detection (MANDATORY SELF-ENFORCEMENT)

**THIS IS THE MOST IMPORTANT LAYER.**

Mole MUST evaluate EVERY task and automatically detect if ralph-loop is required.

**Auto-Detection Algorithm (FOLLOW EXACTLY):**

```
Step 1: Receive task from team lead
Step 2: Parse task requirements
Step 3: Check against triggers:
  - Is it YouTube video analysis? → YES → TRIGGER
  - Is it multi-source research? → YES → TRIGGER
  - Is it external link investigation? → YES → TRIGGER
  - Is it report writing? → YES → TRIGGER
  - Does it create files? → YES → TRIGGER
  - Is it multi-step? → YES → TRIGGER
Step 4: If ANY trigger matched → IMMEDIATELY start ralph-loop
Step 5: Do NOT ask team lead for confirmation
Step 6: Do NOT proceed with work without ralph-loop
```

**Example (CORRECT AUTO-DETECTION):**

```
Team Lead: "몰, 이 YouTube 영상 분석해: https://youtube.com/watch?v=xyz"

Mole (internal evaluation):
  → Task: YouTube video analysis
  → Check triggers:
    - Is it YouTube video analysis? YES ✓
    - Does it create files? YES (report will be created) ✓
    - Is it multi-step? YES (open, analyze, extract, report) ✓
  → RESULT: RALPH LOOP REQUIRED
  → ACTION: IMMEDIATELY start ralph-loop

Mole (first response):
  "Starting ralph-loop for video analysis."
  Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video https://youtube.com/watch?v=xyz, extract key features and tech stack, create structured report in reports/ directory --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
```

**Example (WRONG - VIOLATION):**

```
❌ Team Lead: "몰, 이 영상 분석해"
❌ Mole: "I'll open the video in Chrome and analyze it..."
   [Starts working without ralph-loop]

→ CRITICAL VIOLATION: Should have started ralph-loop immediately
→ Team Lead MUST stop and correct
```

### Layer 3: Team Lead Verification (BACKUP ENFORCEMENT)

If Mole fails to use ralph-loop:

1. Team lead checks Mole's first response
2. If no ralph-loop detected → IMMEDIATELY stop
3. Send correction message:
   ```
   "STOP. You MUST use ralph-loop for this task.
   Restart with:
   Skill(ralph-loop:ralph-loop, args: '<task description> --completion-promise \"<COMPLETION>\" --max-iterations <N>')"
   ```
4. Mole restarts with ralph-loop

**This layer is a BACKUP. Mole should NEVER reach this point.**

## Completion Promise Rules

### What is a Completion Promise?

A completion promise is the ONLY way to exit a ralph-loop successfully.

**Format:**
```
<promise>COMPLETION TEXT</promise>
```

**Example:**
```
<promise>VIDEO ANALYSIS COMPLETE</promise>
```

### CRITICAL Rules

**✅ DO:**
- Verify ALL research is genuinely complete before outputting promise
- Check that ALL files (reports, findings) are created and finalized
- Confirm all required information has been extracted
- Review findings for completeness and accuracy

**❌ DON'T:**
- Output false promises to escape the loop
- Lie about completion status
- Output promise if ANY work remains incomplete
- Give up and output promise when stuck

### Verification Checklist Before Promise

Before outputting `<promise>RESEARCH COMPLETE</promise>`:

- [ ] All external links have been investigated
- [ ] All required information has been extracted
- [ ] Report files have been created in appropriate directory (reports/ or findings/)
- [ ] Reports are complete, well-structured, and accurate
- [ ] All questions from team lead have been answered
- [ ] Findings are organized and formatted properly
- [ ] No outstanding TODOs or incomplete sections

**ONLY output promise when ALL items checked.**

## Progress Reporting During Ralph Loop

### Mandatory Interim Reports

During long ralph-loop research, Mole MUST send progress updates to team lead.

**Timing:**
- After each major milestone (link opened, Q&A started, initial findings, report draft)
- At least every 5 iterations for very long tasks
- When switching between major research phases

**Format:**
```
SendMessage to team-lead:
"<Current milestone> 완료. <Brief update on progress>. <Next step>."
```

**Example:**
```
Iteration 1: SendMessage: "Chrome에서 영상 열었습니다. Q&A 기능 활성화 준비 중."
Iteration 3: SendMessage: "Q&A 시작, 기본 정보 수집 완료. 기술 스택 조사 중."
Iteration 7: SendMessage: "기술 스택 파악 완료. 보고서 초안 작성 중."
Iteration 10: SendMessage: "보고서 초안 완성. 최종 리뷰 진행 중."
```

**Why this is MANDATORY:**
- Provides visibility into research progress
- Prevents silent long-running work
- Allows team lead to provide guidance if needed
- Demonstrates iterative progress

## Parallel Ralph Loops (mole2, mole3)

### Multi-Agent Research Coordination

When team lead spawns multiple research agents:

| Agent | Role | Ralph Loop | Git Operations |
|-------|------|------------|----------------|
| **mole** | Research + coordination + git | ✅ YES | ✅ YES (only mole) |
| **mole2** | Research only | ✅ YES | ❌ NO |
| **mole3** | Research only | ✅ YES | ❌ NO |

### Parallel Loop Rules

1. **Each agent starts their own ralph-loop immediately**
   - No waiting for others
   - No asking for confirmation
   - All loops run in parallel

2. **Each agent works independently**
   - mole: Research topic A
   - mole2: Research topic B
   - mole3: Research topic C

3. **Each agent reports progress to team lead**
   - Send interim updates during loops
   - Report completion when done

4. **Only mole handles git operations**
   - mole2 and mole3 create findings/reports in mole/ directory
   - mole consolidates all findings
   - mole creates branch, commits, pushes, creates PR

### Example Parallel Workflow

```
Task: "세 개 React 차트 라이브러리 비교: Recharts, Victory, Nivo"

Team Lead spawns:
  mole: "Research Recharts"
  mole2: "Research Victory"
  mole3: "Research Nivo"

Each agent IMMEDIATELY:
  mole: Skill(ralph-loop:ralph-loop, args: "Research Recharts features, performance, ecosystem --completion-promise 'RECHARTS COMPLETE' --max-iterations 15")
  mole2: Skill(ralph-loop:ralph-loop, args: "Research Victory features, performance, ecosystem --completion-promise 'VICTORY COMPLETE' --max-iterations 15")
  mole3: Skill(ralph-loop:ralph-loop, args: "Research Nivo features, performance, ecosystem --completion-promise 'NIVO COMPLETE' --max-iterations 15")

All loops run in parallel:
  mole: findings/recharts-research.md
  mole2: findings/victory-research.md
  mole3: findings/nivo-research.md

All complete:
  mole2 → team-lead: "Victory research complete"
  mole3 → team-lead: "Nivo research complete"
  mole: Consolidates all findings → reports/chart-libraries-comparison.md
  mole: Git workflow (branch, commit, push, PR)
```

## Violation Scenarios and Corrections

### Violation 1: Starting Work Without Ralph Loop

**Scenario:**
```
Team Lead: "몰, 이 YouTube 영상 분석해"
Mole: "I'll open the video and extract information..."
```

**Problem**: Mole started working without ralph-loop

**Correction:**
```
Team Lead: "STOP. You MUST use ralph-loop for video analysis.
Restart with:
Skill(ralph-loop:ralph-loop, args: 'Analyze YouTube video --completion-promise \"VIDEO ANALYSIS COMPLETE\" --max-iterations 20')"

Mole: "Understood. Starting ralph-loop now."
Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video [link]... --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
```

### Violation 2: Outputting False Promise

**Scenario:**
```
Mole (Iteration 8): "I'm stuck, can't extract more info. Let me just finish..."
<promise>VIDEO ANALYSIS COMPLETE</promise>

[But report is incomplete, key info missing]
```

**Problem**: Mole output false promise to escape loop

**Correction:**
```
Team Lead: "The report is incomplete. Key sections are missing.
Why did you output the completion promise?"

Mole: "I apologize. I should not have output a false promise.
The research is not actually complete. I will continue iterating to gather the missing information."

[Loop should have continued automatically, but if cancel was called:]

Mole: Skill(ralph-loop:ralph-loop, args: "Continue YouTube video analysis, focus on missing sections --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 10")
```

### Violation 3: Not Reporting Progress

**Scenario:**
```
Mole: Skill(ralph-loop:ralph-loop, args: "Research libraries --max-iterations 25")
[Iterations 1-15 pass in silence, no progress reports]
```

**Problem**: No interim progress reports during long research

**Correction:**
```
Team Lead: "몰, 진행 상황 보고해. 어디까지 했어?"

Mole: "Currently on iteration 12. I've completed research on Library A and B.
Now working on Library C. Will report after each library going forward."

[Should have been sending progress reports automatically]
```

## Best Practices

### 1. Default to Ralph Loop

**When in doubt, use ralph-loop.**

- If task seems simple but might need iteration → Use ralph-loop
- If task might create files → Use ralph-loop
- If unsure if it's multi-step → Use ralph-loop

**Overusing ralph-loop is BETTER than underusing it.**

### 2. Set Appropriate Max Iterations

**Research tasks typically need:**
- Simple research: 10-15 iterations
- YouTube video analysis: 15-20 iterations
- Multi-source research: 20-30 iterations
- Complex report writing: 20-25 iterations

**Always set max-iterations to prevent infinite loops.**

### 3. Use Clear, Research-Specific Promises

**GOOD:**
```
"VIDEO ANALYSIS COMPLETE"
"LIBRARY COMPARISON COMPLETE"
"PATTERN EXTRACTION COMPLETE"
"REPORT WRITTEN AND REVIEWED"
```

**BAD:**
```
"done"
"finished"
"maybe complete"
```

### 4. Leverage Iteration History

Each iteration sees previous work:
- Read your own draft reports and refine them
- Review findings files and add missing details
- Build on previous Q&A results
- Continuously improve report structure and clarity

### 5. Trust the Process

**Don't fight the loop:**
- If you're stuck, analyze what's blocking you
- Ask better questions, try different approaches
- Refine your research strategy
- Trust max-iterations to stop if truly stuck

**Don't escape the loop:**
- Never output false promises
- Never give up on research prematurely
- The loop is designed to help you iterate to completion

## Integration with Git Workflow

### Research + Git Flow

1. **Start ralph-loop for research**
2. **Loop creates findings/reports iteratively**
3. **Loop outputs completion promise when done**
4. **/cancel-ralph**
5. **Only mole (not mole2/mole3) handles git:**
   - Create branch: `git checkout -b wogus/ARASEO-XXX`
   - Commit: `git commit -m "Add research findings"`
   - Push: `GITHUB_TOKEN= git push -u origin wogus/ARASEO-XXX`
   - Create PR: `gh pr create ...`

**Example:**
```
Skill(ralph-loop:ralph-loop, args: "YouTube video analysis --completion-promise 'ANALYSIS COMPLETE' --max-iterations 20")
[Iterations proceed, report created]
<promise>ANALYSIS COMPLETE</promise>

# After loop completes:
git checkout -b wogus/ARASEO-456
git add reports/youtube-video-analysis.md
git commit -m "Add YouTube video analysis report

- Analyzed video: [title]
- Extracted key features and tech stack
- Documented implementation patterns
- Recommendations for Araseo project"
GITHUB_TOKEN= git push -u origin wogus/ARASEO-456
gh pr create --title "Add YouTube video analysis" --body "..."
```

## Commands (Accessed via Skill Tool)

**ALL users (team lead, teammates, everyone) access ralph-loop via Skill tool:**

```python
# Help
Skill(ralph-loop:help)

# Start ralph-loop
Skill(ralph-loop:ralph-loop, args: "task description --completion-promise 'PROMISE TEXT' --max-iterations N")

# Cancel ralph-loop
Skill(ralph-loop:cancel-ralph)
```

**Example:**
```python
Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video and create report --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
```

## Plugin Installation & Configuration

### Current Configuration

Ralph-loop plugin is already configured in `/Users/wogus/Wogus/Araseo/mole/.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "ralph-loop@claude-plugins-official": true
  }
}
```

### Verify Plugin Availability

Check if ralph-loop plugin is available:

```
Skill(ralph-loop:help)
```

This should show ralph-loop command help. If it works, the plugin is properly configured.

### Installation (If Needed)

If ralph-loop commands are not available, install the plugin globally:

```bash
npm install -g @anthropic-ai/ralph-loop
```

After installation, verify the plugin is enabled in `.claude/settings.json` with the format shown above.

## Troubleshooting

### Problem: Ralph Loop Plugin Not Available

**Symptom:**
```
Skill(ralph-loop:help)
Error: Unknown skill: ralph-loop
```

**Diagnosis:**
- Ralph loop is a **plugin**, not a built-in command
- Plugin may not be installed or not configured in settings.json
- Plugin configuration may be incorrect

**Solution:**
1. **Check plugin configuration:**
   ```bash
   cat /Users/wogus/Wogus/Araseo/mole/.claude/settings.json
   ```
   Should show:
   ```json
   {
     "enabledPlugins": {
       "ralph-loop@claude-plugins-official": true
     }
   }
   ```

3. **Install plugin if missing:**
   ```bash
   npm install -g @anthropic-ai/ralph-loop
   ```

4. **Verify installation:**
   ```
   Skill(ralph-loop:help)
   ```

5. **IMMEDIATELY report to team lead if issues persist:**
   ```
   "CRITICAL ERROR: ralph-loop plugin is not available in my environment.
   - Checked settings.json: Plugin is configured correctly
   - Attempted npm install: [result]
   - Plugin verification: [result]
   I CANNOT proceed without ralph-loop as it is MANDATORY for this task."
   ```

5. **If plugin truly unavailable after all attempts, work in degraded mode (LAST RESORT):**
   - Break task into smallest possible steps
   - Complete one step
   - Self-review and critique
   - Proceed to next step
   - Report progress after each step
   - Continue until complete

**Degraded mode example:**
```
Task: "YouTube video analysis"

Manual Iteration 1:
  - Open video in Chrome
  - Self-review: Did it open? Is Q&A available?
  - Report: "Video opened, Q&A ready"

Manual Iteration 2:
  - Ask initial questions
  - Self-review: Are answers complete?
  - Report: "Basic info extracted"

[Continue until complete]
```

### Problem: Loop Running Too Long

**Symptom:**
- Loop exceeds expected iterations
- No new progress being made

**Diagnosis:**
- Completion criteria might be too strict
- Stuck on a blocker (link not working, Q&A not responding)
- Not actually making progress (reading same info repeatedly)

**Solution:**
1. **Check iteration progress:**
   ```bash
   head -20 /Users/wogus/Wogus/Araseo/mole/.claude/.ralph-loop.local.md
   ```

2. **Review recent file changes:**
   ```bash
   git diff
   ```
   Are files actually changing each iteration?

3. **Analyze blocker:**
   - Is external link accessible?
   - Is Chrome responding?
   - Is Q&A feature working?

4. **Trust max-iterations:**
   - Loop will stop automatically
   - User can interrupt with /cancel-ralph if needed

### Problem: Can't Extract Complete Information

**Symptom:**
- Research seems incomplete
- Can't figure out what's missing

**Solution:**
1. **Re-read task requirements:**
   - What did team lead ask for?
   - Are all aspects covered?

2. **Review completion checklist:**
   - Features extracted? ✓/✗
   - Tech stack identified? ✓/✗
   - Implementation patterns analyzed? ✓/✗
   - Report written? ✓/✗
   - Recommendations included? ✓/✗

3. **Try different approaches:**
   - Ask broader questions in Q&A
   - Search for additional sources
   - Read documentation more thoroughly

4. **Send progress report and ask for guidance:**
   ```
   SendMessage to team-lead:
   "Iteration 12. Extracted features and tech stack.
   Having difficulty finding implementation patterns.
   Should I focus on different aspects or continue digging?"
   ```

## Summary

### Golden Rules for Mole

1. **Ralph loop is MANDATORY for complex research**
2. **Auto-detect and start ralph-loop WITHOUT being told**
3. **Never work without ralph-loop when required**
4. **Only output completion promise when genuinely complete**
5. **Send progress reports during long research**
6. **Trust the process, don't fight the loop**

### Quick Reference: When to Use Ralph Loop

| Task Type | Ralph Loop | Reason |
|-----------|------------|--------|
| YouTube video analysis | ✅ MANDATORY | Multi-step, file creation |
| Multi-source research | ✅ MANDATORY | Multi-step, file creation |
| External link investigation | ✅ MANDATORY | Multi-step, file creation |
| Report writing | ✅ MANDATORY | Iterative refinement |
| Library comparison | ✅ MANDATORY | Multi-step, file creation |
| Pattern extraction | ✅ MANDATORY | Multi-step, file creation |
| Single bash command | ❌ Skip | One-shot operation |
| Pure file reading | ❌ Skip | No file creation |
| Quick question answer | ❌ Skip | Immediate response |

### Default Rule

**If in doubt → USE RALPH LOOP.**

Ralph loop is the DEFAULT mode of operation for research work.
