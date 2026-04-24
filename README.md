# Aviator Prompt Optimizer

An AI agent skill that teaches Claude, Codex, Gemini and other AI agents how to rewrite prompts for **[OpenText Content Aviator](https://www.opentext.com/products/content-aviator) (Olli AI)** — transforming casual or ChatGPT-style prompts into prompts optimized for Aviator's Agentic RAG pipeline (semantic search + LLM).

## Skill

| Skill | Description |
|-------|-------------|
| **aviator-prompt-optimizer** | Rewrites prompts so they serve both semantic retrieval (keywords) and the LLM (tone, persona, format) |

## Prerequisites

- An AI assistant that supports Agent Skills (Claude Code, Claude.ai web, GitHub Copilot, OpenAI Codex, Gemini CLI, etc.)
- Access to OpenText Content Aviator (to actually use the optimized prompts)

## Installation

### Claude Code

Add the marketplace from GitHub, then install the plugin:

```bash
claude plugin marketplace add janwienand/aviator-prompt-optimizer-marketplace
claude plugin install aviator-prompt-optimizer@aviator
```

Equivalent inside a running Claude Code session:

```
/plugin marketplace add janwienand/aviator-prompt-optimizer-marketplace
/plugin install aviator-prompt-optimizer@aviator
```

### Claude.ai (Web / Desktop App)

Custom Skills are available on **Pro, Max, Team, and Enterprise** plans with code execution enabled.

1. Download the skill ZIP from the latest [GitHub release](https://github.com/janwienand/aviator-prompt-optimizer-marketplace/releases/latest) (`aviator-prompt-optimizer.zip`).
2. In [claude.ai](https://claude.ai), open **Settings → Capabilities → Skills** (also reachable via **Customize → Skills**).
3. Click **+ → Create skill → Upload a skill**.
4. Select `aviator-prompt-optimizer.zip`.

> The ZIP must contain the skill folder at its root, with `SKILL.md` inside. The provided release ZIP is already packaged correctly.

Prefer to build the ZIP yourself?

```bash
git clone https://github.com/janwienand/aviator-prompt-optimizer-marketplace
cd aviator-prompt-optimizer-marketplace/skills
zip -r ../aviator-prompt-optimizer.zip aviator-prompt-optimizer
```

### OpenAI Codex

This repository includes a marketplace catalog at `.agents/plugins/marketplace.json`. When the repo is your current workspace, Codex discovers it automatically as a repo-scoped marketplace. Open the plugin directory, select **Aviator Prompt Optimizer**, and install **aviator-prompt-optimizer**.

To make the plugin available across all workspaces, add an entry to your personal marketplace at `~/.agents/plugins/marketplace.json` (create the file if it doesn't exist), replacing `<path>` with the absolute path to this directory:

```json
{
  "name": "aviator",
  "interface": { "displayName": "Aviator Prompt Optimizer" },
  "plugins": [
    {
      "name": "aviator-prompt-optimizer",
      "source": { "source": "local", "path": "<path>" },
      "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
      "category": "Productivity"
    }
  ]
}
```

Then restart Codex.

### Gemini CLI

Install directly from the GitHub repository:

```bash
gemini extensions install https://github.com/janwienand/aviator-prompt-optimizer-marketplace
```

Gemini CLI auto-discovers the skill and activates it when relevant.

### GitHub Copilot

Copy the `skills/aviator-prompt-optimizer/` folder into your Copilot skills directory (`<user>/.copilot/skills/`).

### Other AI assistants

Any assistant that supports the [Agent Skills](https://agentskills.io) standard can load skills from this repo. Point your assistant's skill path at the `skills/` subdirectory.

## Usage

Once installed, the skill activates automatically when you ask to optimize a prompt for Aviator / Olli. Example triggers:

- "Optimize this prompt for Aviator: …"
- "Mach diesen Prompt Olli-tauglich."
- "Rewrite this RAG prompt for Content Aviator."

The skill walks through persona, context, task, and output format, then returns a structured, Aviator-ready prompt.

## Resources

- [OpenText Content Aviator](https://www.opentext.com/products/content-aviator)
- [Anthropic — Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Claude.ai — Use Skills](https://support.claude.com/en/articles/12512180-use-skills-in-claude)

## License

MIT
