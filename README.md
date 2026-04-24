# Aviator Prompt Optimizer — Claude Code Marketplace

Ein öffentlicher [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) Plugin-Marketplace, der den **Aviator Prompt Optimizer** Skill bereitstellt.

Der Skill formuliert Prompts für **OpenText Content Aviator (Olli AI)** um — er verwandelt lockere oder ChatGPT-artige Prompts in Prompts, die für Aviators Agentic-RAG-Pipeline (semantische Suche + LLM) optimiert sind.

## Voraussetzungen

- [Claude Code](https://docs.claude.com/en/docs/claude-code/overview) installiert (`npm install -g @anthropic-ai/claude-code` oder via IDE)

## Installation

In einer laufenden Claude Code Session:

```
/plugin marketplace add jwienand/aviator-prompt-optimizer-marketplace
/plugin install aviator-prompt-optimizer@aviator-prompt-optimizer-marketplace
```

> Passe `jwienand/...` an den tatsächlichen GitHub-Pfad an, falls das Repo unter einem anderen Namen / Account liegt.

Alternativ lokal (z. B. zum Testen vor dem Push):

```
/plugin marketplace add /pfad/zu/aviator-prompt-optimizer-marketplace
/plugin install aviator-prompt-optimizer@aviator-prompt-optimizer-marketplace
```

## Nutzung

Nach der Installation wird der Skill automatisch aktiviert, sobald du Claude Code bittest, einen Prompt für Aviator / Olli zu optimieren. Beispiele:

- „Optimiere diesen Prompt für Aviator: …"
- „Mach diesen Prompt Olli-tauglich."
- „Schreib diesen RAG-Prompt für Content Aviator um."

Der Skill führt dich durch Persona, Kontext, Aufgabe und Output-Format und gibt einen strukturierten, Aviator-optimierten Prompt zurück.

## Updates

```
/plugin marketplace update aviator-prompt-optimizer-marketplace
/plugin install aviator-prompt-optimizer@aviator-prompt-optimizer-marketplace
```

## Deinstallation

```
/plugin uninstall aviator-prompt-optimizer@aviator-prompt-optimizer-marketplace
/plugin marketplace remove aviator-prompt-optimizer-marketplace
```

## Aufbau

```
aviator-prompt-optimizer-marketplace/
├── .claude-plugin/
│   └── marketplace.json          # Marketplace-Katalog
├── plugins/
│   └── aviator-prompt-optimizer/
│       ├── .claude-plugin/
│       │   └── plugin.json       # Plugin-Metadaten
│       └── skills/
│           └── aviator-prompt-optimizer/
│               ├── SKILL.md
│               └── references/
│                   └── prompt-templates.md
└── README.md
```

## Lizenz / Kontakt

Maintained by Jan Wienand (OpenText). Feedback & Issues gerne via GitHub.
