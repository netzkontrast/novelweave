---
description: "Initialize the tracking system, setting up tracking data based on the story outline."
---

# Initialize Tracking System

Initialize all tracking data files based on the created story specifications and creative plan.

User Input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## When to Use

Execute this command after completing `/specify` and `/plan`, before starting to write.

## Initialization Flow

### 1. Read Base Data

Use the `read_file` tool to read the necessary files:

-   Read the specification file `stories/*/specification.md` to get the story settings.
-   Read the creative plan `stories/*/creative-plan.md` to get the chapter plan.
-   Read the task list `stories/*/tasks.md` to get task information.

### 2. Load Tracking Templates

**Important**: First, load the extension's built-in tracking templates as a structural reference.

Use the `read_file` tool to read the following template files:

1.  **Plot Tracking Template**:
    -   Extension path: `templates/novel-tracking/plot-tracker.json`
2.  **Timeline Template**:
    -   Extension path: `templates/novel-tracking/timeline.json`
3.  **Character Relationships Template**:
    -   Extension path: `templates/novel-tracking/relationships.json`
4.  **Character State Template**:
    -   Extension path: `templates/novel-tracking/character-state.json`
5.  **Validation Rules Template**:
    -   Extension path: `templates/novel-tracking/validation-rules.json`

### 3. Initialize Tracking Files

**Important**: Based on the loaded templates and in conjunction with the clue management specifications in `specification.md`, Chapter 5, generate the initial tracking data.

Create or update `spec/tracking/plot-tracker.json`:

-   Use the template as the base structure.
-   Read all clue definitions from `specification.md`, section 5.1, and populate them.
-   Read all intersection points from `specification.md`, section 5.3.
-   Read all foreshadowing from `specification.md`, section 5.4.
-   Read the clue distribution for chapter segments from `creative-plan.md`.
-   Set the current state (assuming writing has not yet begun).

**Generated `plot-tracker.json` Example**:

```json
{
	"novel": "[Read story name from specification.md]",
	"lastUpdated": "[YYYY-MM-DD]",
	"currentState": {
		"chapter": 0,
		"volume": 1,
		"mainPlotStage": "[Initial Stage]"
	},
	"plotlines": {
		"main": {
			"name": "[Main Plot Name]",
			"status": "active",
			"currentNode": "[Starting Point]",
			"completedNodes": [],
			"upcomingNodes": "[Read from intersection points and chapter plan]"
		},
		"subplots": [
			{
				"id": "[Read from 5.1, e.g., PL-01]",
				"name": "[Clue Name]",
				"type": "[Main/Subplot/Main-support]",
				"priority": "[P0/P1/P2]",
				"status": "[active/dormant]",
				"plannedStart": "[Starting Chapter]",
				"plannedEnd": "[Ending Chapter]",
				"currentNode": "[Current Node]",
				"completedNodes": [],
				"upcomingNodes": "[Read from intersection table]",
				"intersectionsWith": "[Read related clues from 5.3 intersection table]",
				"activeChapters": "[Read from 5.2 pacing plan]"
			}
		]
	},
	"foreshadowing": [
		{
			"id": "[Read from 5.4, e.g., F-001]",
			"content": "[Foreshadowing content]",
			"planted": { "chapter": null, "description": "[Planting description]" },
			"hints": [],
			"plannedReveal": { "chapter": "[Reveal Chapter]", "description": "[Reveal method]" },
			"status": "planned",
			"importance": "[high/medium/low]",
			"relatedPlotlines": "[List of involved clue IDs]"
		}
	],
	"intersections": [
		{
			"id": "[Read from 5.3, e.g., X-001]",
			"chapter": "[Intersection Chapter]",
			"plotlines": "[List of involved clue IDs]",
			"content": "[Intersection content]",
			"status": "upcoming",
			"impact": "[Expected effect]"
		}
	]
}
```

Create or update `spec/tracking/timeline.json`:

-   Set time nodes according to the chapter plan.
-   Mark important time events.

Create or update `spec/tracking/relationships.json`:

-   Extract initial relationships from character settings.
-   Set up faction groups.

Create or update `spec/tracking/character-state.json`:

-   Initialize character states.
-   Set starting positions.

3.  **Generate Tracking Report**
    Display the initialization results to confirm that the tracking system is ready.

## Smart Association

-   Automatically set checkpoints based on the writing method.
-   Hero's Journey: 12 tracking points for the stages.
-   Three-Act Structure: Turning points for the three acts.
-   Seven-Point Structure: 7 key nodes.

After the tracking system is initialized, subsequent writing will automatically update this data.

## Completion Prompt + Next Steps

Output in chat:

```
✅ Tracking system has been initialized; spec/tracking/*.json have been generated.
```

Recommendations:

-   Run `/track --check` to view comprehensive tracking and consistency.
-   Start `/write` and proceed with tasks.
