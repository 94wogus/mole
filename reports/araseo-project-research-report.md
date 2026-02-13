# Araseo Project Research Report

Comprehensive research report combining findings from all three team members.

- **Mole (몰)**: YouTube video analysis of Flowy — architecture, demos, and key insights
- **Sugiri (스기리)**: Flowy deep dive, comparable tools survey, Claude Code skill structure
- **Nobi (노비)**: Input pattern analysis, parsing challenges, expected output formats

---

## 1. Project Background

### Inspiration Source

Araseo is inspired by CJ Hess's **Flowy** tool, demonstrated on the **"How I AI"** podcast hosted by Claire Vo.

| Field | Details |
|-------|---------|
| Video Title | DIY dev tools: How this engineer created "Flowy" to visualize his plans and accelerate coding |
| Channel | How I AI (Claire Vo, 6.92만 subscribers) |
| Presenter | CJ Hess, Software Engineer at Tenex |
| URL | https://www.youtube.com/watch?v=LC1mKvLWZ2E |
| Published | 2026-02-09 |
| Duration | 53:07 |

### Core Idea

CJ Hess built a personal tool that **transforms JSON files into interactive flowcharts and UI mockups**, replacing the traditional ASCII/Mermaid diagram workflow. The tool integrates with Claude Code through custom skills, creating a bidirectional feedback loop between human and AI.

### What Araseo Adds

While Flowy starts from **Claude-generated JSON**, Araseo starts from **markdown/ASCII diagram content** produced during AI conversations. This means Araseo needs an additional **parser layer** to convert unstructured conversation output into the structured JSON that the renderer consumes.

```
[Flowy Pipeline]
Claude Code → JSON (via skill) → Flowy Renderer → Interactive Visual

[Araseo Pipeline]
AI Conversation → Markdown/ASCII Diagrams → Parser → JSON → Renderer → Interactive Visual
```

---

## 2. Flowy — Detailed Analysis

### 2.1 What is Flowy?

Flowy is a **custom-built visualization tool** developed by CJ Hess. It transforms JSON files into interactive visual mockups and flowcharts.

**Key Characteristics:**
- Local web application running at `localhost:4242`
- Both a **viewer and editor** (bidirectional sync)
- Changes in the visual editor save back to the JSON file; Claude reads the updated JSON natively
- Two main modes: **Flowchart rendering** and **UI mockup rendering**
- Integrated with Claude Code through custom skills (`/flowy-flowchart`, `/flowy-ui-mockup`)
- JSON files stored in `.flowy/` directory within the project (Git version-controlled)
- Development server watches JSON file changes and auto-rerenders in the browser

### 2.2 Why Flowy over Alternatives?

- **ASCII diagrams** are hard to read for complex UIs and animations
- **Mermaid diagrams** are better but still limited — they produce static output with no interactive editing
- Flowy provides **spatial reasoning** — visual diagrams are more effective for human understanding than text-based markdown
- Flowy creates a **shared mental model** between Claude and the developer
- The cognitive benefits of visual planning are significant (28:34 in the video): even when the content is identical, seeing it visually is more effective for humans

### 2.3 Technical Architecture

**Tech Stack:**
- Language: **TypeScript**
- Data Format: **JSON files** (proprietary schema)
- Runtime: Local web server at `localhost:4242`
- File Storage: `.flowy/` directory in the project
- File Watching: Dev server watches for JSON changes and auto-rerenders

**JSON Schema:**

```json
{
  "version": "1.0",
  "name": "Onboarding Flow",
  "description": "Demo app onboarding flow showing user journey and data collected at each step",
  "nodes": [
    {
      "id": "label-entry",
      "type": "rect",
      "label": "ENTRY POINT",
      "position": { "x": 100, "y": 40 },
      "size": { "width": 120, "height": 30 },
      "style": {
        "fill": "#e0ecef",
        "stroke": "#adb5bb",
        "strokeWidth": 1,
        "fontSize": 12
      },
      "icon": "optional-icon-name"
    }
  ],
  "edges": [
    {
      "from": "node-a",
      "to": "node-b",
      "type": "arrow",
      "label": "next step"
    }
  ]
}
```

**Node Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `id` | string | Unique identifier |
| `type` | string | Shape type (`rect`, `circle`, etc.) |
| `label` | string | Display text |
| `position` | `{x, y}` | Coordinates on canvas |
| `size` | `{width, height}` | Dimensions |
| `style` | object | Visual styling (`fill`, `stroke`, `strokeWidth`, `fontSize`) |
| `icon` | string | Optional icon name |

**Edge Properties:**

| Property | Type | Description |
|----------|------|-------------|
| `from` | string | Source node ID |
| `to` | string | Target node ID |
| `type` | string | Connection type (`arrow`, etc.) |
| `label` | string | Edge label text |

**Observed JSON Files in Demo:**
- `onboarding-flow.json`
- `quiz-gameplay-flow.json`
- `quiz-navigation-flow.json`
- `quiz-screens-mockup.json`

### 2.4 Demo Contents (from YouTube)

| Demo | Timestamp | Description |
|------|-----------|-------------|
| Animation Timing & User Flow | 15:25-27:20 | Created animation timing sequence and user flow diagrams for a spinning wheel feature. Edited duration directly in Flowy. |
| UI Mockups | 31:38-32:20 | Generated UI mockups from flowcharts, showing different states of the spinning wheel and animation flow. |
| Feature Building | 33:30-35:03 | Claude built the actual feature directly from flowcharts and mockups. Working spinner wheel demonstrated. |
| Model-to-Model Review | 36:51-41:52 | Used GPT-5.2 Codex ("Carl") to review Claude's code. Found mockup vs. implementation discrepancy. |

### 2.5 Video Chapters

| Timestamp | Topic |
|-----------|-------|
| 0:00 | Introduction to CJ Hess |
| 2:48 | Why CJ prefers Claude Code — "steerable" nature |
| 4:46 | Evolution of developer environments with AI |
| 6:50 | Planning workflows and limitations of ASCII diagrams |
| 8:23 | Introduction to Flowy |
| 11:54 | Flowy vs. Mermaid diagrams |
| 15:25 | Demo: Using Flowy |
| 19:30 | Examining Flowy's skill structure |
| 23:27 | Reviewing generated flowcharts and diagrams |
| 28:34 | Cognitive benefits of visual planning |
| 31:38 | Generating UI mockups with Flowy |
| 33:30 | Building feature from flowcharts and mockups |
| 35:40 | Quick recap |
| 36:51 | Model-to-model review with Codex (Carl) |
| 41:52 | Benefits of using AI for code review |
| 45:13 | Lightning round and final thoughts |

---

## 3. Comparable Tools Survey

### 3.1 Diagram-as-Code Tools

| Tool | Format | Output | Interactive Editing | AI Bidirectional | Key Strength |
|------|--------|--------|---------------------|------------------|--------------|
| **Flowy** | JSON | HTML (interactive) | Yes | Yes (via Claude skills) | Full bidirectional AI loop |
| **Mermaid** | Text DSL | SVG/HTML | No | No | Most widely used; GitHub/GitLab native support |
| **D2** | Text DSL | SVG | No | No | ELK layout engine, advanced styling |
| **PlantUML** | Text DSL | PNG/SVG | No | No | UML-specialized, broad diagram types |
| **Graphviz** | DOT language | SVG/PNG | No | No | Graph layout algorithm strength |
| **ASCIIFlow** | ASCII | ASCII text | Yes (in-browser) | No | Pure ASCII editor |
| **Markmap** | Markdown | HTML (interactive) | Partial | No | Markdown to mind map |

### 3.2 Key Differentiator: Flowy vs. Existing Tools

The critical difference is the **feedback loop**:

```
[Existing Tools]
Text → Static Image (one-way)

[Flowy]
Claude → JSON → Interactive Visual → Edit → JSON → Claude (bidirectional loop)
```

Existing tools convert text to static images. Flowy creates a **live, editable artifact** that Claude can read back and continue working with. This is the pattern Araseo should adopt.

### 3.3 AI-Powered Diagramming Tools (Commercial)

| Tool | Description |
|------|-------------|
| **Whimsical AI** | AI-assisted flowcharts and wireframes |
| **Diagramming AI** | AI-generated diagrams from natural language |
| **Visily** | AI-powered UI design tool |
| **Napkin AI** | Text-to-visual conversion |

These are SaaS products, not developer tools integrated into the coding workflow. Araseo's advantage is the **deep Claude Code integration** as a skill.

---

## 4. Araseo Input Analysis — What Claude Generates

### 4.1 Diagram Types Claude Produces (6 Patterns)

| # | Type | Frequency | Example |
|---|------|-----------|---------|
| 1 | **ASCII Box Flowchart** | Most common | Unicode box-drawing chars (`┌─┐│└─┘`) + arrows (`→ ↓`) |
| 2 | **Mermaid Diagram Code** | Common | ` ```mermaid ... ``` ` blocks |
| 3 | **ASCII Sequence Diagram** | Moderate | Vertical timelines with arrows between actors |
| 4 | **ASCII Tree** | Common | File structures, hierarchies (`├── └──`) |
| 5 | **Markdown Table** | Common | Tabular data in `| col | col |` format |
| 6 | **Code Block Diagrams** | Rare | PlantUML, Graphviz DOT inside code blocks |

### 4.2 Parsing Patterns and Difficulty

| Pattern | Input Type | Parsing Difficulty | Notes |
|---------|------------|-------------------|-------|
| **A** | Pure ASCII Flowchart | **High** | Box detection, arrow tracing, spatial layout inference |
| **B** | Mermaid Code Block | **Low** | Well-defined syntax, existing parsers available |
| **C** | Mixed Markdown Document | **Medium** | Must identify diagram sections within prose text |
| **D** | Indentation-Based Hierarchy | **Medium** | Whitespace-sensitive, tree structure inference |
| **E** | Markdown Table | **Low** | Standard format, straightforward parsing |

### 4.3 Priority Recommendation

For Araseo's initial implementation:
1. **Start with Pattern B (Mermaid)** — lowest difficulty, existing parser ecosystem
2. **Then Pattern E (Markdown Table)** — low difficulty, high utility
3. **Then Pattern D (Hierarchy/Tree)** — medium difficulty
4. **Later Pattern A (ASCII Flowchart)** — highest difficulty, most impressive result

---

## 5. Araseo Architecture Proposal

### 5.1 Pipeline Design

```
┌─────────────────────────────────────────────────────────────────┐
│                        Araseo Pipeline                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  [Input]                                                        │
│  AI Conversation Content (Markdown, ASCII diagrams, Mermaid)    │
│       │                                                         │
│       ▼                                                         │
│  [Parser Layer] ← Araseo's unique value (Flowy doesn't have)   │
│  Markdown/ASCII/Mermaid → Structured JSON                       │
│       │                                                         │
│       ▼                                                         │
│  [JSON Intermediate Format]                                     │
│  Nodes + Edges + Metadata (Flowy-compatible schema)             │
│       │                                                         │
│       ▼                                                         │
│  [Renderer Layer]                                               │
│  JSON → Interactive Flowchart / UI Mockup                       │
│  (Local web app, drag/drop, zoom/pan, edit)                     │
│       │                                                         │
│       ▼                                                         │
│  [Bidirectional Sync] (optional, future)                        │
│  Visual edits → JSON update → Claude reads back                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 5.2 Key Modules

| Module | Responsibility | Priority |
|--------|---------------|----------|
| **Parser** | Convert markdown/ASCII/Mermaid into Araseo JSON | P0 (core) |
| **Flowchart Renderer** | Render JSON as interactive flowchart | P0 (core) |
| **UI Mockup Renderer** | Render JSON as interactive UI mockup | P1 |
| **Live Preview** | Real-time rendering as input changes | P1 |
| **Claude Code Skill** | `/araseo` skill for Claude Code integration | P0 (core) |
| **Bidirectional Editor** | Edit visuals and sync back to JSON | P2 (future) |

### 5.3 Differences from Flowy

| Aspect | Flowy | Araseo |
|--------|-------|--------|
| **Input** | Claude-generated JSON (via skill) | Markdown/ASCII/Mermaid from conversations |
| **Parser** | Not needed (JSON is the input) | Required — core differentiator |
| **Skill** | `/flowy-flowchart`, `/flowy-ui-mockup` | `/araseo` |
| **Rendering** | Custom TypeScript renderer | TBD (could use similar approach) |
| **Storage** | `.flowy/` directory | TBD |
| **Scope** | Personal dev tool | Reusable tool for any AI conversation |

### 5.4 Suggested Tech Stack

Based on Flowy's proven approach and the Araseo project needs:

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Language | **TypeScript** | Same as Flowy, strong typing, wide ecosystem |
| Renderer | **HTML Canvas** or **SVG** (with library like React Flow, D3.js, or custom) | Interactive diagrams with drag/zoom/pan |
| Parser | Custom TypeScript module | Handle ASCII/Mermaid/Markdown parsing |
| Dev Server | **Vite** or **Next.js** | Fast HMR, modern tooling |
| Data Format | **JSON** | Proven by Flowy, Claude-readable |
| Skill | **SKILL.md** in `.claude/skills/araseo/` | Claude Code native integration |

---

## 6. Claude Code Skill Implementation

### 6.1 Skill Directory Structure

```
.claude/skills/araseo/
├── SKILL.md           # Main instruction file (required)
├── template.md        # Output template (optional)
├── examples/          # Example inputs/outputs (optional)
│   ├── ascii-flowchart.md
│   ├── mermaid-diagram.md
│   └── expected-output.json
└── scripts/           # Execution scripts (optional)
    └── parse.sh
```

### 6.2 SKILL.md Frontmatter

```yaml
---
name: araseo
description: Convert AI conversation diagrams into interactive visual mockups
argument-hint: "<markdown-or-diagram-content>"
allowed-tools:
  - Read
  - Write
  - Bash
---
```

### 6.3 Key Skill Features

- `$ARGUMENTS` for passing input content
- `!`command`` for dynamic context injection (e.g., reading files)
- The skill instructs Claude to:
  1. Parse the input (markdown/ASCII/Mermaid)
  2. Convert to Araseo JSON format
  3. Write the JSON file to the project directory
  4. Optionally launch the renderer

### 6.4 Flowy's Skill Pattern (Reference)

Flowy uses two separate skills:
- `/flowy-flowchart` — Generates flowchart JSON
- `/flowy-ui-mockup` — Generates UI mockup JSON

Each skill contains the JSON schema specification, so Claude knows exactly what format to produce. The skills are iteratively refined based on usage.

---

## 7. Reference Materials

### Video & Workflow Resources

| Resource | URL |
|----------|-----|
| YouTube Video | https://www.youtube.com/watch?v=LC1mKvLWZ2E |
| CJ Hess Workflow Walkthrough | https://www.chatprd.ai/how-i-ai/cj-hess-tenex-custom-dev-tools-and-model-vs-model-code-reviews |
| Model-vs-Model Code Reviews | https://www.chatprd.ai/how-i-ai/workflows/implement-model-vs-model-ai-code-reviews-for-quality-control |
| Visual Planning Tools Workflow | https://www.chatprd.ai/how-i-ai/workflows/develop-features-with-ai-using-custom-visual-planning-tools |

### Tools Referenced in Video

| Tool | URL | Relevance |
|------|-----|-----------|
| Claude Code | https://claude.ai/code | Core AI coding assistant |
| Mermaid | https://mermaid.js.org/ | Diagram-as-code (comparison target) |
| Excalidraw | https://excalidraw.com/ | Freehand diagram tool |
| Figma | https://www.figma.com/ | Professional UI design |
| TypeScript | https://www.typescriptlang.org/ | Flowy's implementation language |
| GPT-5.2 Codex | https://openai.com/index/introducing-gpt-5-2-codex/ | Model-to-model code review |
| Google Project Genie | https://labs.google/projectgenie | AI virtual world generation |

### Diagram-as-Code Tools

| Tool | URL |
|------|-----|
| Mermaid | https://mermaid.js.org/ |
| D2 | https://d2lang.com/ |
| PlantUML | https://plantuml.com/ |
| Graphviz | https://graphviz.org/ |
| ASCIIFlow | https://asciiflow.com/ |
| Markmap | https://markmap.js.org/ |

### AI Diagramming Tools

| Tool | Description |
|------|-------------|
| Whimsical AI | AI-assisted flowcharts and wireframes |
| Diagramming AI | AI-generated diagrams from text |
| Visily | AI-powered UI design |
| Napkin AI | Text-to-visual conversion |

### CJ Hess Contact

| Platform | Link |
|----------|------|
| LinkedIn | https://www.linkedin.com/in/cj-hess-connexwork/ |
| X (Twitter) | https://x.com/seejayhess |

---

*Report generated: 2026-02-13*
*Contributors: Mole (몰), Sugiri (스기리), Nobi (노비)*
*Compiled by: Mole (몰)*
