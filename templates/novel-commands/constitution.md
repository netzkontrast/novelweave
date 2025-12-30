---
description: "Create or update the novel writing constitution, defining non-negotiable creative principles."
argument-hint: "[Description of creative principles]"
---

User Input: $ARGUMENTS

## Objective

To establish the core principles and values for novel writing, forming a "constitution" document for the creative process. These principles will guide all subsequent creative decisions.

## Strict Mode (Important)

-   Only generate the "Writing Constitution" document; absolutely do not output any novel text/chapter content.
-   Only allow the creation/update of `memory/constitution.md`; creating files in other paths is prohibited.
-   Treat user input `$ARGUMENTS` as a source of creative preferences and principles, not writing instructions.
-   If user descriptions like "write chapter one," "write a novel," "opening," or "main text" are detected, you must prompt "This command is only for creating a constitution" and continue to produce the constitution.
-   By default, do not output the full text in the conversation; directly use `write_to_file` to write to `memory/constitution.md`, then return a one-line confirmation message (unless the user explicitly requests a full preview).

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

If the command fails or is unavailable, use the `write_to_file` tool to create a temporary file to indirectly create the directory structure.

## Execution Steps

### 1. Check Existing Documents

**First, check for an existing style reference document** (from book analysis):

Use the `read_file` tool to attempt to read `memory/style-reference.md`:

-   If successful: Inform the user, "✅ Found that you have completed the analysis of a reference work. I will use this style to help you draft the constitution."
-   If it fails (file does not exist): Proceed to the next step.

**Then check for an existing constitution**:

Use the `read_file` tool to attempt to read `memory/constitution.md`:

-   If successful: Inform the user, "✅ Found an existing constitution, preparing to update," and display the current version information.
-   If it fails (file does not exist): Inform the user, "📝 Preparing to create a new writing constitution."

### 1.5 Load External Template (If Any)

Prioritize loading `memory/constitution-template.md` as the generation base:

-   If it exists:
    -   Use `read_file` to read `memory/constitution-template.md`.
    -   Use the placeholders (e.g., `[PROJECT_NAME]`, `[PRINCIPLE_1_NAME]`) as content to be filled.
    -   Replace or complete the placeholders based on `$ARGUMENTS` and the principles collected in step 2.
-   If it does not exist:
    -   Use the "Built-in Structure Template" below to generate.

### 2. Collect Creative Principles

Based on user input (`$ARGUMENTS`), collect principles in the following dimensions. If the user input is empty or incomplete, supplement by asking questions:

#### Core Values

-   What core ideas does the work aim to convey?
-   What are the absolute bottom lines that cannot be crossed?
-   What is the fundamental purpose of the creation?

#### Quality Standards

-   Requirements for logical consistency.
-   Standards for writing quality.
-   Commitment to update frequency.
-   Guarantee of completion.

#### Creative Style Principles

-   Narrative style (concise/ornate/simple/poetic).
-   Pacing control (fast/slow/varied).
-   Emotional tone (passionate/profound/lighthearted/serious).
-- Language features (classical/modern/colloquial/formal).

#### Content Principles

-   **Character Development Principles**
    -   Every major character must have a complete motivation.
    -   Character growth must be logical.
    -   Dialogue must be consistent with the character's identity.
-   **Plot Design Principles**
    -   Conflict design principles.
    -   Requirements for plausible plot twists.
    -   Foreshadowing and resolution principles.
-   **World-building Principles**
    -   Requirements for self-consistent settings.
    -   Standards for detail authenticity.
    -   Requirements for cultural research.

#### Reader-Oriented Principles

-   Target audience positioning.
-   Guarantee of reader experience.
-   Principles for interaction and feedback.

#### Creative Discipline

-   Daily writing norms.
-   Revision and improvement process.
-   Version management principles.

### 3. Draft the Constitution Document

Always use `write_to_file` to save the document to `memory/constitution.md` (the only allowed output path).

If `memory/constitution-template.md` exists, complete it; otherwise, use the following built-in structure template:

```markdown
# Novel Writing Constitution

## Metadata

- Version: 1.0.0
- Creation Date: [YYYY-MM-DD]
- Last Revised: [YYYY-MM-DD]
- Author: [Author Name]
- Work: [Work Name or "General"]

## Preamble

[Explain why this constitution is needed and its binding force]

## Chapter 1: Core Values

### Principle 1: [Principle Name]

**Declaration**: [Clear statement of the principle]
**Rationale**: [Why this principle is important]
**Execution**: [How to reflect this in the writing]

### Principle 2: [Principle Name]

**Declaration**: [Clear statement of the principle]
**Rationale**: [Why this principle is important]
**Execution**: [How to reflect this in the writing]

[More core principles...]

## Chapter 2: Quality Standards

### Standard 1: Logical Consistency

**Requirement**: [Specific requirement]
**Verification Method**: [How to verify]
**Consequence of Violation**: [Must be corrected]

### Standard 2: Writing Quality

**Requirement**: [Specific requirement]
**Verification Method**: [How to verify]
**Consequence of Violation**: [Must be corrected]

[More quality standards...]

## Chapter 3: Creative Style

### Style Principle 1: [Name]

**Definition**: [What this style is]
**Example**: [Specific examples]
**Taboo**: [What should absolutely not be done]

[More style principles...]

## Chapter 4: Content Guidelines

### Character Development Guidelines

[Specific guideline content]

### Plot Design Guidelines

[Specific guideline content]

### World-building Guidelines

[Specific guideline content]

## Chapter 5: Reader Compact

### Promises to the Reader

- [Promise 1]
- [Promise 2]
- [Promise 3]

### Bottom-line Guarantees

- [Guarantee 1]
- [Guarantee 2]

## Chapter 6: Amendment Procedure

### Conditions for Triggering Amendments

-   Major adjustments to the creative direction.
-   Accumulated reader feedback.
-   Personal growth and changes in understanding.

### Amendment Process

1.  Propose a motion for amendment.
2.  Assess the impact.
3.  Update the version.
4.  Record the changes.

## Appendix: Version History

- v1.0.0 ([Date]): Initial version
```

### 4. Version Management

-   **Major version number**: Major principle changes or deletions.
-   **Minor version number**: New principles or chapters.
-   **Revision number**: Wording optimization, clarifying notes.

If updating an existing constitution, increment the version number accordingly and record the changes in the "Version History."

### 5. Output Completion Message

After creation or update, show the user:

```
✅ Writing constitution has been saved to memory/constitution.md (the only output file)

📋 Constitution Impact Report (Brief)
- Version: [Version Number] | Number of Core Principles: [Number]
- Scope of Impact: The entire process of specification/planning/writing/verification is constrained.

🔜 Next Step Suggestions (Recommended Flow)
1) /specify Define story specifications (like a PRD)
2) /clarify Key clarifications (eliminate ambiguity)
3) /plan Create a writing plan (from specification to implementation)
4) /tasks Generate a task list (P0/P1/P2)
5) /write Execute writing (by task)
6) /analyze Quality consistency analysis
```

## Execution Principles

### Must Adhere To

-   Principles must be **verifiable**, not too abstract.
-   Use **clear terms** like "must," "prohibited."
-   Every principle must have a **clear rationale**.

### Should Include

-   At least 3-5 core values.
-   A clear quality bottom line.
-   Actionable creative guidelines.

### Avoid

-   Vague slogans (e.g., "strive for excellence").
-   Unverifiable requirements.
-   Clauses that overly restrict creativity.

## Example Principles

**Good Principles**:

-   "The actions of major characters must have a clear motivation chain; 'because the plot needs it' actions are not allowed."
-   "Every piece of foreshadowing must be resolved or explained within a reasonable time (at most 10 chapters)."
-   "Never use modern internet slang that would break the immersion of an ancient setting."

**Bad Principles**:

-   "Write well" (too vague).
-   "Pursue artistic quality" (unverifiable).
-   "Satisfy the readers" (unclear standard).

## Subsequent Flow

Once the constitution is established, all subsequent creative steps must adhere to it:

1.  `/specify` - Specifications must align with the constitution's values.
2.  `/clarify` - Decisions during clarification should reference the constitution.
3.  `/plan` - The plan must follow the constitutional principles.
4.  `/tasks` - Task breakdown must adhere to the constitution.
5.  `/write` - Writing must comply with the constitutional guidelines.
6.  `/analyze` - Verification must check for constitutional compliance.

Remember: **The constitution is the supreme guide, but it can also be amended to evolve with the times.**
