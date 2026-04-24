---

name: aviator-prompt-optimizer
description: Rewrites and optimizes prompts for OpenText Content Aviator (Olli AI). Use this skill whenever the user wants to craft, improve, or rewrite a prompt intended for Aviator, or mentions "Aviator prompt", "Olli AI prompt", "Content Aviator", or asks to optimize a prompt for a RAG-based system that uses semantic search + LLM. Also trigger when the user pastes a draft prompt and says something like "make this work better in Aviator" or "optimize for Aviator". This skill is specifically designed for Aviator's Agentic RAG architecture, which differs fundamentally from prompting ChatGPT or Claude directly.
user-invocable: true
--------------------

# Aviator Prompt Optimizer

## Step 0: Version Echo

Read the plugin version from `${CLAUDE_SKILL_DIR}/../../.claude-plugin/plugin.json` and echo it:

```bash
echo "aviator-prompt-optimizer v$(jq -r .version ${CLAUDE_SKILL_DIR}/../../.claude-plugin/plugin.json)"
```

## Why Aviator needs different prompts

Transforms casual, generic, or ChatGPT-style prompts into prompts optimized for OpenText Content Aviator's Agentic RAG pipeline.

Aviator does NOT work like ChatGPT or Claude. When a user sends a prompt, it does NOT go straight to an LLM. Instead, a multi-step process happens:

1. **Semantic Search (PG Vector)** — The prompt first hits an internal vector search that finds relevant document chunks by similarity. This step has zero GenAI intelligence — it matches on keywords and semantic proximity. If this step fails to find the right chunks, the LLM never sees the relevant content.
2. **Agentic Orchestration** — An agent pipeline rephrases, routes, and scores the retrieved chunks.
3. **LLM Generation (currently Gemini)** — Only now does a language model get involved. It receives: the retrieved chunks as context, a rephrased version of the user prompt, and the original prompt.

The critical insight: **a prompt must serve two masters simultaneously** — the semantic search engine (which needs specific keywords) AND the LLM (which needs formatting instructions, tone, persona).

If the information isn't found in the documents, Aviator deliberately returns "No Answer" rather than hallucinating. This is a trust feature, not a bug.

## Transformation Rules

When optimizing a prompt, apply ALL of the following rules systematically:

### Rule 1: Directives, not conversation

Replace casual language with action verbs at the start of the prompt.

| Instead of...                                   | Use...                                  |
| ----------------------------------------------- | --------------------------------------- |
| I need / I'm looking for / Can you help me with | **Provide** / **Create** / **Generate** |
| Could you maybe summarize                       | **Summarize**                           |
| I was wondering about                           | **Explain** / **List** / **Identify**   |
| Help me draft                                   | **Draft**                               |
| Tell me about                                   | **Describe** / **Outline**              |

**Example:**

- Before: "Hey, I'm looking for some info on how we handle document retention"
- After: "Provide a detailed overview of OpenText document retention policies and compliance capabilities"

### Rule 2: Remove filler words

Strip anything that adds noise for the semantic search without adding meaning. These words consume similarity-matching budget without helping find the right chunks.

Remove: "please", "could you", "I think", "maybe", "kind of", "just", "actually", "basically", "I would like to", "it would be great if", "help me"

### Rule 3: Keyword specificity for semantic search

This is the most impactful rule. The semantic search needs precise terms to find the right document chunks. Generic terms cause the search to grab irrelevant chunks and stop early.

- Name the **specific product or solution area** (e.g., "OpenText Content Management" not just "OpenText")
- Name the **specific capability** (e.g., "employee onboarding workflow automation" not just "HR stuff")
- Include **synonyms and abbreviations** in parentheses so the vector search has multiple matching opportunities: "Human Resources (HR)", "Static Application Security Testing (SAST)", "Software Composition Analysis (SCA)"

### Rule 4: Scope constraints — be strict, not soft

Constrain the output so both the search and the LLM know what to focus on. Use hard, numeric language rather than hedged adjectives.

- Exact counts: **"exactly 4 bullet points"**, "top 5 key points", "3 main benefits"
- Hard limits: **"strictly under 200 words"**, "no more than 3 paragraphs" — stronger than "short" or "brief"
- Specify a domain: "for manufacturing", "in the healthcare sector", "for DACH region customers"
- Specify depth: "comprehensive", "high-level overview", "detailed technical breakdown"

### Rule 5: Dual-targeting (Semantic Search + LLM)

Structure the prompt so the first half targets the semantic search and the second half targets the LLM.

**Semantic search block** (first half):

- Action directive + specific topic + keywords + synonyms

**LLM instruction block** (second half):

- Audience/persona: "This is for a CISO", "Write as a sales rep addressing an SVP"
- Tone: "professional", "executive-level", "technical"
- Format: "as a numbered list", "in table format with columns X, Y, Z", "as an executive summary"
- Role: "I am a Solution Consultant at OpenText"

### Rule 6: Incremental refinement via chaining

For complex needs, guide the user to break it into chained prompts within the same Aviator chat window (Aviator retains context per session):

1. First prompt: Get the core content
2. Second prompt: Reshape or restructure ("Now format the above as a table with columns: Feature, Benefit, Use Case")
3. Third prompt: Refine tone or audience ("Rewrite this for a C-level audience, max 200 words")

### Rule 7: Open with a persona frame

Start the prompt with an "Act as…" or "You are…" directive that names a **specific role AND specialization**. This anchors the LLM's voice and scope before any task details are given. Vague persona ("act as an expert") underperforms specific persona.

- Weak: "Act as a sales expert."
- Strong: "Act as a strategic OpenText Account Executive specialized in competitive displacement in regulated industries."
- Strong: "You are an expert Outbound Consultant specialized in hyper-personalized, executive-level cold outreach."

Persona-first is different from the audience/tone fields in Rule 5: it defines **who is writing**, not who is reading.

### Rule 8: Guardrails — say what NOT to produce

Negative constraints narrow the output space as much as positive ones. Include them when the task has a predictable failure mode.

- **Forbidden phrases**: e.g. `Avoid generic pleasantries like "I hope this finds you well."`
- **Forbidden patterns**: "No filler, no jargon.", "Do not use bracketed placeholders in the final output."
- **No empty brackets**: If your prompt expects the LLM to fill fields from context, add: "If a field is not explicitly provided, synthesize from the attached materials or use professional judgment — do not leave empty brackets."

### Rule 9: Prefer structured output formats for multi-dimensional asks

When the ask has multiple dimensions, request a **table with named columns** or a **named framework** rather than free prose. Structured formats force the LLM to address each dimension and are easier to consume.

- Tables with named columns: "Format as a table with columns: Question, Target Role, Strategic Justification."
- Named framework categories: "Organize under: Frustration, Owner, Criteria, Urgency, Stakeholder — with 3 items per category."
- Labeled sections: "Provide three distinct sections labeled Competitive Seeds, FUD, and Proactive Landmines."

End the prompt with an explicit **"Output format:"** line when the format matters.

## Reference material

`references/prompt-templates.md` contains a curated set of official Aviator prompt templates (outreach emails, discovery calls, FOCUS framework, competitive positioning, objection handling). Consult this file on demand — do not auto-load — when the user's request closely matches one of these use cases and you want concrete patterns to draw from. Do not copy templates wholesale; extract the structural pattern and apply the rules above.

## Output Format

When the user provides a prompt to optimize, respond with:

### 1. Analysis

Briefly explain what's weak about the original prompt in the context of Aviator's architecture (which master — semantic search or LLM — is underserved).

### 2. Optimized Prompt

The rewritten prompt, ready to paste into Aviator. Present it in a fenced code block for easy copying.

### 3. Workspace Reminder

Remind the user: "Make sure you've selected the right Aviator workspace and checked the relevant 'Related' tabs (products, proposals, technical docs) before submitting this prompt — the workspace scope determines which documents the semantic search can access."

### 4. Chaining Suggestion (if applicable)

If the task is complex enough to benefit from chaining, suggest 2-3 follow-up prompts.

## Language Handling

Detect the language of the input prompt and produce the optimized prompt in the same language. If the input is German, output in German. If English, output in English. Directives should match the language:

- EN: Provide, Create, Generate, Draft, Summarize, List, Identify, Compare
- DE: Erstelle, Generiere, Fasse zusammen, Liste auf, Identifiziere, Vergleiche, Beschreibe, Erarbeite

## Examples

**Example 1 — Vague English prompt:**

Input: "I need some info about how OpenText helps with supply chain"

Optimized:

```javascript
Provide a comprehensive overview of the top 5 ways OpenText Content Management supports supply chain optimization, including document automation, supplier onboarding, and compliance tracking.

Audience: Supply Chain Director at a manufacturing enterprise.
Tone: Professional, executive-level.
Format: Numbered list with a brief explanation (2-3 sentences) per point.
```

**Example 2 — German prompt, too casual:**

Input: "Kannst du mir bitte was zusammenfassen wie unser Produkt bei der digitalen Transformation hilft?"

Optimized:

```javascript
Fasse die 5 wichtigsten Vorteile von OpenText Content Management für die digitale Transformation zusammen, insbesondere in den Bereichen Dokumentenautomatisierung, Workflow-Optimierung und Compliance-Management.

Zielgruppe: IT-Entscheider (CIO/CTO) in einem mittelständischen Unternehmen.
Ton: Professionell, auf Entscheider-Ebene.
Format: Nummerierte Liste mit jeweils 2-3 Sätzen Erklärung pro Punkt.
```

**Example 3 — Complex prompt that benefits from chaining:**

Input: "I need to answer an RFP about our content management capabilities for a healthcare customer"

Optimized (Prompt 1 of 3):

```javascript
List the top 10 OpenText Content Management capabilities relevant to the healthcare sector, including regulatory compliance (HIPAA, GDPR), electronic health record (EHR) integration, and clinical document management.

Format: Numbered list with capability name and one-sentence description each.
```

Chaining suggestions:

- Prompt 2: "Expand each capability into a detailed paragraph suitable for an RFP response. Include specific product features and customer benefits."
- Prompt 3: "Reformat the above into a professional RFP response table with columns: Requirement, OpenText Capability, Key Benefits, Relevant Product Module."
