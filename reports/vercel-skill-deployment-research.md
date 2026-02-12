# Vercel Skill Deployment Research Report

## Summary

There are **three distinct distribution systems** for Claude Code skills/extensions, plus a **Vercel-specific deployment pattern** for hosting web app renderers. This report covers each system and how they apply to the Araseo project.

---

## 1. Distribution System A: Vercel Skills Ecosystem (`npx skills`)

### What Is It?

Vercel released an **open agent skills ecosystem** (January 2026) — a CLI tool and directory for managing reusable skill packages across 35+ AI coding agents.

- **CLI**: `npx skills`
- **Directory/Leaderboard**: [skills.sh](https://skills.sh/)
- **GitHub**: https://github.com/vercel-labs/skills

### How It Works

```bash
# Install a skill package
npx skills add vercel-labs/agent-skills

# Install specific skills only
npx skills add owner/repo --skill frontend-design --skill skill-creator

# Target specific agent
npx skills add owner/repo -a claude-code

# Management commands
npx skills list          # View installed skills
npx skills find [query]  # Search for skills
npx skills remove [name] # Uninstall
npx skills check         # Check for updates
npx skills init [name]   # Create new skill template
```

### Skill Package Structure

Each skill is a **SKILL.md file** with YAML frontmatter:

```markdown
---
name: my-skill
description: Brief explanation of what the skill does
---

Instructions for the agent...
```

Skills are organized in repositories:

```
my-skills-repo/
├── skills/
│   ├── skill-one/
│   │   └── SKILL.md
│   └── skill-two/
│       └── SKILL.md
├── .curated/          # Curated skills
├── .experimental/     # Experimental skills
└── README.md
```

### Cross-Agent Support

The CLI auto-detects installed agents and installs skills to the correct location:

| Agent | Skill Path |
|-------|-----------|
| Claude Code | `.claude/skills/` |
| Cursor | `.cursor/skills/` |
| Cline | `.cline/skills/` |
| OpenCode | `.agents/skills/` |
| 30+ others | Agent-specific paths |

### Installation Methods

- **Symlink** (recommended): Single source of truth, updates propagate automatically
- **Copy**: Independent copies for each agent

### Key Advantage for Araseo

Publishing to the Vercel skills ecosystem means Araseo would be **installable across 35+ AI agents**, not just Claude Code.

---

## 2. Distribution System B: Claude Code Plugin System

### What Is It?

Anthropic's **native plugin system** for Claude Code — more powerful than skills alone, supporting commands, agents, hooks, MCP servers, and LSP servers.

- **Docs**: https://code.claude.com/docs/en/plugins
- **GitHub**: https://github.com/anthropics/skills

### Plugin vs. Standalone Comparison

| Approach | Skill Names | Best For |
|----------|-------------|----------|
| **Standalone** (`.claude/`) | `/hello` | Personal workflows, single project |
| **Plugin** (`.claude-plugin/`) | `/plugin-name:hello` | Sharing, distribution, cross-project |

### Plugin Structure

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json        # Manifest (required)
├── commands/              # User-invoked skills (slash commands)
│   └── hello/
│       └── SKILL.md
├── skills/                # Agent-invoked skills (auto-triggered)
│   └── code-review/
│       └── SKILL.md
├── agents/                # Custom agent definitions
├── hooks/                 # Event handlers
│   └── hooks.json
├── .mcp.json              # MCP server configurations
├── .lsp.json              # LSP server configurations
└── README.md
```

### plugin.json Manifest

```json
{
  "name": "araseo",
  "description": "Convert AI conversation diagrams into interactive visual mockups",
  "version": "1.0.0",
  "author": {
    "name": "Araseo Team"
  },
  "homepage": "https://github.com/94wogus/araseo",
  "repository": "https://github.com/94wogus/araseo"
}
```

### Distribution Methods

#### Local Testing
```bash
claude --plugin-dir ./my-plugin
```

#### Plugin Marketplace
```bash
# Add a marketplace (GitHub repo)
/plugin marketplace add your-org/your-skills-repo

# Install from marketplace
/plugin install all-skills@your-skills-repo

# Or install directly
claude plugin install <plugin-name>
```

### Key Advantage for Araseo

Plugins can bundle **skills + MCP servers + hooks** together — meaning Araseo could include the renderer as an MCP server that starts automatically when the plugin is loaded.

---

## 3. Distribution System C: GitHub Repository Direct

### What Is It?

The simplest approach — host skills in a GitHub repo, users clone or reference it.

### How It Works

Users manually copy or symlink the skill files into their `.claude/skills/` directory, or use `npx skills add` pointing to the GitHub repo.

```bash
# Via Vercel skills CLI
npx skills add 94wogus/araseo-skill

# Or manual
git clone https://github.com/94wogus/araseo-skill.git
cp -r araseo-skill/skills/araseo .claude/skills/
```

---

## 4. Vercel Deployment Pattern: Renderer Hosting

### Vercel Deploy Skill

Vercel provides an official **deploy skill** that enables deploying web apps directly from Claude Code conversations.

- **GitHub**: https://github.com/vercel/vercel-deploy-claude-code-plugin
- **No authentication required** — returns preview URL and claimable deployment link

### How It Works

```bash
# Install the deploy skill
npx skills add vercel-labs/agent-skills
```

Then in Claude Code:
```
Deploy this to Vercel
```

The skill:
1. Packages the project automatically
2. Detects the framework (Next.js, React, Vue, etc.)
3. Deploys to Vercel
4. Returns `previewUrl`, `claimUrl`, `deploymentId`, `projectId`

### Araseo Renderer on Vercel

The pattern for Araseo would be:

```
[Araseo Skill]                    [Araseo Renderer on Vercel]
Claude writes JSON via /araseo → JSON file in project
                                  ↓
                                  Renderer reads JSON
                                  ↓
                                  Interactive visual at vercel-url.vercel.app
```

**Two hosting approaches:**

#### Approach A: Local Renderer (like Flowy)
- Renderer runs at `localhost:PORT`
- Skill starts the dev server automatically
- No Vercel needed for the renderer itself
- Simple, fast, no external dependencies

#### Approach B: Vercel-Hosted Renderer
- Renderer deployed to Vercel as a web app
- JSON files uploaded or synced to the renderer
- Shareable URLs — anyone can view the diagrams
- Good for team collaboration and demos

#### Approach C: Hybrid
- Local dev server for real-time editing during development
- Vercel deployment for sharing and collaboration
- Use the Vercel Deploy skill to push to production when ready

---

## 5. Recommended Strategy for Araseo

### Phase 1: Local Development
1. Build the `/araseo` skill as a **standalone skill** in `.claude/skills/araseo/`
2. Build the renderer as a **local web app** (like Flowy at `localhost:4242`)
3. Test and iterate quickly

### Phase 2: Plugin Packaging
1. Convert to a **Claude Code plugin** with `.claude-plugin/plugin.json`
2. Bundle: skill + renderer start hook + MCP server (optional)
3. Test with `claude --plugin-dir ./araseo-plugin`

### Phase 3: Distribution
1. **Vercel skills ecosystem**: Publish to `npx skills add` for cross-agent support (35+ agents)
2. **Claude Code marketplace**: Publish as a plugin for rich Claude-specific features
3. **skills.sh listing**: Get listed on the directory for discoverability
4. **Vercel-hosted renderer**: Deploy renderer to Vercel for shareable diagram URLs

### Plugin Structure for Araseo (Proposed)

```
araseo-plugin/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── araseo/
│       └── SKILL.md          # /araseo:convert skill
├── commands/
│   └── araseo/
│       └── SKILL.md          # /araseo slash command
├── hooks/
│   └── hooks.json            # Auto-start renderer on plugin load
├── renderer/                  # Web app source
│   ├── package.json
│   ├── src/
│   └── ...
├── scripts/
│   └── start-renderer.sh     # Start local dev server
└── README.md
```

---

## 6. Reference Links

### Official Documentation
| Resource | URL |
|----------|-----|
| Vercel Skills Announcement | https://vercel.com/changelog/introducing-skills-the-open-agent-skills-ecosystem |
| Vercel Skills CLI (GitHub) | https://github.com/vercel-labs/skills |
| Vercel Agent Skills Collection | https://github.com/vercel-labs/agent-skills |
| skills.sh Directory | https://skills.sh/ |
| Claude Code Plugins Docs | https://code.claude.com/docs/en/plugins |
| Anthropic Official Skills | https://github.com/anthropics/skills |
| Vercel Deploy Plugin | https://github.com/vercel/vercel-deploy-claude-code-plugin |
| Agent Skills FAQ | https://vercel.com/blog/agent-skills-explained-an-faq |

### Related Articles
| Title | URL |
|-------|-----|
| Vercel Introduces Skills.sh (InfoQ) | https://www.infoq.com/news/2026/02/vercel-agent-skills/ |
| Vercel Add-Skill Guide (Medium) | https://medium.com/vibe-coding/the-easiest-way-to-extend-claude-code-cloudflare-complicates-it-4995e8b4cab3 |
| Claude Code Skills for Web Dev (Apidog) | https://apidog.com/blog/claude-code-skills-for-web-dev/ |

---

*Report generated: 2026-02-13*
*Researcher: Mole (몰)*
