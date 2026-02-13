# Flowy Research Report — YouTube Video Analysis

## Source

- **Title**: DIY dev tools: How this engineer created "Flowy" to visualize his plans and accelerate coding
- **Channel**: How I AI (hosted by Claire Vo, 6.92만 subscribers)
- **Presenter**: CJ Hess, Software Engineer at Tenex
- **URL**: https://www.youtube.com/watch?v=LC1mKvLWZ2E
- **Published**: 2026-02-09
- **Views**: 6,604 (as of 2026-02-13)
- **Duration**: 53:07

---

## 1. What is Flowy?

Flowy is a **custom-built visualization tool** developed by CJ Hess. Its primary purpose is to transform **JSON files into interactive visual mockups and flowcharts**, providing a more effective way to plan and visualize complex UI and animation workflows compared to traditional ASCII diagrams or Mermaid diagrams.

### Key Characteristics
- **Local web application** running at `localhost:4242`
- **Dual-purpose**: Both a viewer and an editor
- **Bidirectional sync**: Changes made in the visual editor are saved back to the JSON file, and Claude can read the updated JSON natively
- **Two main modes**: Flowchart rendering and UI mockup rendering
- **Claude Code skill integration**: Claude writes JSON files through custom skills, and Flowy renders them

### Why Flowy over Alternatives?
- ASCII diagrams are hard to read for complex UIs and animations
- Mermaid diagrams are better but still limited for interactive visual planning
- Flowy provides **spatial reasoning** — seeing diagrams visually is more effective for human understanding than parsing text-based markdown
- Flowy creates a **shared mental model** between Claude and the human developer

---

## 2. How Flowy Works — Technical Architecture

### Core Pipeline
```
Claude Code → (writes JSON via skill) → JSON file → Flowy (localhost:4242) → Interactive Visual
User edits in Flowy → JSON file updated → Claude reads updated JSON → Continues development
```

### JSON Format Structure
Based on the video demo, the JSON format follows this schema:

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
      "position": {
        "x": 100,
        "y": 40
      },
      "size": {
        "width": 120,
        "height": 30
      },
      "style": {
        "fill": "#e0ecef",
        "stroke": "#adb5bb",
        "strokeWidth": 1,
        "fontSize": 12
      }
    },
    {
      "id": "app-launch",
      "type": "circle",
      "label": "AppLaunch",
      "position": {
        "x": 81,
        "y": 126.99726282983391
      },
      "size": {
        "width": 100,
        "height": 100
      }
    }
  ]
}
```

### Node Types Observed
- `rect` — Rectangular nodes (for labels, steps, processes)
- `circle` — Circular nodes (for events, states)

### Node Properties
- `id`: Unique identifier
- `type`: Shape type (rect, circle, etc.)
- `label`: Display text
- `position`: `{x, y}` coordinates on canvas
- `size`: `{width, height}` dimensions
- `style`: Visual styling (`fill`, `stroke`, `strokeWidth`, `fontSize`)

### Tech Stack
- **Language**: TypeScript
- **Data Format**: JSON files (proprietary schema)
- **Runtime**: Local web server at `localhost:4242`
- **Project Structure**: Separate from the main app codebase
- **File Storage**: JSON files stored within the project directory (observed file tree includes `onboarding-flow.json`, `quiz-gameplay-flow.json`, `quiz-navigation-flow.json`, `quiz-screens-mockup.json`)

---

## 3. Claude Code Skill Structure

Flowy integrates with Claude Code through **custom skills**. CJ has built two main skills:

### Skill Architecture
1. **Flowchart Skill** — Instructs Claude on how to write JSON files that generate flowcharts in Flowy
2. **UI Mockup Skill** — Instructs Claude on how to write JSON files that generate UI mockups in Flowy

### How Skills Work
- Skills are **markdown files** placed in the `.claude/` directory (or similar skill location)
- They describe the **JSON format** Flowy expects, so Claude knows exactly how to write valid files
- Skills are **iteratively refined** based on usage and feedback
- The skill tells Claude:
  - The JSON schema to follow
  - Available node types and their properties
  - How to structure connections/edges between nodes
  - Naming conventions for files

### Skill-Driven Workflow
1. User prompts Claude: "Create a flowchart for the onboarding flow"
2. Claude reads the Flowy skill to understand the JSON format
3. Claude writes a `.json` file (e.g., `onboarding-flow.json`)
4. User opens Flowy at `localhost:4242` to see the rendered diagram
5. User edits the diagram visually in Flowy
6. Changes are saved back to the JSON file
7. Claude can read the updated JSON and continue building the feature

---

## 4. Demo Contents

### Demo 1: Animation Timing and User Flow Diagrams (15:25-27:20)
- CJ prompts Claude Code to create an animation timing sequence diagram and a user flow diagram for a "tips and tricks" section of an app (spinning wheel feature)
- Shows how to view and edit diagrams in Flowy
- Demonstrates adjusting animation duration directly in the visual editor

### Demo 2: Generating UI Mockups (31:38-32:20)
- After modifying flowcharts, CJ prompts Claude to generate UI mockups based on the diagrams
- Mockup includes different states of the spinning wheel and the animation flow
- Shows Flowy rendering screen mockups with component layouts

### Demo 3: Building Feature from Flowcharts and Mockups (33:30-35:03)
- Based on generated flowcharts and mockups, Claude directly builds the feature
- Demonstrates the working spinner wheel in the app — spinning and revealing a tip
- Shows the end-to-end pipeline: plan visually → build from plan

### Demo 4: Model-to-Model Code Review with Codex (36:51-41:52)
- CJ uses GPT-5.2 Codex (nicknamed "Carl") to review code generated by Claude
- Codex identifies a discrepancy between the mockup and implementation (pointer landing between dots instead of on them)
- Demonstrates quality control through AI-to-AI review

---

## 5. Key Video Chapters

| Timestamp | Topic |
|-----------|-------|
| 0:00 | Introduction to CJ Hess |
| 2:48 | Why CJ prefers Claude Code — "steerable" nature and intent understanding |
| 4:46 | Evolution of developer environments with AI |
| 6:50 | Planning workflows and limitations of ASCII diagrams |
| 8:23 | Introduction to Flowy |
| 11:54 | Flowy vs. Mermaid diagrams |
| 15:25 | Demo: Using Flowy |
| 19:30 | Examining Flowy's skill structure |
| 23:27 | Reviewing generated flowcharts and diagrams |
| 28:34 | Cognitive benefits of visual planning vs. text-based planning |
| 31:38 | Generating UI mockups with Flowy |
| 33:30 | Building feature directly from flowcharts and mockups |
| 35:40 | Quick recap |
| 36:51 | Model-to-model review with Codex (Carl) |
| 41:52 | Benefits of using AI for code review |
| 45:13 | Lightning round and final thoughts |

---

## 6. Key Takeaways for the Araseo Project

### Direct Relevance
1. **Flowy's core concept is almost identical to Araseo's vision** — converting structured data into interactive visual mockups and flowcharts
2. **JSON as intermediate format** — Flowy uses JSON (not markdown or ASCII) as the canonical data format. This is a proven approach.
3. **Bidirectional editing** — Flowy is not just a renderer but also an editor. Changes flow back to JSON. This enables a feedback loop between human and AI.
4. **Claude Code skill integration** — The skill system teaches Claude the JSON schema, enabling Claude to generate valid diagrams without additional tools.
5. **Two rendering modes** — Flowchart mode and UI mockup mode are separate but use the same JSON-based approach.

### Architecture Patterns to Adopt
1. **Local web app** — Flowy runs as a local server (`localhost:4242`). This is simple, fast, and requires no external dependencies.
2. **JSON schema design** — Nodes have `id`, `type`, `label`, `position`, `size`, `style`. Edges/connections link nodes. This is a well-proven pattern.
3. **Skill-driven AI integration** — The skill file acts as a "contract" between Claude and the rendering tool. Claude follows the skill to produce valid JSON.
4. **Iterative skill refinement** — Skills improve over time based on actual usage patterns.

### Differences / Improvements for Araseo
1. **Araseo's unique value**: Araseo starts from **markdown/ASCII diagram input** (from conversation content), while Flowy starts from Claude-generated JSON. Araseo needs a **parser layer** that Flowy doesn't have.
2. **Open source opportunity**: CJ mentioned plans to open-source Flowy. This could provide reference implementation.
3. **Araseo as a Claude Code skill**: Like Flowy, Araseo should be invokable as a `/araseo` skill within Claude Code.

### Referenced Tools
- Claude Code: https://claude.ai/code
- Mermaid diagrams: https://mermaid.js.org/
- Excalidraw: https://excalidraw.com/
- Figma: https://www.figma.com/
- TypeScript: https://www.typescriptlang.org/
- GPT-5.2 Codex (for model-to-model review)
- Google's Project Genie (generates explorable virtual worlds from images)

### External Workflow Resources
- Detailed workflow walkthroughs: https://www.chatprd.ai/how-i-ai/cj-hess-tenex-custom-dev-tools-and-model-vs-model-code-reviews
- Implement Model-vs-Model AI Code Reviews: https://www.chatprd.ai/how-i-ai/workflows/implement-model-vs-model-ai-code-reviews-for-quality-control
- Develop Features with AI Using Custom Visual Planning Tools: https://www.chatprd.ai/how-i-ai/workflows/develop-features-with-ai-using-custom-visual-planning-tools

### CJ Hess Contact
- LinkedIn: https://www.linkedin.com/in/cj-hess-connexwork/
- X: https://x.com/seejayhess

---

*Report generated: 2026-02-13*
*Researcher: Mole (몰)*
