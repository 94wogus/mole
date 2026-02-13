# Ralph Loop Rules for Research & Investigation (MANDATORY)

**⚠️ CRITICAL: Ralph Loop is MANDATORY for ALL complex research tasks. This is NOT optional. ⚠️**

## What is Ralph Loop?

Ralph Loop implements the **Ralph Wiggum technique** - an iterative research methodology based on continuous AI loops, pioneered by Geoffrey Huntley.

**Core concept**: The same prompt is fed to Claude repeatedly. Claude sees its own previous work in the files, creating a self-referential loop where it iteratively improves until completion.

**Each iteration**:
1. Claude receives the SAME prompt
2. Works on the task, modifying files
3. Tries to exit
4. Stop hook intercepts and feeds the same prompt again
5. Claude sees its previous work in the files
6. Iteratively improves until completion

## MANDATORY Usage Rules - When Ralph Loop is REQUIRED

**🔴 ALWAYS USE RALPH LOOP (DEFAULT FOR ALL WORK):**
- **YouTube video analysis** - MANDATORY for all video research tasks
- **Multi-source research** - MANDATORY when comparing multiple libraries/frameworks/tools
- **External link investigation** - MANDATORY for analyzing repos, docs, implementation patterns
- **Report writing** - MANDATORY for creating structured findings documents
- **Pattern extraction** - MANDATORY for identifying patterns across multiple references
- **File creation tasks** - MANDATORY if task creates/edits files in mole/ directory
- **Multi-step research** - MANDATORY if task requires more than 2 steps
- **DEFAULT RULE: If unsure, USE ralph-loop**

**✅ ONLY skip ralph-loop for (RARE EXCEPTIONS):**
- **Single bash commands** - e.g., "git status", "ls" (one-shot operations)
- **Pure reading without file creation** - e.g., "이 파일 읽어줘" (Read tool only)
- **Immediate one-line text responses** - e.g., "mole 레포 경로가 뭐야?" (simple answer)
- **Already inside an active ralph-loop** - don't nest loops

**⚠️ TEAMMATES MUST AUTO-DETECT AND USE RALPH LOOP:**
- Do NOT wait for team lead to tell you
- Automatically evaluate if task needs ralph-loop
- If task creates/edits files → MANDATORY ralph-loop
- If task is multi-step → MANDATORY ralph-loop
- **Self-enforcement is REQUIRED**

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

**Examples:**
```python
Skill(ralph-loop:ralph-loop, args: "YouTube 영상 분석 후 상세 보고서 작성 --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")

Skill(ralph-loop:ralph-loop, args: "세 가지 차트 라이브러리 비교 조사 --completion-promise 'COMPARISON COMPLETE' --max-iterations 25")

Skill(ralph-loop:ralph-loop, args: "외부 링크 분석 및 구현 패턴 추출 --completion-promise 'PATTERN ANALYSIS COMPLETE' --max-iterations 15")
```

### Command Options

- `--max-iterations <n>` - Max iterations before auto-stop (prevents infinite loops)
- `--completion-promise <text>` - Promise phrase to signal completion

### How It Works

1. Creates `.claude/.ralph-loop.local.md` state file
2. Mole works on research task
3. When trying to exit, stop hook intercepts
4. Same prompt fed back
5. Mole sees previous research findings
6. Continues until promise detected or max iterations

## Completion Promises

To signal completion, MUST output a `<promise>` tag:

```
<promise>RESEARCH COMPLETE</promise>
```

**CRITICAL RULES:**
- ✅ Only output the promise when research is **completely and unequivocally COMPLETE**
- ❌ Do NOT output false promises to escape the loop
- ❌ Do NOT lie even if stuck
- ✅ Trust the process - the loop is designed to continue until genuine completion

## Automatic Detection and Self-Enforcement (CRITICAL)

**🔴 MOLE MUST USE RALPH LOOP AUTOMATICALLY WITHOUT BEING TOLD:**

### Auto-Detection Algorithm (FOLLOW EXACTLY)

```
Step 1: Receive task from team lead
Step 2: Evaluate task requirements
Step 3: Check against MANDATORY triggers:
  - Is it YouTube video analysis? → YES → TRIGGER
  - Is it multi-source research? → YES → TRIGGER
  - Is it external link investigation? → YES → TRIGGER
  - Is it report writing? → YES → TRIGGER
  - Does it create files? → YES → TRIGGER
  - Is it multi-step (>2 steps)? → YES → TRIGGER
Step 4: If ANY trigger matched → IMMEDIATELY start ralph-loop
Step 5: Do NOT ask team lead for confirmation
Step 6: Do NOT proceed with work without ralph-loop
```

### Example (CORRECT AUTO-DETECTION)

```
Team Lead: "몰, 이 YouTube 영상 분석해: https://youtube.com/watch?v=xyz"

Mole (internal evaluation):
  → Task: YouTube video analysis
  → Check triggers:
    - YouTube video analysis? YES ✓
    - Creates files? YES (report) ✓
    - Multi-step? YES (open, Q&A, extract, report) ✓
  → RESULT: RALPH LOOP REQUIRED
  → ACTION: IMMEDIATELY start ralph-loop

Mole (first response):
  "Starting ralph-loop for video analysis."
  Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video, extract features and tech stack, create report --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
```

### Example (WRONG - VIOLATION)

```
❌ Team Lead: "몰, 이 영상 분석해"
❌ Mole: "I'll open the video and analyze it..."
   [Starts working without ralph-loop]

→ CRITICAL VIOLATION
→ Team Lead MUST stop and correct immediately
```

## Mole Research Workflow with Ralph Loop

### Step 1: Receive Research Task

Team lead assigns an investigation task with external links or topics.

**Example:**
```
Team Lead: "몰, 이 YouTube 영상 분석해서 보고서 작성: [link]"
```

### Step 2: Auto-Detect and Start Ralph Loop (AUTOMATICALLY)

**IMMEDIATELY** analyze task requirements and start Ralph Loop.

**DO NOT:**
- Wait for explicit ralph-loop instruction
- Ask if ralph-loop is needed
- Start working without ralph-loop

**Example:**
```
Mole (AUTOMATIC RESPONSE):
  Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video [link], extract key features, tech stack, and implementation patterns. Create structured report in reports/ directory. --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")
```

### Step 3: Iterative Research

**Iteration 1**: Open YouTube link in Chrome, activate Q&A feature
**Iteration 2**: Ask initial questions, extract basic info (title, channel, topic)
**Iteration 3**: Ask follow-up questions about tech stack
**Iteration 4**: Extract implementation patterns
**Iteration 5**: Create draft report structure
**Iteration 6**: Fill in findings
**Iteration 7**: Refine and organize report
**Iteration 8**: Final review, output completion promise

### Step 4: Progress Reporting

During long research tasks, send intermediate reports to team lead:

**Example:**
```
SendMessage to team-lead:
"영상 기본 정보 확인 완료 - 제목: [title], 채널: [channel]
현재 Q&A를 통해 기술 스택 조사 중"
```

### Step 5: Completion

Output completion promise and report final findings to team lead.

**Example:**
```
<promise>VIDEO ANALYSIS COMPLETE</promise>

SendMessage to team-lead:
"YouTube 영상 분석 완료.
보고서 위치: /Users/wogus/Wogus/Araseo/mole/reports/video-analysis-[date].md
핵심 발견: [요약]"
```

## Best Practices for Mole Research

### 1. Always Set Max Iterations for Research

**DO:**
```python
Skill(ralph-loop:ralph-loop, args: "영상 분석 --max-iterations 20")
```

**DON'T:**
```python
Skill(ralph-loop:ralph-loop, args: "영상 분석")  # Can run forever!
```

### 2. Use Clear Research-Specific Completion Promises

**GOOD:**
```
--completion-promise "VIDEO ANALYSIS COMPLETE"
--completion-promise "LIBRARY COMPARISON COMPLETE"
--completion-promise "PATTERN EXTRACTION COMPLETE"
--completion-promise "REPORT WRITTEN"
```

**BAD:**
```
--completion-promise "done"  # Too vague
--completion-promise "probably finished"  # Not definitive
```

### 3. Structure Research Before Completion Promise

Before outputting `<promise>RESEARCH COMPLETE</promise>`:
- ✅ Verify all external links have been investigated
- ✅ Confirm report files are created in `reports/` or `findings/`
- ✅ Check that all required information has been extracted
- ✅ Review findings for completeness and clarity
- ✅ Ensure findings are organized and formatted properly

### 4. Send Intermediate Progress Reports

During long research loops:
- Report after each major step (link opened, Q&A started, first insights gathered)
- Don't work silently - team lead needs visibility
- Use SendMessage to update team lead on progress

**Example workflow:**
```
Iteration 1: "Chrome에서 영상 열었습니다"
Iteration 3: "Q&A 시작, 기본 정보 수집 완료"
Iteration 7: "기술 스택 파악 완료, 보고서 작성 중"
Iteration 10: "보고서 초안 완성, 리뷰 중"
```

### 5. Leverage Previous Iterations

Each iteration sees previous work:
- Read your own draft reports and refine them
- Review your own findings files and add missing details
- Check your own notes and expand on them
- Build on previous Q&A results to ask better follow-up questions

### 6. Don't Fight the Loop - Use It

If stuck in a loop:
- ❌ Don't output false promises
- ✅ Analyze what information is still missing
- ✅ Ask better questions to fill gaps
- ✅ Refine report structure for clarity
- ✅ Trust max-iterations to stop if needed

## Examples

### Example 1: YouTube Video Research

```
Team Lead: "몰, 이 영상 분석해: https://youtube.com/watch?v=..."
Mole: Skill(ralph-loop:ralph-loop, args: "Analyze YouTube video, extract key features and tech stack --completion-promise 'VIDEO ANALYSIS COMPLETE' --max-iterations 20")

Iteration 1: Open video in Chrome using mcp__claude-in-chrome tools
Iteration 2: Click "질문하기" button, start Q&A
Iteration 3: Ask "이 영상의 주요 내용은 무엇인가요?"
Iteration 4: Extract basic info (title, channel, topic)
Iteration 5: Ask "사용된 기술 스택은 무엇인가요?"
Iteration 6: Extract tech stack info
Iteration 7: Ask "구현 방법에 대해 자세히 알려주세요"
Iteration 8: Extract implementation patterns
Iteration 9: Create report file in reports/
Iteration 10: Write findings to report
Iteration 11: Review and refine report
Iteration 12: Add recommendations section
Iteration 13: Final review, output <promise>VIDEO ANALYSIS COMPLETE</promise>
```

### Example 2: Multi-Library Comparison Research

```
Team Lead: "몰, React 차트 라이브러리 3개 비교 조사: Recharts, Victory, Nivo"
Mole: Skill(ralph-loop:ralph-loop, args: "Compare three React chart libraries: features, performance, ease of use --completion-promise 'LIBRARY COMPARISON COMPLETE' --max-iterations 25")

Iteration 1: Research Recharts documentation
Iteration 2: Extract Recharts key features
Iteration 3: Research Victory documentation
Iteration 4: Extract Victory key features
Iteration 5: Research Nivo documentation
Iteration 6: Extract Nivo key features
Iteration 7: Create comparison table structure
Iteration 8: Fill in feature comparison
Iteration 9: Research performance benchmarks
Iteration 10: Add performance data to comparison
Iteration 11: Research community and ecosystem
Iteration 12: Add ecosystem data
Iteration 13: Create findings document
Iteration 14: Write recommendations
Iteration 15: Final review, output <promise>LIBRARY COMPARISON COMPLETE</promise>
```

### Example 3: Pattern Extraction from External Link

```
Team Lead: "몰, 이 구현 패턴 분석해: [GitHub repo link]"
Mole: Skill(ralph-loop:ralph-loop, args: "Analyze implementation patterns in repo --completion-promise 'PATTERN ANALYSIS COMPLETE' --max-iterations 15")

Iteration 1: Clone or browse repo
Iteration 2: Identify main file structure
Iteration 3: Extract architecture patterns
Iteration 4: Identify design patterns used
Iteration 5: Extract naming conventions
Iteration 6: Analyze code organization
Iteration 7: Create pattern summary document
Iteration 8: Add code examples
Iteration 9: Write recommendations for Araseo project
Iteration 10: Final review, output <promise>PATTERN ANALYSIS COMPLETE</promise>
```

## Parallel Research with mole2 and mole3

When team lead spawns multiple research agents (mole, mole2, mole3):
- Each can run their own Ralph Loop independently
- mole2 and mole3 focus on research ONLY (no git operations)
- mole (team lead) consolidates findings and handles git workflow

**Example:**
```
Team Lead spawns:
- mole: Skill(ralph-loop:ralph-loop, args: "Research library A --completion-promise 'LIBRARY A COMPLETE' --max-iterations 15")
- mole2: Skill(ralph-loop:ralph-loop, args: "Research library B --completion-promise 'LIBRARY B COMPLETE' --max-iterations 15")
- mole3: Skill(ralph-loop:ralph-loop, args: "Research library C --completion-promise 'LIBRARY C COMPLETE' --max-iterations 15")

All loops run in parallel until each completes.
Then mole consolidates findings and creates PR.
```

## Integration with Git Workflow

Ralph Loop works well with research + git workflow:

1. Start Ralph Loop for research task
2. Loop investigates, extracts findings, creates report files
3. Loop iterates until research is complete
4. Loop outputs completion promise
5. /cancel-ralph
6. Create git branch, commit findings, create PR

**Example:**
```
Skill(ralph-loop:ralph-loop, args: "Comprehensive YouTube video analysis --completion-promise 'ANALYSIS COMPLETE' --max-iterations 20")

# After loop completes:
git checkout -b wogus/ARASEO-123
git add reports/video-analysis.md
git commit -m "Add YouTube video analysis report"
GITHUB_TOKEN= git push -u origin wogus/ARASEO-123
gh pr create --title "..." --body "..."
```

## Monitoring Ralph Loop

Check loop status:
```bash
head -10 /Users/wogus/Wogus/Araseo/mole/.claude/.ralph-loop.local.md
```

This shows:
- Current iteration number
- Max iterations
- Completion promise
- Original prompt

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

If `/ralph-loop` commands are not available, install the plugin globally:

```bash
npm install -g @anthropic-ai/ralph-loop
```

After installation, verify the plugin is enabled in `.claude/settings.json` with the format shown above.

### Configuration Format

**CORRECT format:**
```json
{
  "enabledPlugins": {
    "ralph-loop@claude-plugins-official": true
  }
}
```

**NOT a `plugins` array - use the `enabledPlugins` object format.**

## Troubleshooting

### Plugin Not Available

**Symptom:**
```
/ralph-loop "task"
Error: Unknown command: ralph-loop
```

**Solutions:**

1. **Check plugin configuration:**
   ```bash
   cat /Users/wogus/Wogus/Araseo/mole/.claude/settings.json
   ```
   Should show `enabledPlugins` with `ralph-loop@claude-plugins-official: true`

2. **Install plugin if missing:**
   ```bash
   npm install -g @anthropic-ai/ralph-loop
   ```

3. **Verify installation:**
   ```
   Skill(ralph-loop:help)
   ```

4. **Report to team lead if issues persist:**
   ```
   "CRITICAL: /ralph-loop plugin is not available.
   Checked settings.json - plugin is configured.
   Attempted npm install - still not working.
   Need assistance to resolve plugin availability."
   ```

### Loop Running Too Long

**Symptom**: Ralph Loop exceeds expected iterations for research

**Solutions**:
- Check if you're actually discovering new information each iteration
- Verify you're refining the report (not just re-reading the same info)
- Check for blockers (links not opening, Q&A not working)
- Trust max-iterations to stop eventually

### Can't Extract Complete Information

**Symptom**: Research seems incomplete but can't figure out what's missing

**Solution**:
- Re-read previous findings in files
- Ask broader questions in Q&A
- Check if you've covered all aspects (features, tech stack, patterns, recommendations)
- Review completion criteria in original prompt

### Accidental Loop Start

**Symptom**: Started loop by mistake

**Solution**:
- User can run `/cancel-ralph` to stop
- Or let max-iterations run out

## Learn More

- Original technique: https://ghuntley.com/ralph/
- Ralph Orchestrator: https://github.com/mikeyobrien/ralph-orchestrator
