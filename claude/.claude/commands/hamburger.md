---

name: hamburger

description: Collaborative idea-to-stories workflow. Brainstorms rough ideas through questions, then applies the Hamburger method to generate vertically sliced user stories with acceptance criteria. Accepts inline ideas, a .md/.txt file path, or a Notion page link. Can write output back to Notion via MCP.

---

# /hamburger — Hamburger User Stories Command

## Purpose

Transform ideas into vertically sliced, INVEST-compliant user stories with acceptance criteria. For rough ideas, brainstorms collaboratively first to refine understanding. For detailed inputs (files/Notion), proceeds directly to the Hamburger method. Output in English. No hard cap on the number of stories; generate as many as make sense while keeping them small and shippable.

## Usage

Inline text:

```
/hamburger A habit tracker where users log daily habits and see streaks.
```

File path (.md or .txt):

```
/hamburger ./docs/product-idea.md
```

Structured input:

```
/hamburger
Name: Habit Tracker
Goal: Users log habits daily and track streaks
Constraints: Mobile-first, anonymous usage allowed initially
```

## Accepted Inputs

- Inline idea/goal/problem statement
- File path ending in .md or .txt (PRD/spec/idea). If a file path is provided, read and extract title, goals, constraints, and key bullets
- Notion page URL (e.g., https://www.notion.so/... or notion://...). If provided, read page content via Notion MCP

## Validation

- Required: Non-empty input or a readable file path
- If file path is provided and not found → report and request corrected path or inline text
- If Notion URL is provided but MCP access fails → report error and ask for a corrected link or fallback to inline/file input

## Brainstorming Phase (Conditional)

Triggered when input is rough or lacks detail. Skip for well-structured files or detailed Notion pages.

**Understanding the idea:**
- Check current project context first (files, docs, recent commits if applicable)
- Ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Focus on understanding: purpose, constraints, success criteria, and user outcomes
- YAGNI ruthlessly - remove unnecessary features from initial scope

**Exploring approaches:**
- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with recommended option and explain why

**Validating understanding:**
- Once you believe you understand what you're building, present the refined understanding
- Break it into sections of 200-300 words
- Ask after each section whether it looks right so far
- Be ready to go back and clarify if something doesn't make sense

## Method: Hamburger

Six-step splitting process based on [Gojko Adzic's Hamburger method](https://gojko.net/2012/01/23/splitting-user-stories-the-hamburger-method/):

**The Metaphor:**
- **Layers** (meat, lettuce, cheese, bacon) = Technical/component workflow steps
- **Options per layer** = Different quality levels for each step (slow→fast, manual→automated, basic→advanced)
- **Vertical slices (bites)** = Choosing one quality level per layer to deliver end-to-end value
- **Key insight**: Don't ship just one layer (like eating only lettuce); always deliver a vertical slice

**The Process:**

- **Step 1 – Identify tasks**: List the technical/component steps for the story (these become hamburger layers). Example: "Query DB → Send email → Process responses → Update records"

- **Step 2 – Identify options**: For each task/layer, define quality levels from minimum to maximum. Example for "Send email": "Manual sending" → "Automated generic" → "Automated personalized" → "Automated personalized + auto-unsubscribe"

- **Step 3 – Combine results**: Build a single visual hamburger, align options left→right by quality level across all layers

- **Step 4 – Trim the hamburger**: Remove dominated options (same effort, lower quality) and options beyond maximum needed quality

- **Step 5 – First bite**: Choose the minimum acceptable quality per layer to form the first vertical slice. This becomes Story 1.

- **Step 6 – Further bites**: Increase quality in one or more layers while maintaining vertical value. Each bite = one user story.

**Concrete Example** (from Gojko's article):
Feature: Contact dormant customers by email

Layers and options:
- **Query DB**: Slow inaccurate query → Overnight accurate query → Real-time with activity tracking
- **Send email**: Manual → Automated generic → Personalized → Personalized + auto-unsubscribe
- **Process bounces**: Do nothing → Manual removal → Overnight automated → Real-time automated

First bite (Story 1): Slow query + Automated generic + Do nothing
Second bite (Story 2): Slow query + Personalized email + Do nothing
Third bite (Story 3): Overnight query + Personalized email + Overnight bounce removal

## Vertical Slicing Rules

- Each story should cut through UI → Logic → Data to deliver observable value
- Prefer multiple small stories over one broad one; split by user outcome, not components
- Ensure each story is independently shippable and testable via its acceptance criteria

## Execution

1. Parse input
   - If input is a Notion URL: fetch page content via Notion MCP and extract title, goals, constraints, and key bullets
   - Else if path ends with .md or .txt: read and extract title (H1 or filename), goals, constraints, features or bullets
   - Else: treat the remainder of the command as the idea text

2. Assess input detail level (smart conditional brainstorming)
   - Detailed input: File/Notion with clear goals, constraints, and features → Skip to step 4
   - Rough input: Inline text or vague content → Proceed to step 3

3. Brainstorming phase (only for rough ideas)
   - Check current project context (files, docs, recent commits)
   - Ask clarifying questions one at a time (prefer multiple choice when possible)
   - Focus on: purpose, constraints, success criteria, and user outcomes
   - Explore 2-3 different approaches with trade-offs (lead with recommendation)
   - Present understanding in small sections (200-300 words), validate after each
   - Continue until the idea is well-understood and validated

4. Delegate Hamburger analysis to the helper analyzer
   - Load: `~/.claude/agents/user-stories-hamburger.md`
   - Provide: the validated/refined idea text plus any extracted file context
   - Apply 6-step Hamburger splitting process

5. Generate output in the format below

6. Output destination
   - If the user provided a Notion destination link (page or database), write the output to Notion via MCP
   - If not provided, ask for a Notion link to write the results; if declined, return the markdown in chat

## Output Format (Markdown)

Title:

```
## User Stories for <Name>
```

Optional context (≤2 sentences):

```
Brief Context: <one or two sentences>
```

Stories (repeat for each):

```
### Story N: <concise outcome-based name>
User Story: As a <actor>, I want <action> so that <outcome>.
Acceptance Criteria:
- <bullet>
- <bullet>
- <bullet>

Optional Gherkin:
Given <precondition>
When <action>
Then <observable outcome>
```

Optional sections:

```
Risks / Edge Cases:
- <only if materially affects AC>

Out of Scope:
- <only if needed to constrain>
```

## Edge Cases

- Empty/invalid input → Enter brainstorming mode to understand what they want to build
- File-not-found → Report path issue; request corrected path or inline text
- Vague inline idea → Trigger brainstorming phase to refine through collaborative questions
- Detailed file/Notion input → Skip brainstorming, proceed directly to Hamburger method
- Extremely broad project → Use brainstorming to narrow scope, then provide 1–2 stories per major capability
- Notion read/write failures → Show the error, suggest verifying MCP configuration/permissions, and offer to continue in chat or with a file output

## Notion Integration

- Reading input from Notion:
  - Detect Notion URL
  - Use Notion MCP to fetch the page content
  - Extract title, goals, constraints, and key bullets from the page
- Writing output to Notion:
  - If user provides a Notion page or database link, create or update a page with the generated user stories and acceptance criteria
  - If no link is provided, ask once for a target Notion link; if none is provided, return output in chat
  - Preserve markdown formatting compatible with Notion blocks

## Guarantees

- Output in English, markdown formatted
- No arbitrary cap on story count; keep them small and coherent
- Each story has acceptance criteria that make it testable

## Workflow Summary

```
Rough inline idea
  ↓
Parse input
  ↓
Assess detail level → Rough/vague
  ↓
Brainstorming Phase
  - Ask questions (one at a time)
  - Explore approaches (2-3 options)
  - Validate understanding (sections)
  ↓
Apply Hamburger Method
  - 6-step splitting
  - Generate user stories
  - Create acceptance criteria
  ↓
Output (Notion/markdown)
```

```
Detailed file/Notion
  ↓
Parse input
  ↓
Assess detail level → Detailed/structured
  ↓
Apply Hamburger Method (skip brainstorming)
  - 6-step splitting
  - Generate user stories
  - Create acceptance criteria
  ↓
Output (Notion/markdown)
```

## Examples

### Example 1: Rough Inline Idea (Triggers Brainstorming)

```
/hamburger Build a habit tracker app where users log daily habits and see streaks.
```

Flow: Asks clarifying questions → Explores approaches → Validates understanding → Generates stories

### Example 2: Detailed File (Skips Brainstorming)

```
/hamburger ./docs/product-idea.md
```

Flow: Reads structured file → Directly applies Hamburger method → Generates stories

### Example 3: Notion as Input

```
/hamburger https://www.notion.so/your-workspace/your-page-id
```

Flow: Fetches Notion page → Assesses detail level → Brainstorm or skip → Generates stories

### Example 4: Notion as Output

```
User: Please write the output to this Notion page → https://www.notion.so/your-workspace/target-page-id
Assistant: Writes the generated stories to the provided Notion page via MCP.
```
