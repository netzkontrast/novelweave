---
description: "Define the story specifications, clarifying what kind of work is to be created."
argument-hint: "[Story description]"
---

User Input: $ARGUMENTS

## Objective

To define the story like a product requirements document (PRD), clarifying "what to create" rather than "how to create it." Generate a complete and detailed nine-chapter specification document, and output the specifications with `[Clarification Needed]` markers to leave room for subsequent clarification steps.

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Execution Steps

### 1. Determine Story Name and Path

**For a new story**:

-   Extract the story name from user input, or ask the user.
-   Create the path: `stories/001-[story-name]/specification.md`.
-   Use sequential numbers (001, 002...) to avoid conflicts.

**For updating an existing story**:

-   Use the existing story path.

### 2. Check for Constitution Compliance

Use the `read_file` tool to attempt to read `memory/constitution.md`:

-   If the file exists:
    -   Load the constitutional principles.
    -   Ensure the specifications comply with the constitutional values.
    -   Reference the relevant principles in the specifications.
-   If the file does not exist:
    -   Continue creating the specifications, but recommend that the user create a constitution first.

### 3. Create the Story Specification Document

Generate detailed specifications using the full nine-chapter structure:

```markdown
# Story Specification Document

## Metadata

- Story Name: [Name]
- Version: 1.0.0
- Creation Date: [YYYY-MM-DD]
- Status: Draft
- Author: [Author Name]

## 1. Story Overview

### One-Sentence Story (Elevator Pitch)

[Describe the core of the story in under 30 words]

### Synopsis (100-200 words)

[Expanded description, including the main conflict and a hint of the ending]

### Core Themes

- Theme: [e.g., "Growth," "Redemption," "Revenge"]
- Deeper Meaning: [What you want to express]
- Emotional Core: [What you want the reader to feel]

## 2. Target Positioning

### Target Audience Profile

- Age Group: [Clarification Needed: Specific age range]
- Gender Skew: [Clarification Needed: Male-oriented/Female-oriented/General]
- Reading Level: [Clarification Needed: Beginner/Intermediate/Advanced]
- Genre Preference: [Fantasy/Urban/Historical, etc.]
- Reading Scenario: [Fragmented time/Deep reading]

### Market Positioning

- Main Genre: [Clarification Needed: 爽文/Mystery/Romance/Serious Literature/Sci-Fi/Fantasy/Historical/Urban/Other]
- Sub-genre: [Specific sub-genre, e.g., System-flow/Classic Detective/CEO novel, etc.]
- Genre Fusion: [If any, e.g., Mystery + Romance]
- Genre Tags: [Main Tag] + [Secondary Tag]
- Competitive Analysis: Like [Work 1]'s [feature] + [Work 2]'s [feature]
- Differentiation: [Clarification Needed: What is the core selling point?]

## 3. Success Criteria

### Quantitative Metrics

- Target Word Count: [Clarification Needed: 30k/100k/500k]
- Update Frequency: [Clarification Needed: Daily/Weekly/Monthly]
- Completion Time: [Estimated duration]
- Commercial Goals: [If applicable]

### Quality Standards

- Logical Consistency: [Must/Should] have no obvious loopholes.
- Character Richness: The protagonist has [X] layers, supporting characters have [Y] layers.
- Plot Compactness: [Clarification Needed: Conflict in every chapter/Transitional chapters allowed]
- Writing Standard: [Clarification Needed: Easy to understand/Literary/Professional]

### Reader Feedback Metrics

- Target Rating: [If applicable]
- Engagement Rate: [Comment/Collection ratio]
- Completion Rate: [Expected reader completion percentage]

## 4. Core Requirements

### Must-Haves (P0)

1.  [Core plot element 1]
2.  [Core character relationship]
3.  [Core conflict setup]
4.  [Necessary world-building element]

### Should-Haves (P1)

1.  [Elements that enhance the experience]
2.  [Content that deepens the theme]
3.  [Subplots that enrich the characters]

### Could-Haves (P2)

1.  [Nice-to-have content]
2.  [Optional subplots]
3.  [Additional easter eggs]

## 5. Clue Management Specification

> **Multi-Clue Management Note**: This chapter defines all story clues (main plot, subplots) and their management strategies to address issues like parallel progression, timing of intersections, and ensuring consistency after modifications.

### 5.1 Clue Definition Table

Define the basic information for all story clues:

| Clue ID | Clue Name | Type | Priority | Start-End Chapters | Core Conflict | Main Characters |
| --- | --- | --- | --- | --- | --- | --- |
| PL-01 | [Clue 1 Name, e.g., "Family Line"] | Main/Sub/Main-support | P0/P1/P2 | [Start Ch - End Ch] | [Core conflict of the clue] | [Main characters involved] |
| PL-02 | [Clue 2 Name, e.g., "Love Line"] | Main/Sub/Main-support | P0/P1/P2 | [Start Ch - End Ch] | [Core conflict of the clue] | [Main characters involved] |

**Notes**:

- Clue ID format: PL-XX (abbreviation for Plotline)
- Type: Main (drives the story), Sub (enriches the plot), Main-support (serves the main plot)
- Priority: P0 (Must-have), P1 (Important), P2 (Optional)

### 5.2 Clue Pacing Plan

Plan the activity level of each clue in different stages:

| Clue ID | Volume 1 | Volume 2 | Volume 3 | Volume 4 |
| --- | --- | --- | --- | --- |
| PL-01 | ⭐⭐⭐ Active | ⭐⭐ Medium | ⭐ Background | ⭐⭐⭐ Active |
| PL-02 | ⭐⭐ Initiated | ⭐⭐⭐ Active | ⭐⭐⭐ Active | ⭐⭐ Concluding |

**Notes**:

- ⭐⭐⭐ Active: Heavily featured in this volume, significant portion of the text.
- ⭐⭐ Medium: Progresses normally, moderate portion of the text.
- ⭐ Background: Mentioned occasionally to maintain presence.
- ❌ Not present: The clue has not yet been introduced.

### 5.3 Clue Intersection Point Plan

Pre-plan the intersection points between clues to avoid arbitrary development by the AI:

| Intersection ID | Chapter | Involved Clues | Intersection Content | Expected Effect |
| --- | --- | --- | --- | --- |
| X-001 | [Chapter No.] | PL-XX + PL-YY | [How two/more clues intersect, what happens] | [Impact on the story and characters] |
| X-002 | [Chapter No.] | PL-XX + PL-YY + PL-ZZ | [Specific content of the intersection] | [Expected effect to be produced] |

**Notes**:

- Intersection ID format: X-XXX (from the first letter of Intersection)
- Involved Clues: List all clue IDs that intersect here.
- Intersection Content: The specific plot or event.
- Expected Effect: Emotional conflict, plot twist, character growth, etc.

### 5.4 Foreshadowing Management Table

Manage the planting and revealing of all foreshadowing to ensure nothing is missed:

| Foreshadow ID | Planting Chapter | Involved Clues | Foreshadowing Content | Reveal Chapter | Reveal Method |
| --- | --- | --- | --- | --- | --- |
| F-001 | [Chapter No.] | PL-XX | [What foreshadowing is planted, specific content] | [Chapter No.] | [How it is revealed, through what event] |
| F-002 | [Chapter No.] | PL-XX + PL-YY | [Cross-clue foreshadowing content] | [Chapter No.] | [Reveal method] |

**Notes**:

- Foreshadowing ID format: F-XXX (from the first letter of Foreshadowing)
- Involved Clues: The clues associated with the foreshadowing, may span multiple clues.
- The chapter numbers for planting and revealing must be clear to avoid being forgotten.

### 5.5 Clue Modification Decision Matrix

When a clue needs to be modified, this process must be followed to assess the impact:

**Modification Checklist**:

1.  Check section 5.2: In which volumes is this clue active? Does the activity level need to be adjusted?
2.  Check section 5.3: Which intersection points does this clue involve? Does the timing of the intersection points need to be changed?
3.  Check section 5.4: Which foreshadowing does this clue involve? Does the planting/revealing of the foreshadowing need to be adjusted?
4.  Check `creative-plan.md`: Which chapter segments need to be synchronized?
5.  Check `tasks.md`: Which writing tasks need to be replanned?
6.  Check `plot-tracker.json`: What is the current progress, and how should it be adjusted going forward?

**Example**: Suppose you modify PL-03 (Love Line) to have a character appear in Chapter 50 (originally planned for Chapter 100):

- ✅ Intersection point X-004 needs to be moved up or canceled.
- ✅ The active clues in the chapter segments of Volume 2 need to be adjusted.
- ✅ The "Involved Clues" field of related tasks needs to be updated.
- ✅ It may affect the reveal timing of foreshadowing F-002.

### 5.6 Clue Consistency Principles

**Planning Principles**:

-   Each clue must have a clear beginning, middle, and end.
-   The main plot should occupy 40-60% of the total text.
-   No more than 2-3 subplots to avoid being too scattered.
-   Clue intersections should serve the theme, not just happen for the sake of it.
-   Long-dormant clues (>20 chapters) must have a reasonable justification.

**Verification Standards**:

-   [ ] All clues have clear conflicts and resolutions.
-   [ ] The pacing of clues is reasonably distributed, with no excessive concentration or gaps.
-   [ ] The number of intersection points is moderate (2-3 per 50 chapters is recommended).
-   [ ] All foreshadowing has a corresponding reveal plan.
-   [ ] The modification decision matrix is actionable.

## 6. Constraints

### Content Red Lines

-   Absolutely Prohibited: [e.g., illegal content]
-   To be Avoided: [e.g., sensitive topics]
-   Handle with Care: [Clarification Needed: How to handle romantic relationships]

### Creative Constraints

-   Knowledge Limitations: [Clarification Needed: Is specialized knowledge required?]
-   Time Constraints: [Completion deadline]
-   Resource Constraints: [e.g., required reference materials]

### Technical Constraints

-   Publishing Platform: [Clarification Needed: Web novel platform/Publishing/Self-media]
-   Formatting Requirements: [Chapter length, etc.]
-   Update Requirements: [Fixed times, etc.]

## 7. Risk Assessment

### Creative Risks

-   Writing Difficulty: [Clarification Needed: Where are the challenges?]
-   Writer's Block: [How to cope]
-   Logical Loopholes: [Complexity assessment]
-   Multi-clue Management: [Clarification Needed: Is the number of clues too high?]

### Market Risks

-   Homogenization: [How to differentiate]
-   Reader Acceptance: [Clarification Needed: Is the innovation excessive?]
-   Timeliness: [Will the theme become outdated?]

## 8. Core Decision Points [Clarification Needed]

The following key decisions need to be clarified in the `/clarify` stage:

1.  [Decision 1: e.g., Is the protagonist's personality passionate or calm?]
2.  [Decision 2: e.g., Is the ending open or happy?]
3.  [Decision 3: e.g., Is the narrative single-threaded or multi-threaded?]
4.  [Decision 4: e.g., Is the pacing fast or slow?]
5.  [Decision 5: e.g., Is the style lighthearted or serious?]

## 9. Verification Checklist

-   [ ] The story overview is clear and concise.
-   [ ] The target audience is accurately defined.
-   [ ] The success criteria are measurable.
-   [ ] The core requirements are listed.
-   [ ] The clue management specification is defined.
-   [ ] The constraints are identified.
-   [ ] The risks are assessed.
-   [ ] The key decision points are marked.

## Appendix: Reference Materials

### Sources of Inspiration

-   [Source 1]
-   [Source 2]

### Reference Works

-   [Work 1]: Reference its [feature]
-   [Work 2]: Reference its [feature]

### Additional Notes

[Other information that needs to be noted]
```

### 4. Mark Points Needing Clarification

Mark all decision points that require further clarification:

-   Use the `[Clarification Needed: Specific question]` format.
-   Ensure 5-10 key decision points are marked.
-   These will be addressed in the `/clarify` step.

### 5. Output and Save

-   Save the specifications to `stories/[story-name]/specification.md`.
-   Output a success message.
-   Prompt the next step: Run `/clarify` to clarify key decisions.

## Important Restrictions

⚠️ **Only execute the task for the `/specify` command**:

-   Only generate and save the `specification.md` file.
-   Do not execute other commands like `/clarify`, `/plan`, `/tasks`, `/write`, `/analyze`.
-   Do not automatically create other files.
-   The user needs to manually run subsequent commands.

## Precautions

### Focus on WHAT, not HOW

-   ✅ Correct: "We need a villain that the readers will despise."
-   ❌ Incorrect: "The villain appears in Chapter 3, using a flashback."

### Maintain Flexibility in Specifications

-   Leave room for clarification.
-   Mark all uncertain points as `[Clarification Needed]`.

### Relationship with Subsequent Steps

-   Run `/clarify` to address all `[Clarification Needed]` markers.
-   **After clarification**: Run `/plan` to develop a creative plan.
-   **During execution**: Run `/analyze` to verify that the implementation meets the specifications.

Remember: **Specifications define the goal, not the path. Even if the user input is brief, generate a complete and detailed specification document.**
