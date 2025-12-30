---
description: "Clarify ambiguous points in the story outline through targeted Q&A to ensure a clear creative direction."
argument-hint: "[Keywords or focus areas]"
---

⚠️ **Execution Reminder**: The following are task instructions for you to execute, not content to be displayed to the user. You need to:

1.  Read the `[Clarification Needed]` markers in `specification.md`.
2.  Engage in an interactive clarification dialogue with the user (one question at a time).
3.  Record the clarification results in the specification document.
4.  Only conduct the clarification dialogue in the chat; do not output the entire process.

Focus Area (optional): $ARGUMENTS

## Objective

To detect and reduce ambiguities or missing decision points in the story outline, gather clarifying information through interactive Q&A, and record the results in the story file.

**Note**: This clarification process should be run and completed before `/plan`. If the user explicitly skips clarification (e.g., for exploratory writing), you may proceed, but you must warn that the risk of downstream rework will increase.

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Execution Steps

### 1. Find and Load the Specification File

**Find the specification file**:

Use the `list_files` tool or `execute_command` to list all specification files in the `stories/` directory:

```bash
find stories -name "specification.md" -type f
```

**Handle the result**:

-   If multiple specification files are found, ask the user to choose which story to clarify.
-   If one specification file is found, use `read_file` to read it.
-   If none are found, prompt the user to run `/specify` first to create a story specification.

**Load the specification content**:

-   Use `read_file` to read the specification file.
-   Identify all `[Clarification Needed]` markers.
-   Analyze the completeness of the specification.

### 2. Structured Ambiguity Scan

Conduct a comprehensive scan of the story outline, assessing the clarity of each category (Clear/Partially Clear/Missing):

**Creative Positioning**

-   Target audience (age range, gender preference, reading level)
-   Work's positioning (commercial爽文/serious literature/genre fiction)
-   Expected length (short story 30-50k/novella 100-200k/long-form novel 500k+)

**World-building**

-   Precision of the time period (specific year/dynasty/degree of fantasy)
-   World rules (magic system/technology level/social structure)
-   Geographical scope (single city/multiple countries/continent/interstellar)

**Character Design**

-   Protagonist's growth curve (underdog comeback/genius/steady progress)
-   Protagonist's personality tone (passionate/calm/scheming/saintly)
-   Supporting characters' functional roles (plot-driving/emotional support/contrast)
-   Antagonist's intelligence level (dumbed-down villain/evenly matched/superior intellect)

**Narrative Strategy**

-   Point of view (first-person/third-person limited/omniscient)
-   Timeline structure (linear narrative/flashbacks/multiple parallel lines)
-   Narrative pacing (fast-paced爽文/slow-burn setup/varied)

**Plot Core**

-   Type of core conflict (man vs. man/man vs. nature/man vs. society/man vs. self)
-   Clarity of the main goal (revenge/growth/rescue/exploration)
-   Ending tendency (happy ending/tragedy/open-ended)

**Style and Tone**

-   Choice of writing style (vernacular and smooth/classic and elegant/humorous/gritty and realistic)
-   Descriptive focus (action scenes/psychological description/atmosphere/dialogue-driven)
-   Emotional tone (passionate and exciting/depressing and dark/warm and healing/heart-wrenching)

**Creative Constraints**

-   Handling of sensitive content (level of violence/emotional intimacy)
-   Value orientation (positive energy/realism/critical)
-   Update schedule (daily/weekly/monthly)

Generate candidate questions for each "Partially Clear" or "Missing" category, unless:

-   Clarification will not substantially affect the creative direction.
-   The information is more appropriately determined during the chapter planning stage.

### 3. Generate a Prioritized Question Queue

Internally generate a maximum of 5 prioritized clarification questions, applying the following constraints:

-   A maximum of 5 questions for the entire session.
-   Each question must be answerable in one of the following ways:
    -   Multiple choice (2-5 mutually exclusive options).
    -   Short answer (limited to 5 words or less).
-   Only include questions that have a substantial impact on the creative direction.
-   Ensure balanced category coverage, prioritizing high-impact areas.
-   If more than 5 categories need clarification, select the 5 with the highest (impact × uncertainty).

### 3.5 Question Design Principles (Conversational Understanding)

Each question should sound **like a real writer having a conversation**, not an engineer filling out a form.

**Core Principles**:

-   ✅ **State the observation, then ask the question**: Let the author understand "why you're asking this."
-   ✅ **Use plain language**: Use the language of creators, not academic jargon.
-   ✅ **Point out the impact**: Help the author understand what this decision will affect.
-   ❌ **Avoid asking questions directly**: Don't start with "What is your target audience?"

**Comparison Example**:

❌ **Engineering-style Questioning** (Avoid):

```
Question 1: What is the age range of your target audience?
A. 18-25  B. 26-35  C. 36-45
```

✅ **Conversational Questioning** (Recommended):

```
💬 I noticed your story has campus elements, but also workplace content. The reader groups for these two settings are very different—
Campus readers enjoy passion and growth, while workplace readers are more interested in power dynamics and strategy. This will directly affect our pacing design and value expression.

So I'd like to confirm: **Who are you primarily writing for?**

| Option | Description |
|---|---|
| A | Students (18-25 years old) - focusing on growth and idealism |
| B | Professionals (26-35 years old) - focusing on realism and strategic thinking |
| C | Both (adjust to a dual-narrative, catering to both) |
| D | Custom (please enter your idea) |
```

**Question Structure Template**:

```markdown
💬 [Observed phenomenon/contradiction]. [How this affects a creative decision].

So I'd like to confirm: **[Core Question]**

[Options table or short answer prompt]
```

### 4. Sequential Q&A Loop

**Present one question at a time**, following the conversational format.

Multiple-choice format (must include question context):

```markdown
💬 [Question context: What did you observe? What will this affect?]

So I'd like to confirm: **[Core Question]**

| Option | Description |
| --- | --- |
| A | Detailed description of option A |
| B | Detailed description of option B |
| C | Detailed description of option C |
| D | Detailed description of option D |
| E | Detailed description of option E (optional) |
| F | Custom (please enter your idea) |
```

Short-answer format (must include question context):

```markdown
💬 [Question context: What did you observe? What will this affect?]

So I'd like to confirm: **[Core Question]**

Please provide a brief answer (5 words or less): **\_\_\_**
```

**Processing User's Answer**

-   Validate the answer.
-   If F (Custom) is chosen, accept and record the user's custom input.
-   If there is ambiguity, request a quick clarification.
-   Record the answer and proceed to the next question.

**Stopping Conditions**

-   All key ambiguities have been resolved.
-   The user indicates completion ("Okay," "That's enough," "No more").
-   The 5-question limit has been reached.

### 5. Integrate Clarification Results

Immediately after each accepted answer:

**On First Integration**

-   Create a `## Clarification Records` section in the story outline (if it doesn't exist).
-   Add a `### Clarification Session [Date]` subsection.

**Recording Format**

```markdown
- Q: [Question content] → A: [User's answer]
```

**Update Relevant Sections**
Update the corresponding parts of the story outline based on the clarifications:

-   Creative Positioning → Update the story overview.
-   World-building → Update the world-building section.
-   Characters → Update the character profiles.
-   Narrative Strategy → Add to the creative notes.
-   Style and Tone → Add to the style guide.

### 6. Validate and Save

After each update, validate:

-   Completeness of clarification records.
-   No lingering ambiguous markers have been resolved by the new answers.
-   No contradictory statements.
-   Correct Markdown formatting.

Write the updated content back to the story file.

### 7. Completion Report

The report includes:

-   The number of questions asked and answered.
-   The path to the updated story file.
-   A list of the chapters that were touched.
-   A coverage summary table:

| Category | Status |
| --- | --- |
| Creative Positioning | ✅ Clarified |
| World-building | ✅ Clarified |
| Character Design | ⏸️ Deferred to planning |
| ... | ... |

-   The suggested next command (usually `/plan`).

## Behavioral Rules

-   If no significant ambiguities are found, respond with: "No critical ambiguities requiring immediate clarification were detected."
-   If the story file is missing, guide the user to run `/story` first.
-   Do not exceed the total limit of 5 questions.
-   Avoid asking about purely technical writing details.
-   Respect the user's signal to terminate early.
-   If full coverage is achieved without asking any questions, output a concise coverage summary.

## Novel Writing-Specific Considerations

-   **Genre Adaptation**: Load corresponding knowledge bases based on the story's genre (爽文/mystery/romance/serious literature, etc.) to provide targeted questions.

## Completion Prompt + Next Steps

Output in chat:

```
✅ Clarification complete; "Clarification Records" have been recorded in stories/*/specification.md and relevant sections have been updated.
```

Recommendations:

-   If `[Clarification Needed]` markers still exist → Continue with `/clarify`.
-   If clarification is sufficient → Run `/plan` to create a writing plan.
-   **Reader-oriented**: Different standards and focuses for commercial vs. literary works.
-   **Cultural Sensitivity**: Certain themes require particularly careful handling.
-   **Series Planning**: Whether it is a series will affect the overall architecture and decisions.

Priority Context: {ARGS}
