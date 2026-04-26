---
name: write-skill
description: Generate a new skill definition file with proper structure and best practices. Use when the user says "create a skill for [task]", "write a new skill", "make a skill that does [x]", or any variation asking to define a new AI skill.
---

# Skill Writer

Create a well-structured skill definition that an AI agent can follow to perform a specific task reliably and consistently.

## Goal

Produce a SKILL.md file that clearly defines:
1. When to invoke the skill (trigger patterns)
2. What the skill does (goal/outcome)
3. How to execute it (step-by-step instructions)
4. Edge cases and gotchas

## Step 1: Understand the Task

Ask clarifying questions if needed:
- What is the core task this skill should accomplish?
- What inputs does it need from the user?
- What should the output look like?
- Are there any tools or commands it should use?
- What are common edge cases or failure modes?

## Step 2: Choose a Skill Name

Pick a short, descriptive name using kebab-case:
- Use 1-3 words max
- Focus on the action or output (e.g., `daily-standup`, `write-skill`, `deploy-service`)
- Avoid generic names like `helper` or `utility`

## Step 3: Write the Frontmatter

Start with YAML frontmatter that defines:

```yaml
---
name: skill-name
description: [One sentence describing what the skill does and when to trigger it. Include example trigger phrases like 'Use when the user says "do X", "create Y", or any variation asking for Z.']
---
```

**Description tips**:
- Start with what the skill produces or accomplishes
- Include 3-5 natural trigger phrases users might say
- Be specific about the context (e.g., "based on calendar", "from a transcript file")
- Keep it under 3 sentences

## Step 4: Write the Skill Body

Structure the skill in clear sections:

### Title
Start with an H1 that's slightly more descriptive than the name.

### Goal (optional but recommended)
A 1-3 sentence summary of what the skill achieves and why.

### Steps
Number each major step clearly. Use descriptive headers like:
- `## Step 1: Fetch Data`
- `## Step 2: Process Results`
- `## Step 3: Generate Output`

**For each step**:
- State the objective clearly
- Show exact commands or tool calls if applicable
- Explain what to do with the results
- Use code blocks for commands, examples, or output formats

### Output/Formatting Section
Define exactly what the final output should look like:
- Structure and format
- Where to write it (file, console, artifact)
- Rendering or post-processing steps

### Gotchas Section
List common edge cases, failure modes, or special handling:
- Start each with `**[Issue]**:`
- Be specific about what to skip, ignore, or handle differently
- Include examples where helpful

## Step 5: Use Clear, Direct Language

Write instructions like you're guiding a junior engineer:
- **Use imperatives**: "Fetch the data", "Parse the results", "Skip declined events"
- **Be explicit**: Don't say "handle appropriately" — say exactly what to do
- **Show examples**: When output format matters, include a concrete example
- **One action per bullet**: Break complex operations into sub-bullets

## Step 6: Test the Mental Model

Read through the skill as if you're an AI agent with no prior context:
- Can you execute each step without ambiguity?
- Are all inputs clearly defined?
- Is the output format unambiguous?
- Are edge cases covered?

## Output

Create a new folder: `skills/[skill-name]/`

Write the skill to: `skills/[skill-name]/SKILL.md`

After creating the file, show a summary:

```
Created new skill: [skill-name]

Location: skills/[skill-name]/SKILL.md
Triggers: [list key trigger phrases]
Output: [describe what it produces]

To activate: Add the skills folder to your AI assistant's configuration and say "[example trigger phrase]"
```

## Best Practices

**DO**:
- Use numbered steps for sequential operations
- Include example commands, output formats, and edge cases
- Write in second person ("you check", "parse the results")
- Be opinionated about format and structure
- Front-load the most common use case

**DON'T**:
- Leave format ambiguous ("format appropriately", "do something reasonable")
- Assume the agent knows context outside the skill
- Make steps dependent on unspecified configuration
- Write vague instructions like "handle errors gracefully"
- Overcomplicate simple tasks

## Gotchas

- **Trigger phrases** in the description should be natural language, not technical jargon. Think about how a user would actually ask for this.
- **Command examples** should be concrete and runnable. Use placeholder syntax like `$VARIABLE` for dynamic values.
- **Output format** is critical — if the skill produces structured output, show exactly what it should look like with a real example.
- **Edge cases** should focus on the top 3-5 common failure modes, not every possible scenario.
- **File paths** — if the skill creates files, specify the path pattern clearly (e.g., `artifacts/1on1-[date].md`).
