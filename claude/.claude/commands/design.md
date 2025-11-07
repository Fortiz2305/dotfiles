# Command: /design

Unified design assistant hub supporting review, brainstorming, redesign, and variations modes.

## Parameters

- `<component-path>` (optional): Relative or absolute path to React component file
- `--component <name>` (optional): Search for component by name
- `--screenshot` (optional): Visual review from screenshot
- `--mode <mode>` (optional): Direct mode selection: `review` | `brainstorm` | `redesign` | `variations`

## Instructions

### 1. Parse and Validate Input

**Extract parameters:**
- Component path/name (if provided)
- Mode flag (if provided)
- Screenshot flag (if provided)

**If component path provided:**
- Validate file exists in `apps/spa/src/`
- Verify it's a React component (`.tsx` or `.jsx` extension)
- Extract component name from filename
- Set `component_exists = true`

**If `--component <name>` flag used:**
- Search for matching component files:
  ```bash
  find apps/spa/src -name "*<name>*.tsx" -o -name "*<name>*.jsx"
  ```
- If multiple matches, ask user to select
- If no matches, inform user and exit
- Set `component_exists = true`

**If `--screenshot` flag used:**
- Set `mode = "review"` and `use_screenshot = true`
- Skip component file validation

**If no parameters:**
- Set `component_exists = false`
- Set `mode = "brainstorm"` (only valid option)

### 2. Determine Design Mode

**If mode explicitly specified via `--mode` flag:**
- Use the specified mode
- Validate mode is compatible with component existence:
  - `review`, `redesign`, `variations` require component to exist
  - `brainstorm` works with or without component
- If invalid combination, inform user and exit

**If mode not specified:**
- **Component exists:** Ask user to select mode using AskUserQuestion:
  ```
  Question: "What would you like to do with this component?"
  Options:
    - "Review for issues" → mode = "review"
    - "Propose redesign alternatives" → mode = "redesign"
    - "Generate improvement variations" → mode = "variations"
  ```
- **Component doesn't exist:** Auto-set `mode = "brainstorm"`

### 3. Prepare Project Context

Gather standardized context to pass to all agents:

```yaml
component_path: <absolute-path-to-component> (if applicable)
component_name: <extracted-name> (if applicable)
mode: <selected-mode>
project_context:
  application: "Audiense Action - Marketing campaign design tool"
  target_users: "Marketing professionals (non-technical)"
  tech_stack:
    ui_library: "Titan 5.30.1 (Audiense internal design system)"
    components: "PrimeReact 10.9.6"
    framework: "React 19 with TanStack Router"
    testing: "Testing Library + Vitest"
    architecture: "SPA with file-based routing"
  constraints:
    - "Desktop and tablet support required"
    - "WCAG 2.1 AA accessibility compliance"
    - "Performance critical for large marketing datasets"
    - "Must follow existing component patterns"
```

### 4. Launch Appropriate Agent

Based on selected mode, use the Task tool to launch the corresponding agent:

#### Mode: Review

```
Task(
  subagent_type="general-purpose",
  description="Analyze React component design quality",
  prompt="""
You are a senior UX/UI designer and accessibility expert analyzing a React component.

**IMPORTANT: Read the agent instructions first:**
Before starting your analysis, read `.claude/agents/design-reviewer.md` which contains the complete 7-layer analysis framework, output format, and detailed guidelines.

**Component to Review:**
- Name: {component_name}
- Path: {component_path}

**Project Context:**
{project_context_yaml}

**Your Task:**
1. First, read `.claude/agents/design-reviewer.md` for complete instructions
2. Follow the layer analysis framework defined there
3. Use the autonomous discovery process outlined in the agent doc
4. Return findings in the exact structured format specified

You have full access to Read, Grep, Glob tools - use them autonomously to discover related files and patterns.
"""
)
```

#### Mode: Brainstorm

```
Task(
  subagent_type="general-purpose",
  description="Generate new component design proposals",
  prompt="""
You are a senior UX/UI designer creating a new React component from scratch.

**IMPORTANT: Read the agent instructions first:**
Before starting, read `.claude/agents/design-brainstormer.md` which contains the complete design generation framework, questioning strategy, and output format.

**User Requirements:**
{user_will_describe_requirements}

**Project Context:**
{project_context_yaml}

**Your Task:**
1. First, read `.claude/agents/design-brainstormer.md` for complete instructions
2. Follow the requirements gathering process
3. Generate 3 design alternatives (simple → medium → ambitious)
4. Return proposals in the exact structured format specified

You have full access to Read, Grep, Glob tools - use them autonomously to discover similar patterns in the codebase.
"""
)
```

**Important:** For brainstorm mode, first ask the user to describe what they want to design, then launch the agent with their requirements.

#### Mode: Redesign

```
Task(
  subagent_type="general-purpose",
  description="Propose alternative component architectures",
  prompt="""
You are a senior software architect proposing alternative designs for an existing React component.

**IMPORTANT: Read the agent instructions first:**
Before starting, read `.claude/agents/design-redesigner.md` which contains the redesign framework, analysis approach, and output format.

**Component to Redesign:**
- Name: {component_name}
- Path: {component_path}

**Project Context:**
{project_context_yaml}

**Your Task:**
1. First, read `.claude/agents/design-redesigner.md` for complete instructions
2. Analyze the current design approach (not critically, but understanding philosophy)
3. Propose 2-3 alternative architectural approaches
4. Return redesign concepts in the exact structured format specified

You have full access to Read, Grep, Glob tools - use them autonomously to understand the current implementation and find similar patterns.
"""
)
```

#### Mode: Variations

```
Task(
  subagent_type="general-purpose",
  description="Generate evolutionary improvement path",
  prompt="""
You are a senior UX/UI designer creating an evolutionary improvement roadmap for an existing component.

**IMPORTANT: Read the agent instructions first:**
Before starting, read `.claude/agents/design-variations.md` which contains the variations framework, version spectrum, and output format.

**Component to Improve:**
- Name: {component_name}
- Path: {component_path}

**Project Context:**
{project_context_yaml}

**Your Task:**
1. First, read `.claude/agents/design-variations.md` for complete instructions
2. Analyze the current component state
3. Generate 3 versions: Quick wins (2-4h) → Moderate (1-2d) → Ambitious (3-5d)
4. Return evolution path in the exact structured format specified

You have full access to Read, Grep, Glob tools - use them autonomously to understand the current implementation.
"""
)
```

### 5. Format and Present Results

Once the agent completes its work, format the output based on mode:

**For all modes:**
```markdown
# {emoji} Design {Mode}: {ComponentName}

**Component:** `{relative_path}` (if applicable)
**Mode:** {mode}
**Analysis Date:** {current_date}

---

{agent_output}

---

💡 **Next Steps:**
{mode_specific_suggestions}

📚 **Resources:**
- Titan UI Library: https://titan.audiense.com
- WCAG Guidelines: https://www.w3.org/WAI/WCAG21/quickref/
- Laws of UX: https://lawsofux.com
- Shape of AI: https://www.shapeof.ai
```

**Mode-specific emojis:**
- Review: 🔍
- Brainstorm: 🎨
- Redesign: 🔄
- Variations: 📈

**Mode-specific next steps:**
- **Review:** "Review priority 1 items immediately. Create TodoWrite tasks for tracking."
- **Brainstorm:** "Select an option to refine or implement. Run: `npm run test` to verify."
- **Redesign:** "Choose alternative to explore. Consider migration effort and benefits."
- **Variations:** "Start with quick wins (V1) to validate. Iterate based on user feedback."

### 6. Offer Follow-up Actions

After presenting results, ask:

```
Would you like me to:
1. {mode_specific_action_1}
2. Run the design command again with a different mode?
3. Create TodoWrite tasks for implementing the recommendations?
```

**Mode-specific action 1:**
- **Review:** "Fix the priority 1 issues identified?"
- **Brainstorm:** "Create the component based on one of the proposals?"
- **Redesign:** "Create a detailed implementation plan for one alternative?"
- **Variations:** "Implement the quick wins (V1)?"

## Example Usage

```bash
# Interactive mode selection (component exists)
/design apps/spa/src/views/Projects/ProjectCard.tsx

# Search for component by name, then select mode
/design --component SegmentChat

# Direct mode specification
/design --mode review src/components/BrandsInput/BrandsInput.tsx
/design --mode brainstorm
/design --mode redesign apps/spa/src/views/ProjectResults/SegmentCard.tsx
/design --mode variations --component AudienceInsightsTab

# Visual review from screenshot
/design --screenshot
```

## Error Handling

- **File not found:** "Component not found at `{path}`. Use `--component <name>` to search or `--mode brainstorm` to design from scratch."
- **Not a React component:** "File must be a React component (.tsx or .jsx). Provided: `{extension}`"
- **Invalid mode combination:** "Mode `{mode}` requires an existing component. Use `--mode brainstorm` to design from scratch."
- **Agent fails:** "Agent encountered an error: {error_message}. Please try again or use a different mode."
- **Multiple component matches:** Present list with numbered options and ask user to select

## Notes

- The command delegates all analysis/generation logic to specialized agents
- Each agent has full access to Read, Grep, Glob tools for autonomous discovery
- Always provide file:line references in recommendations
- Focus on actionable, specific recommendations with code examples
- Ground all recommendations in Laws of UX and Shape of AI patterns

## Agent File References

- **Review Mode:** `.claude/agents/design-reviewer.md`
- **Brainstorm Mode:** `.claude/agents/design-brainstormer.md`
- **Redesign Mode:** `.claude/agents/design-redesigner.md`
- **Variations Mode:** `.claude/agents/design-variations.md`
