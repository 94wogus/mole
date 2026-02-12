# Araseo Skill Development Guide

**Research by**: Mole (Intelligence Team)
**Date**: 2026-02-13
**Task**: Research Claude Code Skills spec and JSON schema patterns for /araseo skill development

---

## Executive Summary

This report provides comprehensive guidance for developing the `/araseo` skill, which converts markdown planning documents (기획서) into JSON for rendering as interactive flowcharts and UI mockups. The research covers:

1. Claude Code Skills specification and best practices
2. JSON schema patterns for flowcharts and UI mockups
3. Markdown → JSON conversion techniques
4. Real-world skill transformation examples

**Key insight**: Claude IS the parser. The skill teaches Claude the JSON schema, and Claude converts markdown to JSON. No separate parser module needed.

---

## 1. Claude Code Skills Specification

### 1.1 SKILL.md Structure

Every skill requires a `SKILL.md` file with two parts:

1. **YAML Frontmatter** (between `---` markers)
2. **Markdown Content** (instructions for Claude)

**Minimal Example**:
```yaml
---
name: araseo
description: Converts markdown planning documents into JSON for flowchart and UI mockup rendering. Use when user provides a 기획서 or asks to visualize a plan.
---

# Araseo - Markdown to JSON Converter

[Instructions for Claude...]
```

**Source**: [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)

### 1.2 Essential Frontmatter Fields

| Field | Required | Purpose | Example |
|-------|----------|---------|---------|
| `name` | Yes | Becomes `/slash-command` | `araseo` |
| `description` | Yes | Triggers auto-invocation | "Converts markdown planning documents..." |
| `argument-hint` | No | Autocomplete hint | `[filename]` |
| `disable-model-invocation` | No | Prevent auto-trigger | `false` (allow auto-trigger) |
| `user-invocable` | No | Show in `/` menu | `true` (visible) |
| `allowed-tools` | No | Tool restrictions | `Read, Write` |
| `context` | No | Run in subagent | `fork` |

**Recommendation for /araseo**:
- Keep `disable-model-invocation: false` so Claude can auto-trigger when user provides 기획서
- Set `allowed-tools: Read, Write` to limit scope
- Consider `context: fork` if you want isolated execution

### 1.3 String Substitutions

Skills support dynamic placeholders:

| Variable | Description | Example |
|----------|-------------|---------|
| `$ARGUMENTS` | All arguments passed | `/araseo planning.md` → "planning.md" |
| `$ARGUMENTS[N]` or `$N` | Specific argument by index | `$0` = first arg |
| `${CLAUDE_SESSION_ID}` | Current session ID | For logging/tracking |

**Usage in SKILL.md**:
```yaml
---
name: araseo
description: Convert markdown to JSON
---

Convert the file $ARGUMENTS to JSON for rendering.

1. Read the markdown file
2. Parse the structure
3. Generate JSON output
4. Write to output file
```

**Source**: [Claude Code Skills - String Substitutions](https://code.claude.com/docs/en/skills#available-string-substitutions)

### 1.4 Supporting Files

Skills can include additional files for better organization:

```
araseo/
├── SKILL.md           # Main instructions (required)
├── templates/
│   ├── flowchart-schema.json
│   └── ui-mockup-schema.json
├── examples/
│   ├── example-planning-doc.md
│   ├── example-flowchart.json
│   └── example-mockup.json
└── scripts/
    └── validate.py    # Optional validation script
```

**Progressive Disclosure Pattern**:
```markdown
# Araseo Skill

## Quick Start
[Brief instructions]

## JSON Schemas
- **Flowchart schema**: See [templates/flowchart-schema.json](templates/flowchart-schema.json)
- **UI mockup schema**: See [templates/ui-mockup-schema.json](templates/ui-mockup-schema.json)

## Examples
- **Planning document**: See [examples/example-planning-doc.md](examples/example-planning-doc.md)
- **Flowchart output**: See [examples/example-flowchart.json](examples/example-flowchart.json)
```

**Best Practice**: Keep SKILL.md under 500 lines. Move detailed schemas to separate files.

**Source**: [Claude Code Skills - Add Supporting Files](https://code.claude.com/docs/en/skills#add-supporting-files)

### 1.5 Dynamic Context Injection

The `!`command`` syntax runs shell commands BEFORE the skill content is sent to Claude:

```yaml
---
name: araseo
description: Convert markdown to JSON
---

## Input Document Content
!`cat $ARGUMENTS`

## Your Task
Convert the above markdown content to JSON following the schema below...
```

**How it works**:
1. `!`cat $ARGUMENTS`` executes immediately (preprocessing)
2. Command output replaces the placeholder
3. Claude receives the fully-rendered prompt with actual file content

**Important**: This is preprocessing, not runtime execution. Claude sees the result, not the command.

**Source**: [Claude Code Skills - Inject Dynamic Context](https://code.claude.com/docs/en/skills#inject-dynamic-context)

---

## 2. JSON Schema Patterns

### 2.1 Flowchart Schema (JSON Graph Format)

**Standard**: [JSON Graph Specification](https://github.com/jsongraph/json-graph-specification)

**Core Structure**:
```json
{
  "graph": {
    "directed": true,
    "label": "My Flowchart",
    "type": "flowchart",
    "metadata": {},
    "nodes": {
      "node_id_1": {
        "label": "Start",
        "metadata": {
          "shape": "oval",
          "color": "#4CAF50"
        }
      },
      "node_id_2": {
        "label": "Process Step",
        "metadata": {
          "shape": "rectangle",
          "color": "#2196F3"
        }
      },
      "node_id_3": {
        "label": "Decision?",
        "metadata": {
          "shape": "diamond",
          "color": "#FF9800"
        }
      }
    },
    "edges": [
      {
        "source": "node_id_1",
        "target": "node_id_2",
        "relation": "flows_to",
        "directed": true,
        "metadata": {
          "label": ""
        }
      },
      {
        "source": "node_id_2",
        "target": "node_id_3",
        "relation": "flows_to",
        "directed": true
      }
    ]
  }
}
```

**Key Components**:

1. **Graph Object**:
   - `directed`: Boolean (default: true)
   - `label`: Display title
   - `type`: Graph type (e.g., "flowchart", "diagram")
   - `metadata`: Custom properties
   - `nodes`: Dictionary of node objects (keyed by unique IDs)
   - `edges`: Array of edge objects

2. **Node Object**:
   - Key: Unique node ID (string)
   - `label`: Display text (required)
   - `metadata`: Custom properties (shape, color, position, etc.)

3. **Edge Object**:
   - `source`: Source node ID (required)
   - `target`: Target node ID (required)
   - `relation`: Relationship type (e.g., "flows_to", "depends_on")
   - `directed`: Boolean (inherits from graph if omitted)
   - `metadata`: Custom properties (label, style, etc.)

**Flowchart-Specific Metadata Recommendations**:

| Shape | Use Case | Color Suggestion |
|-------|----------|------------------|
| `oval` | Start/End | Green (#4CAF50) |
| `rectangle` | Process step | Blue (#2196F3) |
| `diamond` | Decision point | Orange (#FF9800) |
| `parallelogram` | Input/Output | Purple (#9C27B0) |
| `cylinder` | Database | Teal (#009688) |

**Sources**:
- [JSON Graph Specification](https://github.com/jsongraph/json-graph-specification)
- [JSON Graph Format Info](https://jsongraphformat.info/)

### 2.2 UI Mockup Schema

**Inspiration**: [WireMD](https://wiremd.dev/) - Markdown to UI mockup converter

**WireMD Approach**:
1. Parse markdown with extended syntax (bracket notation)
2. Convert to JSON representation
3. Render to multiple formats (HTML, React, Tailwind, etc.)

**Example Markdown → JSON Mapping**:

**Input Markdown**:
```markdown
# Login Page

[[ Logo | Sign In | Help ]]

## Welcome Back

[Email address]
[Password]

[Sign In Button]

---

[Forgot password?]
```

**Output JSON** (simplified):
```json
{
  "page": {
    "title": "Login Page",
    "components": [
      {
        "type": "navbar",
        "items": ["Logo", "Sign In", "Help"]
      },
      {
        "type": "heading",
        "level": 2,
        "text": "Welcome Back"
      },
      {
        "type": "input",
        "label": "Email address",
        "inputType": "email"
      },
      {
        "type": "input",
        "label": "Password",
        "inputType": "password"
      },
      {
        "type": "button",
        "text": "Sign In",
        "variant": "primary"
      },
      {
        "type": "divider"
      },
      {
        "type": "link",
        "text": "Forgot password?"
      }
    ]
  }
}
```

**Proposed UI Mockup JSON Schema for Araseo**:

```json
{
  "mockup": {
    "version": "1.0",
    "title": "Page Title",
    "metadata": {
      "style": "wireframe",
      "theme": "clean"
    },
    "layout": {
      "type": "single-page",
      "sections": [
        {
          "id": "header",
          "type": "header",
          "components": [
            {
              "type": "navbar",
              "items": [
                {"text": "Logo", "type": "logo"},
                {"text": "Menu Item 1", "type": "link"},
                {"text": "Menu Item 2", "type": "link"}
              ]
            }
          ]
        },
        {
          "id": "main",
          "type": "main",
          "components": [
            {
              "type": "heading",
              "level": 1,
              "text": "Page Heading"
            },
            {
              "type": "form",
              "fields": [
                {
                  "type": "input",
                  "inputType": "text",
                  "label": "Username",
                  "placeholder": "Enter username",
                  "required": true
                },
                {
                  "type": "input",
                  "inputType": "password",
                  "label": "Password",
                  "required": true
                }
              ],
              "actions": [
                {
                  "type": "button",
                  "text": "Submit",
                  "variant": "primary"
                }
              ]
            }
          ]
        },
        {
          "id": "footer",
          "type": "footer",
          "components": [
            {
              "type": "text",
              "content": "© 2026 Company Name"
            }
          ]
        }
      ]
    }
  }
}
```

**Supported Component Types** (based on WireMD's 41 components):

| Component | Type | Properties |
|-----------|------|------------|
| Navigation | `navbar`, `sidebar`, `breadcrumb` | `items[]` |
| Text | `heading`, `paragraph`, `text` | `text`, `level` |
| Input | `input`, `textarea`, `select` | `label`, `inputType`, `placeholder` |
| Button | `button`, `link` | `text`, `variant`, `href` |
| Layout | `container`, `grid`, `flex` | `children[]`, `columns` |
| Media | `image`, `video`, `icon` | `src`, `alt` |
| Data | `table`, `list`, `card` | `headers[]`, `rows[]` |
| Feedback | `alert`, `toast`, `badge` | `message`, `variant` |

**Source**: [WireMD](https://wiremd.dev/)

---

## 3. Markdown → JSON Conversion Techniques

### 3.1 Claude Structured Output (Recommended)

**Feature**: Claude Structured Outputs (Public Beta - Nov 2025)

Claude can generate schema-guaranteed JSON responses using the `response_format` parameter in the API. For Claude Code skills, you achieve this through careful prompting.

**Key Technique**: Explicit schema instruction in SKILL.md

```yaml
---
name: araseo
description: Convert markdown to JSON
---

# Araseo - Markdown to JSON Converter

You are a markdown-to-JSON converter. Your task is to read markdown planning documents and convert them to structured JSON.

## CRITICAL: Output Format Rules

1. **Output ONLY valid JSON** - No markdown code fences, no preambles, no explanations
2. **Do NOT wrap JSON in ```json blocks** - Output raw JSON directly
3. **Follow the schema exactly** - Every field must match the schema below

## JSON Schema for Flowcharts

```json
{
  "graph": {
    "directed": true,
    "nodes": { ... },
    "edges": [ ... ]
  }
}
```

## Conversion Process

1. Read the input markdown file: $ARGUMENTS
2. Parse the structure and identify:
   - Flowchart nodes (steps, decisions, start/end points)
   - Connections between nodes
   - Labels and metadata
3. Generate JSON matching the schema EXACTLY
4. Output ONLY the JSON (no markdown fences, no extra text)

## Example

Input:
```markdown
# User Login Flow
Start → Check credentials → Decision: Valid? → Success / Failure
```

Output (NO CODE FENCES):
{"graph":{"directed":true,"nodes":{"start":{"label":"Start"},"check":{"label":"Check credentials"},"decision":{"label":"Valid?"},"success":{"label":"Success"},"failure":{"label":"Failure"}},"edges":[{"source":"start","target":"check"},{"source":"check","target":"decision"},{"source":"decision","target":"success","metadata":{"label":"Yes"}},{"source":"decision","target":"failure","metadata":{"label":"No"}}]}}
```

**Common Pitfall**: Claude often wraps JSON in markdown code blocks:

❌ **Wrong**:
```json
{
  "graph": { ... }
}
```

✅ **Correct**:
```json
{"graph": { ... }}
```

**Solution**: Explicitly state "Do NOT wrap JSON in code fences" in the skill instructions.

**Sources**:
- [Claude API Structured Output Guide](https://thomas-wiegold.com/blog/claude-api-structured-output/)
- [Claude Structured Outputs Discussion](https://github.com/unclecode/crawl4ai/issues/1663)

### 3.2 Schema-First Approach

**Pattern**: Define the schema first, then instruct Claude to populate it.

```yaml
---
name: araseo
description: Convert markdown to JSON
---

# Schema Template

Below is the JSON schema template. Fill in the values based on the input markdown.

```json
{
  "graph": {
    "directed": true,
    "label": "<EXTRACT_TITLE>",
    "nodes": {
      "<NODE_ID_1>": {
        "label": "<NODE_LABEL_1>",
        "metadata": {}
      }
    },
    "edges": [
      {
        "source": "<SOURCE_ID>",
        "target": "<TARGET_ID>",
        "relation": "flows_to"
      }
    ]
  }
}
```

# Instructions

1. Read $ARGUMENTS
2. Replace all <PLACEHOLDERS> with actual values
3. Add more nodes/edges as needed
4. Output the populated JSON
```

**Advantage**: Claude sees the structure visually and fills in the blanks.

### 3.3 Example-Based Learning

**Pattern**: Provide 2-3 examples in the skill to teach Claude the conversion.

```yaml
---
name: araseo
description: Convert markdown to JSON
---

# Examples

## Example 1: Simple Flowchart

**Input**:
```markdown
# Simple Process
Start → Step A → Step B → End
```

**Output**:
```json
{"graph":{"directed":true,"label":"Simple Process","nodes":{"start":{"label":"Start"},"a":{"label":"Step A"},"b":{"label":"Step B"},"end":{"label":"End"}},"edges":[{"source":"start","target":"a"},{"source":"a","target":"b"},{"source":"b","target":"end"}]}}
```

## Example 2: Decision Flow

**Input**:
```markdown
# Approval Process
Submit → Review → Approved? → Yes: Publish / No: Revise
```

**Output**:
```json
{"graph":{"directed":true,"label":"Approval Process","nodes":{"submit":{"label":"Submit"},"review":{"label":"Review"},"decision":{"label":"Approved?","metadata":{"shape":"diamond"}},"publish":{"label":"Publish"},"revise":{"label":"Revise"}},"edges":[{"source":"submit","target":"review"},{"source":"review","target":"decision"},{"source":"decision","target":"publish","metadata":{"label":"Yes"}},{"source":"decision","target":"revise","metadata":{"label":"No"}}]}}
```

# Your Task

Convert $ARGUMENTS following the same pattern as the examples above.
```

**Advantage**: Learning by example is highly effective for Claude.

**Source**: [Claude Prompt Engineering Best Practices](https://claude.com/blog/best-practices-for-prompt-engineering)

---

## 4. Real-World Skill Transformation Examples

### 4.1 Meta-Skill: skill-creator

**Purpose**: A skill that creates other skills

**Key Patterns**:
- High-level guide with progressive disclosure
- References to detailed documentation
- Step-by-step workflow with scripts
- Template-based initialization

**Structure**:
```
skill-creator/
├── SKILL.md (overview + process)
├── scripts/
│   ├── init_skill.py (generate skill template)
│   └── package_skill.py (validate and package)
└── references/
    ├── PRINCIPLES.md
    └── PATTERNS.md
```

**Insight**: The skill-creator itself embodies the principles it teaches.

**Source**: [Anthropic Skills - skill-creator](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md)

### 4.2 Document Transformation Skills

**Available Skills** (from Anthropic's official repo):
- `docx` - Word document creation & editing
- `pdf` - PDF generation and manipulation
- `pptx` - PowerPoint presentation creation
- `xlsx` - Excel spreadsheet generation

**Pattern**: These skills demonstrate complex structured output generation.

**Common Structure**:
1. **Schema definition** (in SKILL.md or references/)
2. **Template files** (in assets/)
3. **Validation scripts** (in scripts/)
4. **Examples** (in examples/)

**Source**: [Anthropic Skills Repository](https://github.com/anthropics/skills)

### 4.3 Markdown to EPUB Converter

**Purpose**: Convert markdown documents to EPUB ebooks

**Relevant Pattern**:
- Input: Markdown file
- Process: Parse structure, extract metadata, apply styling
- Output: Structured file (EPUB)

**Parallel to Araseo**:
- Input: Markdown 기획서
- Process: Parse structure, identify nodes/components
- Output: Structured JSON

**Source**: [36 Claude Skills Examples](https://aiblewmymind.substack.com/p/claude-skills-36-examples)

### 4.4 API Docs Generator

**Purpose**: Generate developer-friendly API documentation from endpoint definitions

**Relevant Pattern**:
- Input: Structured data (API endpoints)
- Process: Apply template and formatting rules
- Output: Consistent documentation

**Key Insight**: Template + data = structured output (same as Araseo)

**Source**: [How to Create Claude Skills](https://claude.com/blog/how-to-create-skills-key-steps-limitations-and-examples)

---

## 5. Recommendations for /araseo Skill Development

### 5.1 Skill Structure

```
araseo/
├── SKILL.md                          # Main skill instructions
├── templates/
│   ├── flowchart-schema.json         # JSON Graph Format schema
│   └── ui-mockup-schema.json         # UI component schema
├── examples/
│   ├── planning-docs/
│   │   ├── simple-flowchart.md
│   │   ├── decision-flow.md
│   │   └── ui-mockup.md
│   └── output/
│       ├── simple-flowchart.json
│       ├── decision-flow.json
│       └── ui-mockup.json
└── scripts/
    └── validate-json.py              # Optional JSON schema validator
```

### 5.2 SKILL.md Template

```yaml
---
name: araseo
description: Converts markdown planning documents (기획서) into JSON for flowchart and UI mockup rendering. Use when user provides a 기획서, asks to visualize a plan, or mentions flowcharts/mockups.
argument-hint: [markdown-file]
allowed-tools: Read, Write
---

# Araseo - Markdown to JSON Converter

알아서 (Araseo) automatically converts AI conversation content into interactive visual mockups and diagrams.

## Your Role

You are a markdown-to-JSON converter. Read markdown planning documents and convert them to structured JSON for rendering.

## CRITICAL: Output Format Rules

1. **Output ONLY valid JSON** - No markdown code fences, no preambles, no explanations
2. **Do NOT wrap JSON in ```json blocks** - Output raw JSON directly
3. **Follow the schema exactly** - Every field must match the schema

## Supported Output Types

1. **Flowcharts**: Process flows, decision trees, state diagrams
2. **UI Mockups**: Wireframes, page layouts, component structures

## JSON Schemas

### Flowchart Schema (JSON Graph Format)

See [templates/flowchart-schema.json](templates/flowchart-schema.json) for complete schema.

**Structure**:
```json
{
  "graph": {
    "directed": true,
    "label": "Flowchart Title",
    "nodes": {
      "node_id": {
        "label": "Node Label",
        "metadata": {"shape": "rectangle"}
      }
    },
    "edges": [
      {
        "source": "node_id_1",
        "target": "node_id_2",
        "relation": "flows_to"
      }
    ]
  }
}
```

### UI Mockup Schema

See [templates/ui-mockup-schema.json](templates/ui-mockup-schema.json) for complete schema.

**Structure**:
```json
{
  "mockup": {
    "title": "Page Title",
    "layout": {
      "sections": [
        {
          "type": "header",
          "components": [...]
        }
      ]
    }
  }
}
```

## Examples

### Flowchart Example

**Input**: [examples/planning-docs/simple-flowchart.md](examples/planning-docs/simple-flowchart.md)
**Output**: [examples/output/simple-flowchart.json](examples/output/simple-flowchart.json)

### UI Mockup Example

**Input**: [examples/planning-docs/ui-mockup.md](examples/planning-docs/ui-mockup.md)
**Output**: [examples/output/ui-mockup.json](examples/output/ui-mockup.json)

## Conversion Process

1. **Read** the input file: $ARGUMENTS
2. **Determine type**: Flowchart or UI mockup?
3. **Parse structure**: Identify nodes/edges or components/layout
4. **Generate JSON**: Follow the appropriate schema
5. **Write output**: Save to `<input-filename>.json`
6. **Confirm**: Report the output file path

## Tips

- For flowcharts: Look for arrows (→), decision points, and process steps
- For UI mockups: Look for page structure, components, and layout descriptions
- Use meaningful node IDs (e.g., "start", "check_auth", "success")
- Add metadata for visual hints (shapes, colors, positions)

## Validation

After generating JSON, you can optionally run:
```bash
python scripts/validate-json.py <output-file>
```
```

### 5.3 Implementation Strategy

**Phase 1: Core Functionality**
1. Create SKILL.md with flowchart support only
2. Define JSON Graph Format schema in templates/
3. Provide 3 examples (simple, decision, complex)
4. Test with real planning documents

**Phase 2: UI Mockup Support**
1. Add UI mockup schema to templates/
2. Provide UI mockup examples
3. Update SKILL.md with mockup instructions
4. Test with wireframe planning documents

**Phase 3: Refinement**
1. Add validation script (optional)
2. Improve schema based on renderer feedback
3. Add more examples
4. Document edge cases and limitations

### 5.4 Testing Checklist

- [ ] Skill auto-triggers when user mentions "기획서", "flowchart", or "mockup"
- [ ] Manual invocation works: `/araseo planning.md`
- [ ] Reads markdown file correctly
- [ ] Generates valid JSON (no markdown code fences)
- [ ] Follows schema exactly (validates against JSON schema)
- [ ] Writes output file with correct naming
- [ ] Reports output file path to user
- [ ] Handles errors gracefully (invalid markdown, missing file)

### 5.5 Common Pitfalls to Avoid

1. ❌ **JSON wrapped in markdown code fences**
   - Solution: Explicitly instruct "Output raw JSON, no code fences"

2. ❌ **Schema drift** (output doesn't match schema)
   - Solution: Provide clear schema template, use validation

3. ❌ **Over-complicated SKILL.md** (too verbose)
   - Solution: Keep main file under 500 lines, use progressive disclosure

4. ❌ **No examples** (Claude guesses the format)
   - Solution: Provide 2-3 clear examples

5. ❌ **Missing error handling**
   - Solution: Instruct Claude to validate input and report errors

---

## 6. Additional Resources

### Official Documentation
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)
- [Agent Skills Open Standard](https://agentskills.io)
- [Anthropic Skills Repository](https://github.com/anthropics/skills)

### JSON Schema Standards
- [JSON Graph Specification](https://github.com/jsongraph/json-graph-specification)
- [JSON Graph Format Info](https://jsongraphformat.info/)

### Real-World Examples
- [WireMD - Markdown to UI Mockup](https://wiremd.dev/)
- [36 Claude Skills Examples](https://aiblewmymind.substack.com/p/claude-skills-36-examples)
- [Awesome Claude Skills](https://github.com/ComposioHQ/awesome-claude-skills)

### Tools
- [Figma Flowchart to JSON Plugin](https://www.figma.com/community/plugin/1257283022168373227/flowchart-to-json)
- [JSON Canvas to Mermaid Converter](https://github.com/alexwiench/JSON-Canvas-To-Mermaid)
- [Dynamic JSON Chart Generator](https://github.com/toct92/dynamic-json-chart)

---

## 7. Conclusion

The `/araseo` skill should:

1. **Read** markdown planning documents (기획서)
2. **Parse** structure (flowchart nodes/edges OR UI components/layout)
3. **Convert** to JSON following well-defined schemas
4. **Output** valid JSON (NO markdown code fences)
5. **Support** both flowcharts (JSON Graph Format) and UI mockups

**Key Success Factors**:
- ✅ Clear schema definition (use JSON Graph Format for flowcharts)
- ✅ Explicit output format instructions (no code fences!)
- ✅ 2-3 concrete examples for each type
- ✅ Progressive disclosure (main SKILL.md < 500 lines)
- ✅ Supporting files for schemas and examples

**Next Steps for Sugiri**:
1. Create skill directory structure in `Araseo-skill/.claude/skills/araseo/`
2. Write SKILL.md based on template in Section 5.2
3. Create schema templates in `templates/`
4. Add examples in `examples/`
5. Test with real 기획서 documents
6. Iterate based on renderer integration

---

**Report compiled by**: Mole (Intelligence Team)
**For**: Team Lead & Sugiri (Skill Developer)
**Date**: 2026-02-13
