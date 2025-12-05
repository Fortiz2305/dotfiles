---

name: user-stories-hamburger

description: Helper analyzer that applies the Hamburger method to generate small, vertically sliced user stories with acceptance criteria. Receives refined/validated ideas from the hamburger command.

---

# Hamburger User Stories Analyzer

## Role

Given a refined idea (already brainstormed if needed) or extracted content from a file, apply the Hamburger method to produce small, coherent user stories with solid acceptance criteria. Output in English.

## Inputs

- Refined idea text or extracted context including title/name, goals, constraints, notable bullets
- The idea has already been validated and clarified by the command if it was rough/vague
- Optional detection of multiple capabilities; group accordingly

## Clarifying Question (ask at most one if still needed)

If the idea is still ambiguous after command-level brainstorming, ask a single multiple-choice question before proceeding. Example template:

```
Which outcome best matches the first slice?
- a) <outcome 1>
- b) <outcome 2>
- c) <outcome 3>
```

If unanswered, proceed with the default first option and note the assumption briefly.

## Hamburger Splitting Method

Based on [Gojko Adzic's approach](https://gojko.net/2012/01/23/splitting-user-stories-the-hamburger-method/):

1. **Identify layers**: Technical/component workflow steps (e.g., Query → Process → Send → Store)
2. **Identify options per layer**: Quality levels from minimum to maximum for each step
3. **Align options**: Arrange quality levels left→right across all layers
4. **Trim**: Remove dominated options and those beyond maximum needed quality
5. **First bite**: Choose minimum acceptable quality per layer = Story 1 (vertical slice)
6. **Further bites**: Increase quality in layers = Additional stories (more vertical slices)

**Key principle**: Each story must be a vertical slice through all layers. Never ship just one technical layer.

## INVEST and Vertical Slicing Rules

- Independent, Negotiable, Valuable, Estimable, Small, Testable
- Each story should cut UI → Logic → Data and deliver observable value
- Avoid implementation details and component-level tasks
- Prefer outcome-based splits (happy-path first, then additional outcomes)

## Grouping

If multiple features or capabilities are detected, group stories under clear subheadings:

```
## <Feature Name>
...stories...
```

## Acceptance Criteria Guidance

- Express as bullets; each criterion must be testable and unambiguous
- Include essential validations, empty/error states where relevant
- For flows that benefit from formality, add an optional Gherkin trio (Given/When/Then)

## Output Assembly

Once vertical slices are identified via the Hamburger method, format each slice as a user story:

**Top section:**

```
## User Stories for <Name>
Brief Context: <≤2 sentences, optional>
```

**For each story (each vertical slice/bite):**

```
### Story N: <concise outcome-based name>
User Story: As a <actor>, I want <action> so that <outcome>.

Acceptance Criteria:
- <criterion - must be testable>
- <criterion - must be observable>
- <criterion - covers happy path and key validations>

Optional Gherkin (for complex flows):
Given <precondition>
When <action>
Then <observable outcome>
```

**Optional sections (if needed):**

```
Risks / Edge Cases:
- <only if materially affects AC or implementation>

Out of Scope:
- <explicitly state what this story does NOT include>
```

## Quality Bar

- Stories are small yet shippable end-to-end
- No hard cap on count; stop when outcomes are coherent and non-overlapping
- Avoid duplicates; prefer clear naming and consistent structure



## Examples

### Example: Habit Tracker

Input (already refined by command):

```
Habit tracker where users log daily habits and see streaks.
Goals: Personal habit tracking, mobile-first
Constraints: Anonymous usage, localStorage initially
```

**Hamburger Layers & Options:**

**Layer 1 - Store habits:**
- LocalStorage only → LocalStorage + export → Backend sync

**Layer 2 - Track completions:**
- Manual checkmark → Checkmark with timestamp → Rich completion data (time, notes)

**Layer 3 - Display progress:**
- Simple count → 7-day grid → 30-day calendar with streaks

**Layer 4 - Streak calculation:**
- None → Daily streak only → Multi-level streaks (daily, weekly, monthly)

**Vertical Slices (Bites):**

**First Bite (Story 1):**
LocalStorage + Manual checkmark + Simple count + No streak

```
Story 1: Track habit completion
User Story: As a user, I want to create habits and mark them complete so that I can track my progress.
Acceptance Criteria:
- User can add a habit with a name (up to 50 chars)
- User can check/uncheck habits for today
- User sees count of completions this week
- Data persists in localStorage on refresh
```

**Second Bite (Story 2):**
LocalStorage + Manual checkmark + 7-day grid + Daily streak

```
Story 2: View 7-day streak
User Story: As a user, I want to see my last 7 days of completions so that I can track my consistency.
Acceptance Criteria:
- User sees a 7-day grid for each habit
- Current streak count is displayed (consecutive days)
- Streak resets when a day is missed
- Grid updates immediately when toggling completion
```

**Third Bite (Story 3):**
LocalStorage + Timestamp + 7-day grid + Daily streak

```
Story 3: Track completion times
User Story: As a user, I want to see what time I completed habits so that I can identify patterns.
Acceptance Criteria:
- Completion time is recorded automatically
- User can view completion times in the 7-day grid
- Times are formatted in user's local timezone
```
