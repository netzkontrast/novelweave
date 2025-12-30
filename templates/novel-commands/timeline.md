---
description: "Manage and validate the story timeline."
argument-hint: "[add | check | show | sync]"
---

# Timeline Management

Maintain the story's timeline to ensure chronological consistency.

User Input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Features

1.  **Time Logging** - Track the time point of each chapter.
2.  **Parallel Events** - Manage multiple plotlines happening simultaneously.
3.  **Historical Comparison** - Compare with real historical events (for historical novels).
4.  **Logical Validation** - Check the reasonableness of time spans.

## Usage

The following operations are supported:

-   `add` - Add a time node.
-   `check` - Validate time continuity.
-   `show` - Display a timeline overview.
-   `sync` - Synchronize parallel events.

## Execution Steps

### 1. Load Timeline Data

Use the `read_file` tool to read `spec/tracking/timeline.json`:

-   If the file exists, load the existing timeline data.
-   If the file does not exist, recommend running `/track-init` first to initialize the tracking system.

### 2. Deep Check (`--check`)

In `--check` mode, perform a rule-based check equivalent to the script and output a checklist:

1.  **File Integrity**

    -   CHK001 `timeline.json` exists and is in valid format.

2.  **Time Settings**

    -   CHK002 Story start time is set: `storyTime.start`.
    -   CHK003 Current story time is set: `storyTime.current`.

3.  **Event Records**

    -   CHK004 Time events have been recorded (`events.length > 0`).
    -   CHK005 Time events are sorted in ascending order by chapter (incrementing `chapter`, no disorder).

4.  **Parallel Events**

    -   CHK006 Parallel event time points are recorded logically (`parallelEvents.timepoints` can be empty).

5.  **Record Check Results (Optional)**

    -   Write the check time to `lastChecked`.
    -   Write the number of anomalies to `anomalies.lastCheckIssues`.

Checklist output reference:

```
# Timeline Check Checklist
Object: spec/tracking/timeline.json
Number of Recorded Events: [N]

- [x] CHK001 timeline.json exists and is in valid format.
- [x] CHK002 Story start time is set (...).
- [x] CHK003 Current story time has been updated (...).
- [x] CHK004 Time events have been recorded (N events).
- [x] CHK005 Time events are ordered by chapter.
- [ ] CHK006 Parallel event time points have been recorded (K events/no record).

Next Actions...
```

## Timeline Data

Timeline information is stored in `spec/tracking/timeline.json`:

-   In-story time (year/month/day).
-   Chapter correspondence.
-   Important event markers.
-   Time span calculations.

## Example Output

```
📅 Story Timeline
━━━━━━━━━━━━━━━━━━━━
Current Time: Spring, 30th year of the Wanli reign

Chapter 1  | Winter, 29th year of Wanli | Transmigration event
Chapter 4  | First month, 30th year of Wanli | Travels north for the exam
Chapter 6  | Second month, 30th year of Wanli | Metropolitan examination
Chapter 8  | Third month, 30th year of Wanli | Palace examination
Chapter 61 | Fourth month, 30th year of Wanli | [To be written]

⏱️ Time Span: 5 months
🔄 Parallel Event: Japanese invasion of Korea
```

## Completion Prompt + Next Steps

Output in chat:

```
✅ Timeline processing complete (show/add/check/sync)
```

Recommendations:

-   If disorder or missing time nodes are found → Complete the relevant chapters and events in `/plan` or `/tasks`.
-   Run `/track --check` for a comprehensive consistency check.
