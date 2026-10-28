---
description: Create a rich, actionable persona through guided conversation
allowed-tools:
  [
    'mcp__plugin_cartographer_cartographer__whoami',
    'mcp__plugin_cartographer_cartographer__spaces_list',
    'mcp__plugin_cartographer_cartographer__spaces_projects_list',
    'mcp__plugin_cartographer_cartographer__projects_list',
    'mcp__plugin_cartographer_cartographer__find_project_by_name',
    'mcp__plugin_cartographer_cartographer__personas_list',
    'mcp__plugin_cartographer_cartographer__personas_create',
    'mcp__plugin_cartographer_cartographer__personas_update',
  ]
argument-hint: '<persona name, role, or rough description>'
---

# /create-persona

Build a concrete, actionable persona through guided conversation. The goal is a persona that actually changes how the team makes decisions — not a demographic profile that sits in a slide deck.

## Input

The user provides: $ARGUMENTS

This might be a name ("Power User"), a role ("IT admin at a mid-size company"), a rough description, research notes, or just a vague idea. Whatever they give you is the starting point.

## What Makes a Useful Persona

Most personas fail because they describe demographics instead of behavior. A useful persona for story mapping answers:

- **What is this person trying to accomplish with the product?** Goals in their own language, not product-speak.
- **What does their day actually look like?** The context in which they reach for this tool — time pressure, frequency, environment.
- **What are they worried about?** The fears, uncertainties, and risks that make them hesitate.
- **How do they solve this today?** Their current workarounds, tools, manual processes — the thing your product replaces.
- **What would make them say "this is worth it"?** The moment of value that justifies the cost of switching.
- **What would make them give up?** The friction that would send them back to their old way.

The persona should be specific enough that two designers reading it would make the same product decision. If the persona could describe anyone, it describes no one.

## Process

### Phase 1: Understand what exists

Take whatever the user gives you and identify what you already know and what's missing. Don't ask questions you can answer from the input — that's annoying. Instead, reflect back what you've understood and go straight to the gaps.

### Phase 2: Ask sharp questions

Run an interactive Q&A to fill gaps. Don't ask all questions at once — group them into 2-3 focused rounds. Pick the most impactful gaps first.

**Round 1 — Context and goals.** Understand who this person is in relation to the product:

- What's their relationship to the product? (buyer, daily user, occasional user, admin, evaluator)
- What are they trying to accomplish? Not "use the product" — the real-world outcome they care about.
- How often and in what context do they interact? (daily workflow, weekly check-in, emergency use, exploration)

**Round 2 — Tension and motivation.** Understand the emotional and practical forces at play:

- What's their biggest frustration or fear around this problem space? What keeps them up at night?
- How do they solve this today? What's good and bad about their current approach?
- What would make them say "yes, this is better"? What's the aha moment?
- What would make them walk away? What friction is a dealbreaker?

**Round 3 (if needed) — Sharpening.** Drill into anything that's still vague:

- Can you give me a specific example of when they'd use this?
- What's a decision this persona would make differently than [other persona]?
- What does success look like to them — how would they describe it to their boss?

After each round, briefly reflect back what you've captured to confirm understanding. Don't robotically list all questions — read the room. If the user gives rich answers, skip ahead. If they're terse, probe deeper on the most important dimension.

### Phase 3: Draft the persona

Synthesize everything into a structured persona description. Present it to the user for review before creating anything in Cartographer.

Use this structure for the description (stored in the persona's `description` field):

```
**Role:** [One line — their relationship to the product]

**Goals**
- [Primary goal — the real-world outcome, not a product action]
- [Secondary goal]

**Context**
[When/where/how often they interact. What's happening around them. 2-3 sentences.]

**Current reality**
[How they solve this today. What works, what doesn't. 2-3 sentences.]

**Fears & frustrations**
- [What worries them about this problem space]
- [What friction they absolutely won't tolerate]

**Aha moment**
[The specific moment where this product becomes worth it to them. One concrete sentence.]

**Dealbreaker**
[What would send them back to their old way. One sentence.]
```

Keep it tight. Every line should be something the team can act on. Cut anything that doesn't help make a product decision.

**Choosing a name:** If the user gave a role-based name like "Power User" or "Admin," keep it. If they gave a generic description, suggest a short, memorable name. Avoid cutesy alliterative names ("Marketing Mary") — they trivialize the persona. Role-based names ("Field Technician", "Team Lead", "New Hire") are better because they're instantly recognizable.

### Phase 4: Create in Cartographer

After the user approves:

1. Call `spaces_list` to find the target space. If the user hasn't specified, ask.
2. Call `personas_list` to check for existing personas in the space — avoid duplicates.
3. Call `personas_create` with the name and the full structured description.
4. Suggest a color only if there are other personas in the space and cross-cutting color coding would be useful. Otherwise leave it null.

### Phase 5: Connect to the map

After creating the persona, offer next steps:

- "Want me to tag existing cards on a project with this persona?"
- "Want to create another persona? Most products need 2-3."
- "Want to map this persona's journey with `/map-journey`?"

## Conversation Style

This is a collaborative conversation, not a form to fill out. Be curious. Ask follow-up questions when something interesting surfaces. Push back gently if the persona is too generic — "That could describe almost anyone who uses software. What's specific to _this_ person?"

Don't be afraid to suggest concrete details and ask the user to confirm or correct. "It sounds like this person checks in once a week, mostly on Monday mornings to plan the week — is that right?" is better than "How often do they use it?"

## Guidelines

- **No demographics unless relevant.** Age, location, and hobbies don't help unless they directly affect product behavior. A field technician working outdoors on a phone is relevant context. "Lives in Portland, likes hiking" is not.
- **Specific > generic.** "Wants to save time" is useless. "Needs to generate the weekly status report in under 10 minutes before the 9am standup" is actionable.
- **Behavior > identity.** Define personas by what they do and why, not who they are.
- **Conflict is good.** The most useful personas have tension — they want speed but also thoroughness, they need control but don't have time to configure things. This tension generates design insight.
- **2-3 personas per product.** If the user already has 3+ personas, ask whether this is genuinely distinct before adding another. More than 4 personas usually means some can be merged.
