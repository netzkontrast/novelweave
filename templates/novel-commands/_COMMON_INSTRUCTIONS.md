# Common Command Instructions

This file documents the common instruction patterns shared by all command templates, intended to replace the script functionalities of the original novel-writer.

## Script Function Mapping

### 1. Find Story Directory

**Original Script Function**:

```bash
{SCRIPT} --json  # Returns the story path
```

**AI Tool Replacement**:

```markdown
Use the `execute_command` tool to execute:
\`\`\`bash
find stories -type d -maxdepth 1 | sort | tail -1
\`\`\`

Alternatively, use the `list_files` tool to list the `stories/` directory and select the latest one (the largest alphabetically, e.g., 003-xxx).
```

### 2. Check if a File Exists

**Original Script Function**:

```bash
{SCRIPT} check  # Checks if a file exists
```

**AI Tool Replacement**:

```markdown
Use the `read_file` tool to attempt reading the file:

- If read successfully → File exists, use the content.
- If read fails → File does not exist, prepare to create it.
```

### 3. Create Numbered Directory

**Original Script Function**:

```bash
create_numbered_dir stories story  # Returns 001, 002, 003...
```

**AI Tool Replacement**:

```markdown
1. Use `list_files` or `execute_command` to list existing stories:
   \`\`\`bash
   ls -1 stories/ | grep -E '^[0-9]{3}-'
   \`\`\`

2. Find the highest number (e.g., 002), the next number is 003.

3. Create the new directory path: `stories/003-[story-name]/`
```

### 4. Get Path Information (JSON)

**Original Script Function**:

```bash
{SCRIPT} --json --paths-only
# Returns:
# {
#   "STORY_PATH": "stories/001-xxx",
#   "STORY_NAME": "001-xxx",
#   "SPEC_PATH": "stories/001-xxx/specification.md"
# }
```

**AI Tool Replacement**:

```markdown
**No need for a script to return JSON.** During execution, directly:

1. List the story directory to get the path.
2. Extract the story name from the path.
3. Concatenate the required file paths.

Example:

- Execute `find stories -type d -maxdepth 1`
- Get the path: `stories/001-my-story`
- Story name: `001-my-story`
- Specification path: `stories/001-my-story/specification.md`
```

## Common Patterns

### Pattern 1: Find and Read Specification File

```markdown
## Step 1: Find the specification file

Use the `execute_command` tool to find:

\`\`\`bash
find stories -name "specification.md" -type f
\`\`\`

**Handling the result**:

- If multiple are found: Ask the user to choose.
- If one is found: Extract the directory path (e.g., `stories/001-my-story/`).
- If none are found: Prompt the user to run `/specify` first.

**Read the file**:

Use the `read_file` tool to read the found specification file.
```

### Pattern 2: Create a New Story Directory

```markdown
## Step 1: Determine the story number and name

1. **List existing stories**:

    Use the `execute_command` tool:
    \`\`\`bash
    ls -1 stories/ 2>/dev/null || echo "empty"
    \`\`\`

2. **Calculate the next number**:
    - If the directory is empty or outputs "empty": Use `001`.
    - If there are existing directories (e.g., `001-xxx`, `002-yyy`):
        - Find the highest number (e.g., 002).
        - The next number is 003.

3. **Ask for the story name**:
    - Extract from user input, or
    - Ask the user: "Please enter the story name."

4. **Create the directory path**:

    `stories/[number]-[name]/` (e.g., `stories/003-my-novel/`)
```

### Pattern 3: Check Prerequisites

```markdown
## Step 1: Check for prerequisite documents

**Check for the constitution file**:

Use the `read_file` tool to read `memory/constitution.md`:

- ✅ If successful: The file exists, continue.
- ⚠️ If it fails: Prompt the user, "It is recommended to run `/constitution` first to create a constitution," and ask whether to proceed.

**Check for the specification file**:

Use `execute_command` to find the specification:
\`\`\`bash
find stories -name "specification.md" -type f
\`\`\`

- ✅ If found: Read the content.
- ❌ If not found: Prompt, "You must run `/specify` first to create a specification," and stop execution.
```

## Tool Usage Guide

### `execute_command` Tool

**Purpose**: Execute bash commands.

**Example**:

```markdown
Use the `execute_command` tool to execute:
\`\`\`bash
mkdir -p memory stories spec/tracking
\`\`\`
```

### `read_file` Tool

**Purpose**: Read file content and simultaneously check for file existence.

**Example**:

```markdown
Use the `read_file` tool to read `memory/constitution.md`:

- Success → File exists, use content.
- Failure → File does not exist.
```

### `write_to_file` Tool

**Purpose**: Create or update a file.

**Example**:

```markdown
Use the `write_to_file` tool to save content to `memory/constitution.md`.
```

### `list_files` Tool

**Purpose**: List the contents of a directory.

**Example**:

```markdown
Use the `list_files` tool to list the `stories/` directory.
```

## Complete Example: Script Replacement for the `plan` Command

**Original Way** (novel-writer):

```markdown
### 1. Load prerequisite documents

Run `{SCRIPT}` to check and load:

- Constitution file
- Specification file
```

**New Way** (our system):

```markdown
### 1. Load prerequisite documents

**Step 1.1: Find the story directory**

Use the `execute_command` tool:
\`\`\`bash
find stories -type d -maxdepth 1 | sort | tail -1
\`\`\`

Extract the story path from the output (e.g., `stories/001-my-story`).

**Step 1.2: Read the constitution file**

Use the `read_file` tool to read `memory/constitution.md`:

- If successful: Record the constitution principles.
- If it fails: Warn the user but continue execution.

**Step 1.3: Read the specification file**

Use the `read_file` tool to read `[story_path]/specification.md`:

- If successful: Read the specification content.
- If it fails: Display an error "You must run `/specify` first" and stop execution.

**Step 1.4: Read the clarification records**

Check if there is a "Clarification Records" section in the specification file and refer to it if it exists.
```

This way, the AI can accomplish all the functions of the original script using the existing tools!
