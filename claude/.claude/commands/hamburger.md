---

name: hamburger

description: Convert an idea into small, sensible user stories with acceptance criteria using the Hamburger method. Accepts inline ideas, a .md/.txt file path, or a Notion page link. Can write output back to Notion via MCP.

---

# /hamburger — Hamburger User Stories Command

## Purpose

Turn an idea (text, .md file or a Notion link) into vertically sliced, INVEST-compliant user stories with acceptance criteria, applying the Hamburger method. Output in English. No hard cap on the number of stories; generate as many as make sense while keeping them small and shippable.

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
- If idea is very broad or vague → ask exactly one multiple-choice clarifying question, otherwise proceed with sensible defaults and document assumptions
- If Notion URL is provided but MCP access fails → report error and ask for a corrected link or fallback to inline/file input

## Method: Hamburger

Six-step splitting process (per Hamburger method):

- Step 1 – Identify tasks: List the technical/component steps for the story (layers)
- Step 2 – Identify options: Define quality levels/options for each task
- Step 3 – Combine results: Build a single hamburger and align options left→right by quality
- Step 4 – Trim the hamburger: Remove dominated/low-value options; set max needed quality per task
- Step 5 – First bite: Choose the minimum acceptable quality per task to form the first vertical slice
- Step 6 – Further bites: Increase quality in subsequent slices while maintaining vertical value

Presentation layers for the output:

- Top Bun: Brief problem/context summary (≤2 sentences)
- Patty: Small, INVEST user stories (Actor + Action + Outcome)
- Cheese: Acceptance Criteria per story (bullet list)
- Lettuce: Key risks/edge cases that affect AC (optional)
- Bottom Bun: Explicit out-of-scope items to limit scope (optional)

## Vertical Slicing Rules

- Each story should cut through UI → Logic → Data to deliver observable value
- Prefer multiple small stories over one broad one; split by user outcome, not components
- Ensure each story is independently shippable and testable via its acceptance criteria

## Execution

1. Parse input
   - If input is a Notion URL: fetch page content via Notion MCP and extract title, goals, constraints, and key bullets
   - Else if path ends with .md or .txt: read and extract title (H1 or filename), goals, constraints, features or bullets
   - Else: treat the remainder of the command as the idea text
2. Normalize scope
   - If multiple distinct capabilities are evident, group the stories under feature subheadings
   - If vague, ask one concise multiple-choice question; if unanswered, proceed and note assumptions
3. Delegate Hamburger analysis to the helper analyzer
   - Load: `~/.claude/agents/user-stories-hamburger.md`
   - Provide: the normalized idea text plus any extracted file context
4. Generate output in the format below
5. Output destination
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

- Empty/invalid input → Ask for a brief idea with one guiding multiple-choice question
- File-not-found → Report path issue; request corrected path or inline text
- Extremely broad project → Provide 1–2 stories per major capability and recommend running project slicing later
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

## Examples

Inline:

```
/hamburger Build a habit tracker app where users log daily habits and see streaks.
```

File:

```
/hamburger ./docs/product-idea.md
```

Notion as input (reads page content):

```
/hamburger https://www.notion.so/your-workspace/your-page-id
```

Notion as output (after generation, writes to target page):

```
User: Please write the output to this Notion page → https://www.notion.so/your-workspace/target-page-id
Assistant: Writes the generated stories to the provided Notion page via MCP.
```
