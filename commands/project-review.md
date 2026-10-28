---
description: Analyze a project's structure and suggest improvements
allowed-tools:
  [
    'mcp__cartographer__whoami',
    'mcp__cartographer__spaces_list',
    'mcp__cartographer__spaces_projects_list',
    'mcp__cartographer__projects_list',
    'mcp__cartographer__find_project_by_name',
    'mcp__cartographer__spaces_statuses_list',
    'mcp__cartographer__projects_list_backbone',
    'mcp__cartographer__projects_list_all_cards',
    'mcp__cartographer__projects_get_card_details',
    'mcp__cartographer__milestones_list',
    'mcp__cartographer__personas_list',
    'mcp__cartographer__customers_list',
  ]
argument-hint: '<project name>'
---

# /project-review

Analyze a Cartographer project's structure, balance, and completeness, then suggest improvements.

## Input

The user provides: $ARGUMENTS

If no project name is provided, list available projects and ask which one to review.

## Process

1. **Find the project.** If the user provides a project name, call `find_project_by_name` to search for it. Otherwise call `projects_list` to browse, or `spaces_list` then `spaces_projects_list` to drill down by space.

2. **Load full state.** Call `projects_list_all_cards` to get every card with tier, type, parent, and milestone info. It's paginated (default 200 items per page, max 500) — keep passing the response's `nextCursor` back as `cursor` until it comes back `null` before analyzing, so large projects aren't silently truncated. Also call `milestones_list` to get milestone details. Optionally call `spaces_statuses_list` to understand the space's workflow states.

3. **Analyze structure.** Evaluate the project across these dimensions:

### Structural Balance

- **Journey count**: Is it between 3-6? Fewer suggests too narrow a scope, more suggests too broad.
- **Steps per journey**: Are there 2-5 steps per journey? Flag journeys with 0-1 steps (too shallow) or 6+ steps (may need splitting).
- **Cards per step**: Are steps roughly balanced? A step with 15 cards next to one with 1 card suggests an imbalance.
- **Empty steps**: Any steps with zero story cards?

### Type Coverage

- **Type distribution**: What percentage of cards are `none` vs typed? A project with >80% `none` cards is missing analytical depth.
- **Objectives**: Does each step have at least one objective? Missing objectives means the team hasn't defined success.
- **Risks**: Are risks surfaced? A project with zero risk cards is likely hiding assumptions.
- **Questions**: Are open questions captured? Zero questions suggests premature certainty.
- **Knowledge gaps**: Any areas where the team acknowledges they need to learn more?
- **Learnings**: Any evidence-based insights grounding the map?

### Milestone Usage

- **Milestone presence**: Does the project use milestones? If it's a planning project, it should.
- **Card distribution**: Are cards spread across milestones or all in backlog?
- **Balance**: Is one milestone overloaded while others are empty?

### Status Usage

- **Status assignment**: Are story cards assigned statuses?
- **Progress distribution**: What's the breakdown across status categories?

### Narrative Flow

- **Left-to-right reading**: Do the journeys tell a coherent user story when read left to right?
- **Step sequence**: Within each journey, do steps follow a logical order?

## Output

```
## Project Review: [Project Name]

### Structure
| Journey | Steps | Cards | Objectives | Risks | Questions |
|---------|-------|-------|------------|-------|-----------|
| [name]  | X     | X     | X          | X     | X         |
...
| **Total** | **X** | **X** | **X** | **X** | **X** |

### Findings

#### Strengths
- [What the project does well]

#### Issues
- [Severity: High/Medium/Low] [Issue description and recommendation]

### Recommendations
1. **[Most impactful improvement]** — [Why and how]
2. **[Second priority]** — [Why and how]
3. **[Third priority]** — [Why and how]
```

## Guidelines

- Be constructive, not just critical. Acknowledge what's working.
- Prioritize recommendations by impact.
- Don't make changes — only read and analyze. The user decides what to act on.
- If the project is mostly empty, suggest running `/create-project` or `/map-journey` instead.
