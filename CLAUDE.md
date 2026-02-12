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

## Writing Convention

- All directives and instructions in rules/CLAUDE.md MUST be written in English.
- Examples and sample user expressions MUST be written in Korean.

## Status

Greenfield — no research has been conducted yet.
