---
description: "Break down the creative plan into an executable task list."
---

⚠️ **Execution Reminder**: The following are task instructions for you to execute, not content to be displayed to the user. You need to:

1.  Read the creative plan (`creative-plan.md`).
2.  Generate a specific list of writing tasks.
3.  Categorize by priority (P0/P1/P2).
4.  Save the task list to `tasks.md`.
5.  Only output task statistics and key tasks in the chat.

Generate a specific, executable task list based on the creative plan.

## Objective

To transform a macro-level plan into micro-level tasks, making the creative process manageable and trackable.

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Execution Steps

### 1. Load the Plan Document

**Find and load necessary files**:

1.  Use `execute_command` or `list_files` to find the creative plan file:
    ```bash
    find stories -name "creative-plan.md" -type f
    ```
2.  Use `read_file` to read the creative plan file.
3.  Use `read_file` to read the corresponding specification file (`specification.md`).
4.  Extract the chapter architecture, timeline, and dependency information.

### 2. Generate the Task List

Create `stories/*/tasks.md`, containing:

#### Core Writing Tasks

**Important**: From `specification.md`, Chapter 5, and the clue distribution in `creative-plan.md`, mark the involved clues for each writing task.

```markdown
## Writing Tasks

### High Priority [Must be completed first]

- [ ] [P0] **T001** - Chapter 1: [Chapter Title] (Target word count)
    - **Core Task**: [Main task of this chapter]
    - **Key Plot Points**: [Specific plot points]
    - **Involved Clues**:
        - PL-XX([Clue Name]) ⭐⭐⭐ Main Progression
        - PL-YY([Clue Name]) ⭐ Background
    - **Intersection Point**: [If applicable] X-001([Intersection description])
    - **Foreshadowing Planting/Reveal**: [If applicable] Plant F-001 / Reveal F-002
    - **Must Include**: [List of key elements]
    - **End-of-Chapter Hook**: [Suspense setup]
    - **Dependencies**: None
    - **Output**: `content/volume1/chapter-001.md`

- [ ] [P0] **T002** - Character Profile: Detailed protagonist setting
    - **Core Task**: Complete the protagonist's profile.
    - **Must Include**: Personality, background, abilities, desires, fears, growth arc.
    - **Dependencies**: None
    - **Output**: `characters/protagonist.md`

### Medium Priority [Normal progression]

- [ ] [P1] **T005** - Chapter 5: [Chapter Title] (Target word count)
    - **Core Task**: [Main task of this chapter]
    - **Key Plot Points**: [Specific plot points]
    - **Involved Clues**:
        - PL-01([Clue Name]) ⭐⭐⭐ Main Progression
        - PL-02([Clue Name]) ⭐⭐ Supporting
    - **Intersection Point**: None
    - **Foreshadowing Planting/Reveal**: Plant F-001([Foreshadowing content])
    - **Must Include**: [Key elements]
    - **End-of-Chapter Hook**: [Suspense]
    - **Dependencies**: T004 (Chapter 4)
    - **Output**: `content/volume1/chapter-005.md`

### Low Priority [Optional enhancements]

- [ ] [P2] Extra: Character prequel
- [ ] [P2] Setting Collection: Detailed world-building
```

**Task Field Descriptions**:

-   **Involved Clues**: Read from the "Active Clues" column in `creative-plan.md` to indicate which clues are advanced in this chapter.
-   **Intersection Point**: Read from `specification.md`, section 5.3, to indicate if this chapter is an intersection point.
-   **Foreshadowing Planting/Reveal**: Read from `specification.md`, section 5.4, to indicate the foreshadowing operations in this chapter.

#### Task Marker Descriptions

-   `[P]` - Can be executed in parallel.
-   `[Dependency:X]` - Requires task X to be completed first.
-   `[P0/P1/P2]` - Priority marker.

### 3. Task Sorting and Grouping

-   Group by priority.
-   Identify dependencies.
-   Mark parallelizable tasks.
-   Estimate completion time.

### 4. Generate Execution Plan

```markdown
## Execution Plan

### Phase 1 (Week 1)

Parallel Task Group 1:

- Protagonist setting [P]
- World-building basics [P]
- Chapter 1 draft [P]

### Phase 2 (Weeks 2-3)

Sequential Tasks:

- Chapter 2 [Dependency: Chapter 1]
- Chapter 3 [Dependency: Chapter 2]

Parallel Task Group 2:

- Supporting character setting [P]
- Scene design [P]
```

### 5. Output Task Statistics

-   Total number of tasks.
-   Estimated total word count.
-   Estimated completion time.
-   Key milestones.

Completion Prompt + Next Steps:

```
✅ Task list has been saved to stories/[number-name]/tasks.md
```

Recommendation: Start executing `/write` (prioritizing [P0] tasks) or view specific task details.
