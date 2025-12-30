---
description: "Comprehensive tracking of novel writing progress and content."
argument-hint: "[--brief | --plot | --stats | --check]"
---

# Comprehensive Progress Tracking

A complete display of all progress and statuses in the novel writing process.

User Input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Tracking Dimensions

1.  **Writing Progress** - Word count, chapters, completion rate.
2.  **Plot Development** - Main plot progress, subplot status.
3.  **Timeline** - Progression of the story's time.
4.  **Character Status** - Character development and location.
5.  **Foreshadowing Management** - Status of planting and resolving.

## Usage

The following operations are supported (parsed from `$ARGUMENTS`):

-   No arguments - Display the full tracking report.
-   `--brief` - Display brief information.
-   `--plot` - Display only plot tracking.
-   `--stats` - Display only statistics.
-   `--check` - Perform a deep consistency check.

## Execution Steps

### 1. Load Tracking Data

Use the `read_file` tool to read the tracking files:

-   `spec/tracking/timeline.json` - Timeline data.
-   `spec/tracking/relationships.json` - Relationship network.
-   `spec/tracking/plot-tracker.json` - Plot tracking (if it exists).
-   `spec/tracking/character-state.json` - Character status (if it exists).

## Output Example

```
📊 Comprehensive Novel Writing Report
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📖 "The Grand Ming Dynasty"

✍️ Writing Progress
  Completed: 60/240 chapters (25%)
  Word Count: 162,000/800,000
  Currently: Volume 2 "Courtroom Intrigues"

📍 Plot Status
  Main Plot: The Great Reform [Initial entry into the court]
  Subplot: Romance [Getting to know each other]

⏰ Timeline
  Story Time: Spring, 30th year of the Wanli reign
  Time Span: 5 months

👥 Main Characters
  Li Zhongyong: Hanlin Academy Compiler @ Beijing
  Shen Yuqing: Adopted daughter of Zhang Juzheng [Active]

⚡ To Be Addressed
  Foreshadowing: 3 unresolved
  Conflict: Reformists vs. Conservatives [Escalating]

✅ Consistency Check: Passed
```

## Enhanced Features

### Data Consistency Validation

Basic Check (executed by default):

-   File Integrity: `spec/tracking/*.json` all exist and are valid JSON; `progress.json` (if it exists) has a valid format.
-   Chapter Number Sync: `progress.json.statistics.currentChapter`, `plot-tracker.json.currentState.chapter`, and `character-state.json.protagonist.currentStatus.chapter` are consistent.
-   Consistency between `plot-tracker.json` and `stories/*/creative-plan.md`.
-   Time logic of `timeline.json` (chapters incrementing, current time set).
-   Relationship conflicts in `relationships.json`.
-   Positional logic in `character-state.json`.

### Deep Validation Mode (`--check`)

When the `--check` parameter is used, a programmatic deep validation is performed:

#### Internal Task Flow (automatic execution)

```markdown
# Phase 1: Basic Validation [Parallel execution]

- [x] T001 [P] File integrity check (progress.json, plot-tracker.json, timeline.json, relationships.json, character-state.json)
- [x] T002 [P] Chapter number synchronization check (progress.json ↔ plot-tracker.json ↔ character-state.json)
- [x] T003 [P] Execute plot consistency check (plot-check logic)
- [x] T004 [P] Execute timeline validation (timeline logic: chapter ascending order, current time setting)
- [x] T005 [P] Execute relationship validation (relations logic)
- [x] T006 [P] Execute world-building validation (world-check logic)

# Phase 2: In-depth Character Validation

- [x] T007 Load validation rules from validation-rules.json
- [x] T008 Scan all character names in the chapters
- [x] T009 Compare with character-state.json to validate name consistency
- [x] T010 Check if forms of address comply with relationships.json
- [x] T011 Validate if character actions are consistent with their profiles

# Phase 3: Generate Comprehensive Report

- [x] T012 Summarize all validation results
- [x] T013 Mark the severity of issues
- [x] T014 Tally foreshadowing (total, active, overdue >50 chapters)
- [x] T015 Generate repair suggestions
- [x] T016 Output/Update `spec/tracking/.last-check.json` (timestamp, total, passed, warnings, errors)
```

#### Validation Report Example

```
📊 Deep Validation Report
━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Passed Items: 15/18

❌ Issues Found (3):
1. [High] Chapter 3: Protagonist's name "Li Ming" should be "Li Zhongyong"
2. [Medium] Chapter 7: Incorrect form of address for Shen Yuqing, used "Senior Brother"
3. [Low] Chapter 12: Unexplained time jump in the timeline

🔧 Autofixable: 2
📝 Needs Manual Confirmation: 1

Run /track --fix to automatically fix simple issues.
```

### Auto-Fix Mode (`--fix`)

When the `--fix` parameter is used, issues are automatically fixed based on the validation report:

#### Auto-Fix Scope

1.  **Character Name Errors** - Automatically replace based on `validation-rules.json`.
2.  **Fixed Form of Address Errors** - Automatically correct to the right form of address.
3.  **Simple Typos** - Correct obvious typos.

#### Fix Flow

```markdown
# Internal Fix Tasks (automatic execution)

- [x] F001 Read the list of issues from the validation report
- [x] F002 [P] Fix character name error in Chapter 3
- [x] F003 [P] Fix form of address error in Chapter 7
- [x] F004 Generate a fix report
- [x] F005 Update tracking files
```

#### Fix Report Example

```
🔧 Auto-Fix Report
━━━━━━━━━━━━━━━━━━━
✅ Fixed: 2 issues
- Chapter 3: "Li Ming" → "Li Zhongyong"
- Chapter 7: "Senior Brother" → "Young Master"

⚠️ Needs Manual Handling: 1 issue
- Chapter 12: The time jump needs an explanation.

Fix complete! It is recommended to run /track --check again to verify.
```

### Smart Analysis and Suggestions

1.  **Progress Analysis**
    -   Compare planned progress with actual progress.
    -   Predict completion time.
    -   Identify writing bottlenecks.

2.  **Content Analysis**
    -   Foreshadowing coverage (planted/resolved).
    -   Character appearance frequency.
    -   Conflict intensity curve.

3.  **Actionable Suggestions**
    Based on the analysis, provide:
    -   Next writing priorities.
    -   Foreshadowing that needs to be addressed.
    -   Relationship lines that need strengthening.
    -   Timeline adjustment suggestions.

### Plot Consistency Check Details (align with `check-plot` logic)

When generating or reading the report, add the following "plot alignment" check items:

-   Main plot progress: `plot-tracker.json.plotlines.main.currentNode` and `status`.
-   Number of completed nodes: `completedNodes.length`; display the last 3.
-   Upcoming: `upcomingNodes[0:3]`.
-   Foreshadowing statistics: total, active, resolved; overdue threshold (suggested 30-50 chapters, with minor adjustments based on genre).
-   Conflict statistics: `conflicts.active.length` and intensity labels.
-   Pacing suggestions: Generate "hook/mini-climax/end-of-volume" reminders based on the current chapter range.

If `spec/tracking/plot-tracker.json` is missing: first prompt to initialize with `/track-init` (without blocking other checks).

### Visualized Report

Generate a structured report:

```
📊 Comprehensive Tracking Report
━━━━━━━━━━━━━━━━━━━━━━━━━━
[Progress Bar] ████████░░░░░░░ 25%

🎯 Next Step Suggestions:
1. Resolve the "Ancient Bronze Mirror" foreshadowing before Chapter 65.
2. Strengthen the direct conflict between the protagonist and the antagonist.
3. Supplement the timeline details for the second volume.

⚠️ Needs Attention:
- Character B has not appeared for 5 chapters.
- Subplot progress is lagging.
- The time jump in Chapter 45 needs to be explained.
```

### Data Export

Support exporting tracking data as:

-   A complete report in Markdown format.
-   Raw data in JSON format.
-   Visual charts (relationship graph, timeline).

## Completion Prompt + Next Steps

Output in chat:

```
✅ Tracking report has been generated (--brief/--plot/--stats/--check)
```

Recommendations:

-   If there are errors/warnings → Prioritize handling according to the "Repair Suggestions" in the report.
-   Content issues → `/write` to revise the manuscript; Framework/task issues → `/plan`, `/tasks`.
-   After fixing, you can run `/track --check` again to verify.
