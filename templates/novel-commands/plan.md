---
description: "Develop a technical implementation plan based on story specifications."
argument-hint: "[Technical preferences and choices]"
---

⚠️ **Execution Reminder**: The following are task instructions for you to execute, not content to be displayed to the user. You need to:

1.  Read the specification document and the constitution.
2.  Develop the actual creative plan.
3.  Generate and save `creative-plan.md`.
4.  Only output a concise completion prompt in the chat.

User Input: $ARGUMENTS

## Objective

To transform "what to create" (specifications) into "how to create it" (plan). This is the key transition from requirements to implementation.

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Execution Steps

### 1. Load Prerequisite Documents

**Find and load necessary files**:

1.  Use `read_file` to attempt to read `memory/constitution.md` (the constitution file).
2.  Use `execute_command` or `list_files` to find the specification file:
    ```bash
    find stories -name "specification.md" -type f
    ```
3.  Use `read_file` to read the found specification file.
4.  Check for clarification records in the specification file.

**🆕 Conditional Loading: The Golden Opening Rules**:

**Condition Check**:

1.  Check the "Target Word Count" or "Total Chapters" in `specification.md`.
2.  Check if the current planning stage is the opening.
3.  Basis for judgment:
    -   If the total word count is < 10,000 words, or
    -   If the planned chapter range includes chapters 1-3.

**If the opening condition is met, perform the following**:

```bash
# Check if the golden opening rules file exists
test -f spec/presets/golden-opening.md && echo "found" || echo "not-found"
```

-   ✅ **If it exists**: Read `spec/presets/golden-opening.md`.
    -   Automatically apply the five golden rules when planning chapters 1-3.
    -   Specifically note the planning for the first three chapters in the "Chapter Architecture Design" section below.

-   ⚠️ **If it does not exist**: Continue with normal planning (does not affect the process).

**🆕 Conditional Loading: Pacing Configuration**:

If the user has used the `/book-internalize` command to analyze a reference work:

```bash
# Check if the pacing configuration file exists
test -f spec/presets/rhythm-config.json && echo "found" || echo "not-found"
```

-   ✅ **If it exists**: Read `spec/presets/rhythm-config.json`.
    -   Apply the pacing model of the reference work (chapter word count, high-point interval, etc.).
    -   Apply content proportion suggestions (dialogue/action/description/psychology).
    -   Reference this data in "2.2 Chapter Architecture Design."

-   ⚠️ **If it does not exist**: Use default pacing planning.

**Verify Specification Clarification Status**:

-   If there are unclarified key decisions, prompt to run `/clarify` first.
-   Or accept the user's explicit instruction to skip.

### 2. Develop the Creative Plan

Create `stories/*/creative-plan.md`, including the following content:

#### 2.1 Writing Method Selection

**Step 1: Understand Available Writing Methods**

Use the `read_file` tool to read the writing methods guide:

-   Extension path: `templates/novel-knowledge/writing-methods-guide.md`
-   Or user project: `spec/knowledge/writing-methods-guide.md`

**Step 2: Select a Method Based on Story Type**

Recommend the most suitable writing method based on the specification analysis:

| Method | Applicable Types | Preset Document Path |
| --- | --- | --- |
| **Three-Act Structure** | Linear narrative, clear setup-confrontation-resolution | `templates/novel-presets/three-act/` |
| **Hero's Journey** | Growth-oriented, adventure stories | `templates/novel-presets/hero-journey/` |
| **Seven-Point Structure**| Suspense, twist-heavy stories | `templates/novel-presets/seven-point/` |
| **Story Circle** | Character-driven, psychological depth | `templates/novel-presets/story-circle/` |
| **Pixar Formula** | Emotional resonance, heartwarming stories | `templates/novel-presets/pixar-formula/` |
| **Snowflake Method** | Complex plots, multiple threads | `templates/novel-presets/snowflake/` |

**Step 3: Load Detailed Documentation for the Selected Method**

After the user confirms the selection, use the `read_file` tool to read the corresponding preset documents:

For example, if "Three-Act Structure" is chosen, read:

-   `templates/novel-presets/three-act/config.yaml` - Method configuration
-   `templates/novel-presets/three-act/outline.md` - Structure template
-   `templates/novel-presets/three-act/story.md` - Writing guide

**Step 4: Apply Method Guidance**

Develop a creative plan that aligns with the chosen method based on the loaded preset documents.

Record the reasons for the choice and how it will be applied.

### Completion Prompt + Next Steps

After saving, output in the chat:

```
✅ Creative plan has been saved to stories/[number-name]/creative-plan.md
```

Recommendation: Run `/tasks` to generate a task list (by P0/P1/P2).

#### 2.2 Chapter Architecture Design

```markdown
## Chapter Architecture

### Overall Plan

- Total Chapters: [Based on target word count and chapter length]
- Chapter Length: [Based on pacing configuration or default 2000-3000 words/chapter]
- Volume Arrangement: [If applicable]

**🆕 Pacing Parameters (if rhythm-config.json exists)**:

- Average chapter word count: [Read from config, e.g., 3200 words]
- Mini-climax interval: [Read from config, e.g., 5 chapters]
- Major-climax interval: [Read from config, e.g., 30 chapters]
- Pacing style: [Fast/Moderate/Slow]
- Content proportions: Dialogue [X]% / Action [X]% / Description [X]% / Psychology [X]%

### 🌟 Golden Opening Plan (if including Chapters 1-3)

**Important**: If this plan includes Chapters 1-3, special attention must be paid to the following points (based on golden-opening.md):

#### Chapter 1 Plan

- ✅ **Rule 1 - Dynamic Scene Entry**:
    - Prohibited: Static scenes, long environmental descriptions.
    - Required: Direct entry through conflict/action/dialogue.
    - Specific Design: [Describe the opening method for Chapter 1].

- ✅ **Rule 2 - Front-load the Core Conflict**:
    - The protagonist's core conflict must be introduced within Chapter 1.
    - Specific Design: [Describe how the core conflict is presented].

- ✅ **Rule 3 - Avoid Information Dumps**:
    - Absolutely no large-scale world-building introductions at the beginning.
    - Use a "drip-feed" approach to information reveal.
    - Specific Design: [List the information points revealed in Chapter 1].

- ✅ **Rule 4 - Limit the Number of Introduced Characters**:
    - No more than 3 named characters.
    - Specific Design: [List the characters introduced in Chapter 1].

#### Chapters 2-3 Plan

- ✅ **Rule 5 - Quickly Showcase the "Golden Finger" (Special Ability/Advantage)**:
    - Reveal the effect of the "golden finger" in Chapter 2 or 3.
    - Specific Design: [Describe how the golden finger is showcased].

#### Opening Pacing Requirements

- Chapter 1 Goal: Hook the reader, build anticipation.
- Chapter 2 Goal: Showcase abilities, strengthen the hook.
- Chapter 3 Goal: Initial high point, confirm reader interest.

### Emotional Curve Design ⭐ (Building an emotional loop for the reading experience)

**Core Concept**: A good novel is not just a journey of story, but a **journey of emotions**. The essence of why readers keep reading is to chase the ups and downs of emotion and satisfaction.

**Emotion Type Definitions** (using novel terminology):

| Emotion Type | Definition | Reader Experience | Typical Scenes |
| --- | --- | --- | --- |
| 😤 **High Point (爽点)** | Protagonist wins, a reversal, showcases power | Exhilarating, satisfying, looking forward to the next | Face-slapping, comeback, showing off successfully |
| 😭 **Low Point (虐点)** | Protagonist fails, is oppressed, suffers setbacks | Worried, frustrated, hoping for a turnaround | Being bullied, failing, losing someone important |
| 🤔 **Suspense** | The unknown, questions, foreshadowing | Curious, guessing, wanting to continue | A mysterious character appears, a clue is found, a puzzle is left |
| 💧 **Calm** | Daily life, transition, setup | A buffer, understanding, emotional preparation | Daily life, character interactions, world-building exposition |

**Emotional Design Principles**:

1.  ✅ **Build up to let down, then build up again (欲扬先抑)**: Precede a high point with a moderate low point for a stronger impact.
2.  ✅ **Vary the pace (张弛有度)**: Avoid consecutive low points or high points to maintain rhythm.
3.  ✅ **Drive with suspense**: Leave a hook at the end of each chapter to drive the desire to read on.
4.  ✅ **Escalate emotions**: The emotional intensity at the climax should be significantly higher than at the beginning.

**Segmented Emotional Planning**:

| Chapter Segment | Emotion Type | Intensity | Target Effect | Key Scenes |
| --- | --- | --- | --- | --- |
| Chapters 1-3 | Low→High→Suspense | Medium→High→Medium | Suppress then lift off to build reading desire | [Specific description] |
| Chapters 4-8 | Calm→Low→High | Low→Medium→High | First mini-climax | [Specific description] |
| Chapters 9-15 | Suspense→Low→High | Medium→High→High | Second climax, plant foreshadowing | [Specific description] |
| ... | ... | ... | ... | ... |

**Emotional Intensity Levels**:

-   **Low**: Minor emotional fluctuation, mainly for setup and transition.
-   **Medium**: Noticeable emotional ups and downs, reader is engaged.
-   **High**: Emotional peak, reader is highly invested.
-   **Extreme**: The highest point of the entire book, the decisive climax (usually 1-3 instances).

**Emotional Curve Visualization** (optional, ASCII simple chart):

Emotional Intensity
Extreme | ╱╲ ╱╲
High | ╱╲ ╱ ╲ ╱ ╲**_
Medium | ╱╲ ╱ ╲ ╱ ╲_** **\_╱
Low | **╱ ╲╱ ╲\_**\_╱ ╲**╱
└─────────────────────────────────────> Chapters
3 8 15 25 35 45 55

**Emotional Design Self-Checklist**:
- [ ] Is there a clear emotional hook in the first 3 chapters?
- [ ] Is there a calm period of more than 5 consecutive chapters? (Warning: high risk of reader drop-off)
- [ ] Is there a sufficient high-point reward after a low point?
- [ ] Does each volume/stage have a clear emotional climax?
- [ ] Is the highest emotional point of the book in the final 1/3?
- [ ] Does the chapter ending leave a hook for the next chapter?

**Relationship with Pacing Configuration**:
- If `rhythm-config.json` exists, refer to its "high point interval" parameter.
- The emotional pacing of a reference work can be a guide, but adjust according to your own story.
- Different genres have different emotional rhythms (爽文: high-frequency high points; mystery: high-frequency suspense; angst: high satisfaction later).

### Structure Mapping
[Map key nodes to specific chapters based on the selected method]

### Clue Distribution Plan

**Important**: Read the clue management specification from `specification.md`, Chapter 5, and mark active clues in each volume/chapter segment.

#### Volume 1: [Volume Name] (Chapter Range)

| Chapter Segment | Content | Key Events | **Active Clues** | **Intersection Point** |
| --- | --- | --- | --- | --- |
| [Ch. X-Y] | [Segment content] | [List of key events] | PL-01⭐⭐⭐, PL-02⭐⭐ | X-001 (Chapter X) |
| [Ch. X-Y] | [Segment content] | [List of key events] | PL-01⭐⭐, PL-03⭐⭐⭐ | None |

**Clue Annotation Guide**:
- PL-XX: Clue ID, from `specification.md`, section 5.1
- ⭐⭐⭐ Main thread: This chapter segment focuses on advancing this clue, taking up the main portion of the text.
- ⭐⭐ Supporting: Normally advanced, with some text portion.
- ⭐ Background: Mentioned occasionally to maintain its presence.
- X-XXX: Intersection Point ID, from `specification.md`, section 5.3

#### Volume 2: [Volume Name] (Chapter Range)

[Repeat the table structure above]

### Pacing Design
- Opening Hook: Chapter [X]
- First Climax: Chapter [X]
- Midpoint Turn: Chapter [X]
- Biggest Crisis: Chapter [X]
- Final Climax: Chapter [X]
```

#### 2.3 Character System Design

```markdown
## Character System

### Protagonist Design

- Initial State: [Starting point]
- Growth Arc: [Trajectory of change]
- Core Conflict: [Internal vs. External]
- Key Turning Points: [Specific chapters]

### Supporting Character Functions

[Functional role and appearance plan for each important supporting character]

### Relationship Network

[Character relationship map and evolution plan]
```

#### 2.4 World-building

```markdown
## World-building System

### Core Settings

- World Rules: [Physical/Magical/Technological rules]
- Social Structure: [Political/Economic/Cultural]
- Historical Background: [Important historical events]

### Setting Reveal Plan

- Level 1 (Opening): [Basic settings]
- Level 2 (Development): [In-depth settings]
- Level 3 (Climax): [Core secrets]
```

#### 2.5 Plot Technical Design

```markdown
## Plot Technology

### Conflict Escalation Path

1.  Primary Conflict: [Personal level]
2.  Secondary Conflict: [Group level]
3.  Tertiary Conflict: [World level]

### Suspense Setup

-   Main Suspense: [Spans the entire story]
-   Chapter Suspense: [Hooks for each chapter]
-   Subplot Suspense: [Adds layers]

### Foreshadowing Layout

[List of foreshadowing and resolution plan]
```

#### 2.6 Narrative Technique Selection

```markdown
## Narrative Technology

### POV Design

-   Viewpoint Type: [First/Third person]
-   Viewpoint Limitation: [Omniscient/Limited]
-   Multi-POV Arrangement: [If applicable]

### Timeline Design

-   Main Timeline: [Linear/Non-linear]
-   Flashback Interspersion: [Strategy for use]
-   Parallel Narrative: [If applicable]

### Narrative Pacing

-   Fast-paced Sections: [Action/Conflict]
-   Slow-paced Sections: [Emotion/Description]
-   Pacing Variation: [Rhythm of tension and release]
```

### 3. Technical Decision Record

Record all important technical decisions:

-   **Decision**: What was chosen.
-   **Reason**: Why it was chosen.
-   **Risk**: Potential problems.
-   **Alternative**: Backup plan.

### 4. Quality Assurance Plan

```markdown
## Quality Assurance

### Self-Checklist

- [ ] Logical consistency checkpoints
- [ ] Plausibility of character actions
- [ ] Self-consistency of world-building
- [ ] Fluency of pacing

### Verification Nodes

- Every 5 chapters: Minor loop verification
- Every volume: Major loop verification
- On completion: Comprehensive verification
```

### 5. Risk Management

Identify and develop coping strategies for:

-   **Creative Risks**: Inspiration, logic, pacing.
-   **Technical Risks**: Complexity, consistency.
-   **Time Risks**: Schedule, quality balance.

### 6. Output and Validation

-   Save the plan to `stories/*/creative-plan.md`.
-   Verify the plan complies with constitutional principles.
-   Verify the plan meets specification requirements.
-   Prompt for the next step: Run `/tasks` to generate tasks.

## Relationship with Other Commands

-   **Input**: Specifications from `/specify` + clarifications from `/clarify`.
-   **Output**: Provides the basis for task generation for `/tasks`.
-   **Validation**: Used by `/analyze` to check implementation compliance.

## Precautions

### 🌟 Application of the Golden Opening Rules (Important)

**When to apply**:

-   Automatically triggered when the plan includes Chapters 1-3.
-   Or for short works with a total word count < 10,000.

**Why it's important**:

-   The first three chapters determine 80% of reader retention.
-   The opening is the critical window where readers decide whether to continue.
-   The golden opening rules have been validated by numerous blockbuster works.

**How to apply**:

1.  Create a separate "Golden Opening Plan" section in the "Chapter Architecture Design."
2.  Check each of the five golden rules to see if they are reflected in the first three chapters.
3.  Specifically design how each chapter will meet the rule's requirements.
4.  If the specification conflicts with a rule, prioritize the rule (or consciously break it).

**Common Mistakes**:

-   ❌ Long descriptions of world-building in Chapter 1.
-   ❌ The protagonist is just living their daily life in Chapter 1, with no conflict.
-   ❌ Too many characters introduced in Chapter 1 (>3).
-   ❌ The "golden finger"/core ability is delayed until after Chapter 5.

### 🎵 Application of Pacing Configuration

**If `/book-internalize` was used**:

-   The system will automatically read `spec/presets/rhythm-config.json`.
-   Apply the pacing parameters of the reference work (chapter word count, high-point interval, etc.).
-   Apply content proportions (dialogue/action/description/psychology).

**Parameter Priority**:

1.  **User's immediate instructions** (Highest)
2.  **`rhythm-config.json`** (Reference work's pacing)
3.  **Genre knowledge base** (Genre-specific pacing)
4.  **Default values** (2000-3000 words/chapter)

**Recommendations**:

-   The pacing parameters of the reference work are for reference only.
-   Adjust moderately based on your own writing habits.
-   Do not copy mechanically; maintain flexibility.

### Technology Serves the Story

-   All technical choices must serve the story's expression.
-   Don't use techniques for the sake of using them.
-   Maintain flexibility in the plan.

### Executability

-   The plan must be specific and executable.
-   Avoid being overly idealistic.
-   Consider actual creative capacity.

### Iterative Optimization

-   The plan can be adjusted based on practice.
-   Record the reasons for and impact of adjustments.
-- Maintain version tracking.

Remember: **A good plan is half the battle, but always be ready to adjust. The golden opening is a hard rule; other plans can be flexible.**
