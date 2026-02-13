---
name: youtube-research
description: Research YouTube videos using Chrome browser and YouTube's Q&A feature
---

# YouTube Research Skill

Investigate a YouTube video using Chrome browser and YouTube's "질문하기" (Ask Questions) AI feature. Extract detailed information and produce a structured research report.

## Input

- `$ARGUMENTS`: A YouTube video URL (e.g., `https://www.youtube.com/watch?v=XXXX`)

## Required Tools

This skill uses the following tools:

- `mcp__claude-in-chrome__navigate`
- `mcp__claude-in-chrome__computer`
- `mcp__claude-in-chrome__javascript_tool`
- `mcp__claude-in-chrome__tabs_context_mcp`
- `mcp__claude-in-chrome__tabs_create_mcp`
- `mcp__claude-in-chrome__read_page`
- `mcp__claude-in-chrome__get_page_text`
- `Read`
- `Write`
- `SendMessage`

## Workflow

### Step 1: Open Video in Chrome

1. Get available tabs using `mcp__claude-in-chrome__tabs_context_mcp` (create if empty).
2. Navigate to the YouTube URL using `mcp__claude-in-chrome__navigate`.
3. Wait 5 seconds for the page to fully load.
4. Take a screenshot to confirm the video page loaded correctly.

**Report to team lead**: "영상 페이지 로딩 완료. 제목: [title], 채널: [channel]"

### Step 2: Extract Basic Video Information

Use `mcp__claude-in-chrome__get_page_text` to extract:

- Video title
- Channel name and subscriber count
- Upload date
- View count
- Video description (contains chapters, links, and summary)

**Report to team lead**: "영상 기본 정보 확인 완료"

### Step 3: Open the Q&A Chat Panel

Click the "질문하기" button to open the AI Q&A panel on the right side.

**Method A — JavaScript (preferred):**

```javascript
const btn = document.querySelector('button[aria-label="질문하기"]');
if (btn) btn.click();
```

**Method B — Click by coordinates:**
Use `mcp__claude-in-chrome__computer` with `action: left_click` on the "질문하기" button (star icon, located in the action bar below the video).

Wait 3 seconds for the panel to open. Take a screenshot to confirm.

### Step 4: Ask Questions

For each question:

#### 4a. Enter question text

**Method A — JavaScript (preferred):**

```javascript
const textarea = document.querySelector("textarea.chatInputViewModelChatInput");
if (textarea) {
  textarea.focus();
  textarea.value = "YOUR QUESTION HERE";
  textarea.dispatchEvent(new Event("input", { bubbles: true }));
}
```

**Method B — Click and type:**

1. Click on the textarea (`textarea.chatInputViewModelChatInput`).
2. Use `mcp__claude-in-chrome__computer` with `action: type` to enter the question.

#### 4b. Submit the question

**Method A — JavaScript (preferred):**

```javascript
const sendBtn = document.querySelector('button[aria-label="보내기"]');
if (sendBtn && !sendBtn.disabled) sendBtn.click();
```

**Method B — Click:**
Click `button[aria-label="보내기"]`. NOTE: This button is `disabled` by default and only becomes enabled after text is entered in the textarea.

#### 4c. Wait for response

Wait 10-15 seconds for the AI to generate a response.

#### 4d. Read the response

**Method A — JavaScript (preferred):**

```javascript
const responses = document.querySelectorAll("markdown-div.ytwMarkdownDivHost");
const lastResponse = responses[responses.length - 1];
lastResponse ? lastResponse.innerText : "No response found";
```

**Method B — get_page_text:**
Use `mcp__claude-in-chrome__get_page_text` to extract all page text including Q&A responses.

**Method C — Screenshot:**
Take a screenshot and scroll through the response panel to read visually.

### Step 5: Recommended Questions Sequence

Ask questions in this order, reporting to the team lead after each:

1. **Overview question**: "Summarize the entire video in detail. Include the presenter, main topic, key points, and demos shown."
   - **Report**: "영상 전체 요약 확인 완료"

2. **Technical details**: "What is the exact technical architecture, tech stack, and implementation approach discussed?"
   - **Report**: "기술 스택/구현 방식 확인 완료"

3. **Specific topic deep-dive** (adapt to the video content): Ask about specific features, tools, or concepts mentioned.
   - **Report**: "핵심 기능 상세 파악 완료"

4. **Practical takeaways**: "What are the key actionable takeaways and lessons learned?"
   - **Report**: "핵심 포인트 정리 완료"

5. **Follow-up questions** using suggested chips (`button.ytwYouChatChipsDataChip`) if relevant.

### Step 6: Compile Research Report

Write a structured markdown report to `mole/reports/<topic-name>.md` with:

```markdown
# [Topic] Research Report

## Source

- Title, Channel, Presenter, URL, Date, Duration

## 1. [Main Topic Summary]

## 2. [Technical Details]

## 3. [Key Features / Demos]

## 4. [Takeaways for Araseo Project]

## 5. [Reference Materials]

---

_Report generated: [date]_
_Researcher: Mole (몰)_
```

### Step 7: Final Report to Team Lead

Send a complete summary to the team lead via `SendMessage`, including:

- File path of the report
- Key findings summary (bullet points)
- Relevance to Araseo project

## CSS Selectors Reference

| Element            | Selector                                                             | Notes                       |
| ------------------ | -------------------------------------------------------------------- | --------------------------- |
| "질문하기" button  | `button[aria-label="질문하기"]`                                      | Star icon in action bar     |
| Question input     | `textarea.chatInputViewModelChatInput`                               | `placeholder="질문하기..."` |
| Send button        | `button[aria-label="보내기"]`                                        | Disabled until text entered |
| AI response text   | `markdown-div.ytwMarkdownDivHost`                                    | May have multiple elements  |
| User question      | `div.ytChatUserTurnViewModelUserMessage`                             |                             |
| Follow-up chips    | `button.ytwYouChatChipsDataChip`                                     | Suggested questions         |
| Response container | `div#content.style-scope.ytd-engagement-panel-section-list-renderer` |                             |

## Rules

- MUST send intermediate progress reports to the team lead after each major step.
- Do NOT work silently for extended periods.
- If a selector fails, fall back to screenshot-based visual interaction.
- If the "질문하기" feature is unavailable, use `get_page_text` to extract video description and comments as an alternative.
- All reports MUST be saved in `mole/reports/` directory.
