---
description: Interactively map a user journey on an existing project
allowed-tools:
  [
    'mcp__plugin_cartographer_cartographer__whoami',
    'mcp__plugin_cartographer_cartographer__spaces_list',
    'mcp__plugin_cartographer_cartographer__spaces_projects_list',
    'mcp__plugin_cartographer_cartographer__projects_list',
    'mcp__plugin_cartographer_cartographer__find_project_by_name',
    'mcp__plugin_cartographer_cartographer__spaces_statuses_list',
    'mcp__plugin_cartographer_cartographer__projects_list_backbone',
    'mcp__plugin_cartographer_cartographer__projects_list_all_cards',
    'mcp__plugin_cartographer_cartographer__projects_get_card_details',
    'mcp__plugin_cartographer_cartographer__cards_bulk_create',
    'mcp__plugin_cartographer_cartographer__journeys_create',
    'mcp__plugin_cartographer_cartographer__steps_create',
    'mcp__plugin_cartographer_cartographer__story_cards_create',
    'mcp__plugin_cartographer_cartographer__milestones_create',
    'mcp__plugin_cartographer_cartographer__cards_update',
    'mcp__plugin_cartographer_cartographer__cards_move',
    'mcp__plugin_cartographer_cartographer__personas_list',
    'mcp__plugin_cartographer_cartographer__personas_create',
    'mcp__plugin_cartographer_cartographer__customers_list',
    'mcp__plugin_cartographer_cartographer__customers_create',
    'mcp__plugin_cartographer_cartographer__cards_personas_set',
    'mcp__plugin_cartographer_cartographer__cards_customers_set',
  ]
argument-hint: '<project name or journey description>'
---

# /map-journey

Interactively walk through and map a single user journey on an existing Cartographer project.

## Input

The user provides: $ARGUMENTS

This might be a project name, a journey description, or both. If unclear, ask.

## Process

1. **Find the project.** If the user provides a project name, call `find_project_by_name` to search for it. Otherwise call `projects_list` to browse, or `spaces_list` then `spaces_projects_list` to drill down by space. If ambiguous, ask.

2. **Read current state.** Call `projects_list_backbone` to understand the existing journey/step structure. If the project already has journeys, show them to the user.

3. **Identify the journey.** Ask the user about the high-level user activity:
   - "What is the user trying to accomplish?"
   - "What triggers this activity? What does completion look like?"
   - Use the answer to create a journey with `journeys_create` (or identify an existing one to extend).

4. **Walk through steps interactively.** For each step in the journey:
   - Ask: "What does the user do first? What happens next?"
   - Create the step with `steps_create`
   - Then probe deeper for that step:
     - "What should success look like here?" → create an `objective` card
     - "What are the main things that need to happen?" → create `none` cards
     - "What could go wrong?" → create `risk` cards
     - "What don't we know yet?" → create `question` or `knowledge_gap` cards
     - "Have we learned anything relevant from research?" → create `learn` cards
   - After each step, ask: "Is there a next step, or does this complete the journey?"

5. **Review.** After all steps are created, call `projects_list_backbone` again and present the completed journey structure.

## Conversation Style

This is an interactive, guided session. Don't create everything at once — walk the user through one step at a time, asking questions and building the map together. This mirrors how story mapping workshops work in person.

When the user dumps several steps or a whole batch of cards at once (rather than one at a time), use `cards_bulk_create` to create them in a single call instead of many — pass the new `steps` (anchored with the existing `journeyId`) or `cards` (anchored with the existing `stepId`) arrays.

Between steps, briefly summarize what's been created so far to maintain context.

## Output

After the journey is complete, present:

```
## Journey Mapped: [Journey Name]

### Steps
1. [Step 1] — [X cards]
2. [Step 2] — [X cards]
...

### Card Type Distribution
- Objectives: X
- Stories: X
- Risks: X
- Questions: X
- Knowledge gaps: X
- Learnings: X
```

Ask if the user wants to:

- Add more detail to any step
- Map another journey on the same project
- Add milestones for release planning
