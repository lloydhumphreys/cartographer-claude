---
description: Turn research notes or transcripts into typed story cards
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
    'mcp__plugin_cartographer_cartographer__comments_create',
    'mcp__plugin_cartographer_cartographer__personas_list',
    'mcp__plugin_cartographer_cartographer__personas_create',
    'mcp__plugin_cartographer_cartographer__customers_list',
    'mcp__plugin_cartographer_cartographer__customers_create',
    'mcp__plugin_cartographer_cartographer__cards_personas_set',
    'mcp__plugin_cartographer_cartographer__cards_customers_set',
    'Read',
    'Glob',
  ]
argument-hint: '<research notes, file path, or paste content>'
---

# /import-research

Parse user research (interview notes, survey results, usability test findings, transcripts) and turn the insights into typed story cards on a Cartographer project.

## Input

The user provides: $ARGUMENTS

This might be:

- Pasted research notes or transcript
- A file path to read
- A description of findings to capture

If a file path is provided, read it with the Read tool. If content is pasted directly, work with it inline.

## Process

1. **Parse the research.** Read through the provided material and extract:
   - **Insights / Learnings** — Validated observations about user behavior, preferences, or needs
   - **Questions** — Things that came up but weren't answered
   - **Risks** — Concerns, pain points, or potential failure modes discovered
   - **Knowledge gaps** — Areas where more research is clearly needed
   - **Objectives** — Goals or desired outcomes mentioned by users

2. **Present extracted items.** Before creating any cards, show the user what was extracted:

   ```
   ## Extracted from Research

   ### Learnings (X items)
   - [insight 1]
   - [insight 2]

   ### Questions (X items)
   - [question 1]

   ### Risks (X items)
   - [risk 1]

   ### Knowledge Gaps (X items)
   - [gap 1]
   ```

   Ask the user to confirm, edit, or add to the list before proceeding.

3. **Find the target project.** If the user mentions a project name, call `find_project_by_name` to locate it. Otherwise call `projects_list` to browse or `spaces_list` then `spaces_projects_list` to drill down. Ask the user which project to add cards to, or create a new one.

4. **Read project state.** Call `projects_list_all_cards` to understand the existing structure. It's paginated (default 200 items per page, max 500) — keep passing the response's `nextCursor` back as `cursor` until it comes back `null` so large projects aren't silently truncated.

5. **Map items to steps.** For each extracted item, determine which journey and step it belongs to:
   - If the project has existing steps that match, place the card there
   - If new journeys or steps are needed, suggest them to the user before creating
   - Group related items together under the same step

6. **Create cards.** When adding several cards at once, use `cards_bulk_create` in a single call rather than one `story_cards_create` per item — pass its anchored `cards` array (each card with the existing `stepId`), and add any new `steps` (with their `journeyId`) or `journeys` in the same call. Use `story_cards_create` only for a one-off card. Pick the type per item:
   - Insights → `learn` type
   - Questions → `question` type
   - Risks → `risk` type
   - Knowledge gaps → `knowledge_gap` type
   - Objectives → `objective` type

   Include the source context in the card description (e.g., "From user interview with [participant], [date]").

7. **Add provenance comments.** For cards where specific quotes or data support the insight, use `cards_update` to append the supporting evidence to the description of the card.

## Output

```
## Research Import Complete

### Cards Created: X
| Card | Type | Journey > Step |
|------|------|----------------|
| [title] | learn | [Journey] > [Step] |
| [title] | risk | [Journey] > [Step] |
...

### New Structure Created
- [Any new journeys or steps that were added]

### Items Not Placed
- [Any items that didn't fit the current project structure, with suggestions]
```

## Guidelines

- Prefer `learn` cards for research findings — they're the primary output of research import.
- Always include source attribution in the card description.
- Don't create cards with vague titles. "Users found X confusing" is better than "Usability issue".
- Group related findings rather than creating one card per sentence.
- If the research suggests structural changes to the project (new journeys or steps), propose them but let the user decide.
