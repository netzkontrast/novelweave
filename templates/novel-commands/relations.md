---
description: "Manage and track changes in character relationships."
argument-hint: "[update | show | history | check]"
---

# Character Relationship Management

Track and manage the dynamics of relationships between characters to ensure their development is logical.

User Input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Features

1.  **Relationship Network** - Maintain a graph of relationships between characters.
2.  **Relationship Changes** - Record the evolution of relationships over time.
3.  **Faction Management** - Track the opposition and cooperation of various factions.
4.  **Emotional Tracking** - Manage the emotional development between characters.

## Usage

The following operations are supported:

-   `update` - Update character relationships.
-   `show` - Display the relationship network.
-   `history` - View the history of relationship changes.
-   `check` - Validate relationship logic.

## Execution Steps

### 1. Load Relationship Data

Use the `read_file` tool to read `spec/tracking/relationships.json`:

-   If the file exists, load the existing relationship data.
-   If the file does not exist, recommend running `/track-init` first to initialize the tracking system.

### 2. Logic Check (`--check`)

When performing a check, output a checklist according to the following rules:

1.  **File and Structure**

    -   CHK001 `relationships.json` exists and is in valid JSON format.
    -   CHK002 The character dictionary `characters` exists; each character includes a `relationships` key or equivalent categorical keys.
    -   CHK003 Two structures are supported:
        -   Embedded `relationships`: `{ characters: { name: { relationships: { allies:[], enemies:[], romantic:[], family:[], mentors:[], neutral:[] }}}}`
        -   Direct categorical keys: `{ characters: { name: { allies:[], enemies:[], ... }}}`

2.  **Character Reference Validity**

    -   CHK004 All referenced character names have a corresponding node in `characters` (no "unprofiled characters").
    -   CHK005 Both ends of a relationship are string names, with no null values.

3.  **Change History (Optional Field)**

    -   CHK006 If `history` exists, the format includes `chapter|date|changes`.
    -   CHK007 Otherwise, if `relationshipChanges` exists, the record structure is logical.

4.  **Basic Output**

    -   Display the main relationship categories for the protagonist or the first character (romantic/allies/mentors/enemies/family/neutral).
    -   Display the most recent list of relationships (from `history` or `relationshipChanges`).

If an "unprofiled character reference" is found, recommend completing the character node before continuing to write.

## Data Storage

Relationship data is stored in `spec/tracking/relationships.json`:

```json
{
	"characters": {
		"Protagonist": {
			"Allies": ["Character A", "Character B"],
			"Enemies": ["Character C"],
			"Romantic Interest": ["Character D"],
			"Unknown": ["Character E"]
		}
	},
	"factions": {
		"Reformists": ["Protagonist", "Character A"],
		"Conservatives": ["Character C", "Character F"]
	}
}
```

## Output Example

```
👥 Character Relationship Network
━━━━━━━━━━━━━━━━━━━━
Protagonist: Li Zhongyong
├─ 💕 Romantic Interest: Shen Yuqing
├─ 🤝 Ally: Zhang Juzheng (hidden)
├─ 📚 Mentor: Matteo Ricci
├─ ⚔️ Enemy: Shen Shixing's faction
└─ 👁️ Under Surveillance by: Eastern Depot

Faction Opposition:
Reformists ←→ Conservatives
Donglin Party ←→ Eunuch Faction

Recent Changes (Chapter 60):
- Shen Yuqing: Stranger → Mutual Attraction
- Zhang Juzheng: Unknown → Mentor-Student Relationship
```

## Completion Prompt + Next Steps

Output in chat:

```
✅ Relationship operation complete (show/update/history/check)
```

Recommendations:

-   Run `/track --check` for a comprehensive consistency check (titles/factions/locations).
-   The corresponding chapter needs to reflect the relationship changes → Correct or supplement in `/write`.
