---
description: Create a story map project from a brief or product description
allowed-tools:
  [
    'mcp__cartographer__whoami',
    'mcp__cartographer__spaces_list',
    'mcp__cartographer__spaces_create',
    'mcp__cartographer__spaces_projects_list',
    'mcp__cartographer__projects_list',
    'mcp__cartographer__find_project_by_name',
    'mcp__cartographer__spaces_statuses_list',
    'mcp__cartographer__projects_create',
    'mcp__cartographer__projects_update',
    'mcp__cartographer__projects_list_backbone',
    'mcp__cartographer__projects_list_all_cards',
    'mcp__cartographer__cards_bulk_create',
    'mcp__cartographer__journeys_create',
    'mcp__cartographer__steps_create',
    'mcp__cartographer__story_cards_create',
    'mcp__cartographer__milestones_create',
    'mcp__cartographer__cards_update',
    'mcp__cartographer__cards_move',
    'mcp__cartographer__personas_list',
    'mcp__cartographer__personas_create',
    'mcp__cartographer__customers_list',
    'mcp__cartographer__customers_create',
    'mcp__cartographer__cards_personas_set',
    'mcp__cartographer__cards_customers_set',
  ]
argument-hint: '<product brief or feature description>'
---

# /create-project

Create a Cartographer story map project from a product brief, feature description, or high-level idea.

## Input

The user provides: $ARGUMENTS

If no arguments are provided, ask the user to describe the product, feature, or user experience they want to map.

## Process

1. **Parse the brief.** Identify the target user, their goals, and the key activities they perform. Extract any mentions of timeline, releases, or phases.

2. **Find or create a space.** Call `spaces_list` to see existing spaces. Ask the user which space to use, or create a new one with `spaces_create` if needed.

3. **Create the project.** Call `projects_create` with `spaceId`, a descriptive name, and a brief description summarizing the scope.

4. **Design the full structure, then build it in one call.** Plan 3-6 tier-1 journeys (ordered left-to-right as a narrative), 2-5 steps each, the story cards under each step, and any milestones. Then create everything with a single `cards_bulk_create` call:
   - Pass a nested tree: `journeys` → nested `steps` → nested `cards`, plus `milestones`.
   - Within each step, order cards deliberately: an `objective` framing success, then `none` work items, then `risk` (what could go wrong), `question` (decisions to make), `knowledge_gap` (needs research), and `learn` (validated insights).
   - Milestone patterns: "MVP" / "V1.1" / "Future", or sprint-based. To assign a card to a milestone created in the same call, give the milestone a `tempId` and set the card's `milestoneId` to it.
   - The tool handles tier order/positioning and validates the whole batch before creating anything. For very large maps, split into a few calls (e.g. one per journey). Use the single-item tools (`journeys_create`, `steps_create`, `story_cards_create`) only for incremental additions afterward.

5. **Look up statuses first if needed.** If the user wants cards assigned to workflow statuses, call `spaces_statuses_list` before the bulk call to discover available status IDs. Never guess status IDs.

## Output

After creating the project, present a summary:

```
## Project Created: [Project Name]

### Journeys
1. [Journey 1] — [X steps, Y cards]
2. [Journey 2] — [X steps, Y cards]
...

### Milestones
- [Milestone 1]
- [Milestone 2]

### Summary
- Total journeys: X
- Total steps: X
- Total story cards: X
- Card types: X objectives, X risks, X questions, X knowledge gaps, X learnings

[Link to the project if available]
```

Ask the user if they'd like to adjust anything — add more detail to specific journeys, rebalance the structure, or add milestones.

## Guidelines

- Do NOT assign colors unless the brief mentions multiple personas or teams.
- Do NOT skip the step layer — every journey must have at least one step.
- Prefer more typed cards (objectives, risks, questions) over generic `none` cards.
- Keep journey count between 3-6 and step count between 2-5 per journey.
- Read left to right as a user narrative: "First they do X, then Y, then Z."
