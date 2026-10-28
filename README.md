# Cartographer

A plugin that turns Claude into a story mapping specialist. Build collaborative story maps, map user journeys, run product discovery sessions, and organize work using [Cartographer](https://cartographer.studio).

## What's Included

### Skills

Skills activate automatically when your request matches.

| Skill                    | Use for                                                                                                                       |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| `story-mapping`          | How Cartographer works: the 3-tier hierarchy, card types, estimates, dates, and tool orchestration. Loads for any board work. |
| `user-story-mapping`     | Building a new map from a brief, PRD, or idea                                                                                 |
| `interview-to-story-map` | Turning interview transcripts into an evidence-backed map                                                                     |
| `feedback-to-story-map`  | Theming support tickets, NPS comments, and feature requests onto the journey                                                  |
| `mvp-slicing`            | Walking skeleton, release slices, Now / Next / Later                                                                          |
| `story-splitting`        | Splitting big stories into small vertical ones (SPIDR, INVEST)                                                                |
| `story-map-review`       | Reviewing a map's structure, balance, and hidden assumptions                                                                  |
| `story-map-to-backlog`   | Writing tickets with acceptance criteria from a release slice                                                                 |
| `sales-discovery-map`    | Mapping a prospect's workflow and pains from discovery calls (MEDDICC)                                                        |
| `mutual-action-plan`     | A shared buyer and seller plan from evaluation to go-live                                                                     |
| `sales-playbook-map`     | A sales or CS playbook organized along the customer journey                                                                   |

The workflow skills are also available on their own, as a free skills pack for any AI agent, at https://cartographer.studio/skills.

### Commands

| Command            | Description                                                            |
| ------------------ | ---------------------------------------------------------------------- |
| `/create-project`  | Create a story map project from a product brief or feature description |
| `/create-persona`  | Build a rich, actionable persona through guided conversation           |
| `/map-journey`     | Interactively map a user journey step-by-step on an existing project   |
| `/project-review`  | Analyze a project's structure, balance, and completeness               |
| `/import-research` | Turn research notes or transcripts into typed story cards              |

### Agent: Board Builder

An autonomous agent that builds an entire story map project from a product brief, PRD, or research document without step-by-step prompting. Give it a document and it analyzes it, designs the project structure, and populates it with properly typed cards.

## Setup

### Install the plugin

In Claude Code:

```
/plugin install cartographer --marketplace lloydhumphreys/cartographer-claude
```

In claude.ai, Claude Desktop or Cowork, add Cartographer from **Customize > Plugins**.

### MCP Connection

The plugin connects to Cartographer's MCP server at `https://cartographer.studio/mcp`. OAuth authentication is handled automatically — you'll be prompted to sign in on first use.

No environment variables or API keys required.

## Privacy

The plugin runs no local code. Its only network connection is the Cartographer MCP server above, which reads and writes the spaces, projects and cards in your Cartographer account when Claude calls its tools. Story map views open `https://cartographer.studio/embed/...` inside the conversation. See the [privacy policy](https://cartographer.studio/legal/privacy).

## How It Works

Cartographer projects use a strict 3-tier hierarchy:

1. **Journeys** (tier 1) — Top-level columns representing high-level user activities
2. **Steps** (tier 2) — Phases within a journey
3. **Story Cards** (tier 3) — Individual work items with types like objective, risk, question, knowledge_gap, learn, pain_point, and opportunity

The plugin teaches Claude this hierarchy, the correct tool usage order, card type semantics, and story mapping best practices so that projects are well-structured and useful.

## Examples

**Create a project from a brief:**

```
/create-project We're building a collaborative project management tool for remote teams.
The core experience involves creating projects, assigning tasks, and tracking progress.
```

**Create a persona:**

```
/create-persona IT admin who manages the tool for a 50-person team
```

**Map a journey interactively:**

```
/map-journey user onboarding
```

**Review an existing project:**

```
/project-review My Product Project
```

**Import research findings:**

```
/import-research [paste your research notes or provide a file path]
```

**Autonomous project building:**

```
Here's our PRD for the new billing system. Can you create a full story map from this?
```
