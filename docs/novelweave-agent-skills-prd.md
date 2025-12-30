# NovelWeave Agent Skills Support Function PRD

## 📋 Document Information

| Item                 | Information                              |
| -------------------- | ---------------------------------------- |
| **Document Version** | v2.2.0                                   |
| **Creation Date**    | 2025-10-19                               |
| **Product Name**     | NovelWeave - Agent Skills Support        |
| **Target Version**   | v0.13.0 (Core) + v0.14.0 (Marketplace)   |
| **Owner**            | WordFlow Lab                             |
| **Status**           | 📝 Design Review                         |

## Revision History

| Version | Date       | Author       | Change Description                                         |
| ------- | ---------- | ------------ | ---------------------------------------------------------- |
| v2.2.0  | 2025-10-19 | AI Assistant | **Corrected Core Design**: AI autonomous judgment instead of keyword matching |
| v2.1.0  | 2025-10-19 | AI Assistant | Added Skills marketplace design (similar to MCP marketplace) |
| v2.0.0  | 2025-10-19 | AI Assistant | Rewritten based on NovelWeave's actual architecture        |
| v1.0.0  | 2025-10-19 | Claude       | Initial version (obsolete)                                 |

---

## 📑 Table of Contents

1.  [Executive Summary](#executive-summary)
2.  [Product Background](#product-background)
3.  [Core Concepts](#core-concepts)
4.  [Technical Architecture](#technical-architecture)
5.  [Functional Requirements](#functional-requirements)
6.  [Integration with Existing Systems](#integration-with-existing-systems)
7.  [Implementation Plan](#implementation-plan)
8.  [Testing Strategy](#testing-strategy)
9.  [Risk Assessment](#risk-assessment)
10. [Appendix](#appendix)

---

## 🎯 Implementation Plan Overview

| Aspect                 | Plan Description                                                                 |
| ---------------------- | -------------------------------------------------------------------------------- |
| **Integration Method** | ✅ **Built into NovelWeave project** (`src/templates/skills/`)                   |
| **Release Method**     | Packaged with the VSIX extension, available for users upon installation         |
| **Reference Project**  | Learn from the content and structure of `novel-writer-skills` (Claude Code)    |
| **Tool System**        | Use NovelWeave tool groups: `read`, `edit`, `browser`, `command`, `mcp`          |
| **Version Management** | Synchronized with NovelWeave version (v0.13.0+)                                  |

**Why choose built-in integration over a separate package?**

-   ✅ Better user experience: Out-of-the-box after installing NovelWeave
-   ✅ Simpler maintenance: Unified version management, no need for a separate release process
-   ✅ Tighter integration: Deep integration with Modes, Novel Commands, Knowledge Base
-   ✅ Faster releases: Updates with the extension, no extra dependency management

---

## Skills System Comparison Analysis

Before starting the technical design, it's important to understand three different "Skills" systems. Although they share the same name, their implementation mechanisms are completely different.

### Three Different Skills Systems

#### 1. Claude API Skills (Cloud API)

**Reference**: [Claude API Skills Guide](https://docs.claude.com/en/api/skills-guide)

**Core Features**:

-   📤 **Upload to the cloud** - Uploaded via API, stored on Anthropic's servers
-   🐳 **Container execution** - Executes Python code in an isolated Linux container
-   📄 **File generation** - Can create Excel, PowerPoint, PDF, Word documents
-   🔧 **API management** - CRUD operations via REST API
-   🏷️ **Versioning** - Epoch timestamp versions (e.g., `1759178010641129`)

**Usage**:

```python
response = client.messages.create(
    betas=["code-execution-2025-08-25", "skills-2025-10-02"],
    container={
        "skills": [
            {"type": "anthropic", "skill_id": "xlsx", "version": "latest"}
        ]
    },
    tools=[{"type": "code_execution_20250825"}]
)
```

**Applicable Scenarios**: API integration requiring code execution and document generation

---

#### 2. Claude Code Skills (Desktop Application)

**Reference Projects**:

-   [novel-writer-skills](https://github.com/wordflowlab/novel-writer-skills)
-   [superpowers](https://github.com/obra/superpowers)

**Core Features**:

-   💻 **Local files** - Stored in `~/.config/` or project directory
-   🔌 **Plugin system** - Managed via a Plugin mechanism (e.g., `anthropic-agent-skills`)
-   🎣 **Hook injection** - SessionStart Hook injects Skills into the context
-   📝 **Pure prompts** - Does not execute code, only serves as knowledge guidance for the AI
-   📂 **Git version control** - Manages versions using the file system and Git

**Usage**:

```markdown
# .claude/skills/romance-writing/SKILL.md

---

name: romance-novel-conventions
description: "Use when user mentions romance..."
allowed-tools: Read, Grep

---

# Romance Novel Writing Conventions

...
```

**Workflow**:

```
Claude Code starts
  → Loads plugins (Plugin System)
  → SessionStart Hook triggers
  → Scans local Skills files
  → Injects into conversation context
  → Sends to Anthropic API
```

**Applicable Scenarios**: Desktop applications requiring flexible local Skills management

---

#### 3. NovelWeave Skills (Our Implementation)

**Design Philosophy**: Learn from Claude Code, but adapt to the VSCode extension architecture

**Core Features**:

-   📦 **Built-in + Extensible** - Core Skills are built-in, with support for project/personal extensions
-   🚀 **Extension startup loading** - SkillsManager is initialized when the extension is activated
-   💉 **System Prompt injection** - Modifies the `SYSTEM_PROMPT()` function to inject Skills
-   🎨 **WebView UI** - React components for management and visualization
-   🌍 **Multi-Provider** - Supports all AI providers (OpenAI, Anthropic, Gemini, etc.)

**Usage**:

```typescript
// Built-in Skills: src/templates/skills/
// Project Skills: .agent/skills/
// Personal Skills: globalStorage/skills/

const systemPrompt = SYSTEM_PROMPT({
	// ...other parameters
	skillsManager: SkillsManager.getInstance(),
})
```

**Workflow**:

```
VSCode starts NovelWeave
  → extension.ts activates
  → SkillsManager.initialize()
  → Scans Skills from 3 locations
  → User starts a conversation
  → SYSTEM_PROMPT() injects relevant Skills
  → Sends to AI Provider (any)
```

**Applicable Scenarios**: VSCode extensions requiring deep integration with multiple providers and existing features

---

### Detailed Technical Comparison

| Dimension              | Claude API Skills          | Claude Code Skills   | NovelWeave Skills    |
| ---------------------- | -------------------------- | -------------------- | -------------------- |
| **Execution Environment** | ☁️ Cloud isolated container | 💻 Desktop application | 💻 VSCode extension  |
| **Skills Storage**     | Uploaded to Anthropic servers | Local file system    | Local file system    |
| **Loading Time**       | Referenced at API call time | SessionStart Hook    | Extension activation |
| **Injection Location** | `container` parameter      | Conversation context | System Prompt        |
| **Execution Capability** | ✅ Executes Python code    | ❌ Prompts only      | ❌ Prompts only      |
| **File Generation**    | ✅ xlsx, pptx, pdf, docx   | ❌ Not supported     | ❌ Not supported     |
| **Management Method**  | REST API                   | Plugin System        | Built-in feature + files |
| **Version Control**    | Epoch timestamps           | Git                  | Git                  |
| **Extensibility**      | Upload custom skills       | Install plugins      | Add files to file system |
| **AI Provider**        | Anthropic only             | Anthropic only       | ✅ All supported     |
| **UI Interface**       | ❌ API only                | Command line         | ✅ WebView (React)   |
| **Network Requirement**| Skills need to be uploaded | Context needs to be sent | Context needs to be sent |
| **Applicable Scenarios** | API integration, code execution | Desktop writing application | VSCode development environment |

---

### NovelWeave's Design Choices

#### Why not use a plugin system?

Although Claude Code uses a plugin system to manage Skills, NovelWeave opts for a built-in approach for the following reasons:

**1. NovelWeave itself is a "plugin"**

```
VSCode (Platform)
  └── NovelWeave (Extension/Plugin)
      └── Skills (Functional Module)
```

-   NovelWeave is a VSCode extension; adding another layer of plugins would increase complexity
-   Users already install NovelWeave through the VSCode Marketplace

**2. Architectural Consistency**

-   NovelWeave's other features (Modes, Commands, Knowledge Base) are all built-in
-   Maintaining a unified architectural pattern reduces the learning curve

**3. Simplified Maintenance**

-   Single codebase, unified version management
-   Reduces plugin compatibility issues
-   Simplifies the release process

#### How to maintain flexibility?

Although not using a plugin system, NovelWeave still provides a three-tiered Skills architecture:

```
1. Built-in Skills (src/templates/skills/)
   ↓ Highest priority, comes with the extension

2. Project Skills (.agent/skills/)
   ↓ Can override built-in, shared by the team

3. Personal Skills (globalStorage/skills/)
   ↓ User's private configuration
```

**Extension Methods**:

-   ✅ Users can add `.agent/skills/` to their projects (shared via Git)
-   ✅ Users can add personal Skills globally
-   ✅ **Recommended Solution: Skills Marketplace** (similar to the MCP Marketplace):
    -   📦 **Community Skills Repository**: `https://github.com/wordflowlab/novelweave-skills-registry`
    -   🔍 **Marketplace Discovery**: Browse and search for Skills in the WebView UI
    -   ⚡ **One-Click Installation**: Install to globalStorage or the project with a single click
    -   🔄 **Automatic Updates**: Check for and update installed Skills
    -   ⭐ **Community Contributions**: Submit new Skills to the registry via PR

**Skills Marketplace Architecture** (inspired by the MCP Marketplace):

```json
// novelweave-skills-registry/registry.json
{
	"skills": [
		{
			"id": "romance-writing",
			"name": "Romance Novel Conventions",
			"description": "Best practices for romance writing",
			"author": "WordFlow Lab",
			"version": "1.0.0",
			"repository": "https://github.com/wordflowlab/skill-romance-writing",
			"categories": ["writing", "genre-knowledge"],
			"keywords": ["romance", "love", "relationship", "romance"],
			"downloads": 1523,
			"rating": 4.8,
			"verified": true
		},
		{
			"id": "fantasy-worldbuilding",
			"name": "Fantasy World Building",
			"description": "Create consistent fantasy worlds",
			"author": "Community",
			"version": "2.1.0",
			"repository": "https://github.com/user/skill-fantasy-worldbuilding",
			"categories": ["writing", "genre-knowledge"],
			"keywords": ["fantasy", "magic", "worldbuilding", "fantasy"],
			"downloads": 892,
			"rating": 4.6,
			"verified": false
		}
	]
}
```

**User Workflow**:

```typescript
// 1. Browse the marketplace
WebView: Skills panel → "Browse Marketplace" button

// 2. Search for Skills
User input: "romance"
Displays: romance-writing, romance-editing, ...

// 3. View details
Click on a Skill → Displays detailed information, examples, ratings, comments

// 4. Install a Skill
Click "Install" → Choose location (Project / Personal)
System: git clone to the corresponding directory → Automatically refresh Skills

// 5. Update a Skill
Update detected → Prompt user → One-click update (git pull)
```

**Comparison with Other Solutions**:

| Solution                | Advantages                                       | Disadvantages                               | Applicable Scenarios         |
| ----------------------- | ------------------------------------------------ | ------------------------------------------- | ---------------------------- |
| **Skills Marketplace**  | ⭐ Best user experience<br>⭐ Community-driven<br>⭐ Automatic updates | Requires registry maintenance             | ✅ **Recommended as the main solution** |
| VSCode Marketplace      | Official channel<br>High trust                   | Complex release process<br>Slow updates     | Officially curated Skills    |
| npm Package Management  | Familiar to technical users                      | Requires CLI tools<br>Not user-friendly     | Advanced users/developers    |
| Git Repository Cloning  | Most flexible                                    | Completely manual<br>High learning curve   | Experimental Skills        |

**Update Mechanism**:

-   ✅ Built-in Skills are automatically updated with the extension
-   ✅ Marketplace Skills can be set to update automatically or manually
-   ✅ Project Skills are managed by Git (team collaboration)
-   ✅ Personal Skills can be updated selectively (to avoid overwriting customizations)

#### Consistency with Claude Code's Philosophy

Although the implementation mechanisms are different, the core philosophy is the same:

| Philosophy            | Claude Code                | NovelWeave                 |
| --------------------- | -------------------------- | -------------------------- |
| **Local-First**       | ✅ Local files             | ✅ Local files             |
| **Prompt Guidance**   | ✅ No code execution       | ✅ No code execution       |
| **Automatic Activation** | ✅ AI judges from context | ✅ AI judges from context |
| **Git-Friendly**      | ✅ File version control    | ✅ File version control    |
| **Extensible**        | ✅ Add plugins             | ✅ Add files               |

#### Why support multiple providers?

This is a key advantage of NovelWeave over Claude Code:

```typescript
// NovelWeave supports all providers
const providers = [
	"Anthropic", // Claude
	"OpenAI", // GPT-4
	"Google", // Gemini
	"Azure OpenAI",
	"Ollama", // Local models
	// ... and more
]

// Skills are injected via the System Prompt, independent of the provider
```

**Technical Implementation**:

-   Skills content is injected into the System Prompt
-   The System Prompt is a common interface for all providers
-   Does not rely on the API features of a specific provider

---

### Recommendations for Using the Three Systems

| Requirement                               | Recommended System         | Reason                                         |
| ----------------------------------------- | -------------------------- | ---------------------------------------------- |
| **API integration, requiring code execution to generate documents** | Claude API Skills          | The only solution that supports code execution |
| **Desktop writing, focusing on Claude**   | Claude Code Skills         | Official desktop application, plugin ecosystem |
| **VSCode development, multi-model support** | NovelWeave Skills          | Deep integration with the development environment |
| **Team collaboration, need to share Skills** | Claude Code / NovelWeave   | Both support Git version control               |
| **Personal use, simple and fast**         | NovelWeave Skills          | Out-of-the-box, no configuration required      |

---

## Executive Summary

### One-Sentence Description

Add **Agent Skills** support to NovelWeave, enabling the AI to automatically discover and use modularized professional knowledge based on user needs, learning from Claude Code's Skills format and adapting it to NovelWeave, with deep integration into the Modes, Provider, and WebView architecture.

### Core Value

1.  ✅ **Automatic Knowledge Activation** - The AI automatically uses relevant Skills based on the conversation content, without manual triggering
2.  ✅ **Built-in and Out-of-the-Box** - Users can use pre-set writing Skills immediately after installing NovelWeave
3.  ✅ **Deep System Integration** - Works in synergy with Modes, Novel Commands, Knowledge Base, and MCP
4.  ✅ **Learning from Best Practices** - Improved based on `novel-writer-skills`, adapted to the NovelWeave ecosystem
5.  ✅ **Team Collaboration-Friendly** - Naturally share project Skills via git (`.agent/skills/`)
6.  ✅ **Multi-Model Support** - Injected via System Prompt, supports all AI providers

### Comparison with Claude Code

**Design Validation Note**: The following comparison is based on in-depth research of Claude Code Skills (especially the [superpowers](https://github.com/obra/superpowers) plugin). Although the implementation mechanisms are different, NovelWeave's design is **highly consistent and validated** in its core philosophy with Claude Code.

Based on in-depth research of the Claude Code Skills mechanism (see the "Skills System Comparison Analysis" section), we found that the two are highly consistent in their core philosophy:

| Feature                    | Claude Code                   | NovelWeave                                |
| -------------------------- | ----------------------------- | ----------------------------------------- |
| **Core Mechanism**         | ✅ Local files + Prompt injection | ✅ Local files + System Prompt injection  |
| **Skills Format**          | ✅ SKILL.md (YAML + Markdown) | ✅ Fully compatible (with added NovelWeave extension fields) |
| **Automatic Activation**   | ✅ AI judges from context     | ✅ AI judges from context (same mechanism) |
| **Code Execution**         | ❌ No execution, only guidance | ❌ No execution, only guidance             |
| **Version Control**        | ✅ Git                        | ✅ Git                                    |
| **Management Method**      | 🔌 Plugin system (flexible)   | 📦 Built-in feature (simple)              |
| **AI Provider**            | Anthropic only                | ✅ All supported (OpenAI/Gemini/Ollama, etc.) |
| **UI Interface**           | Command line                  | ✅ WebView (React)                        |
| **Extensibility**          | Install plugins               | ✅ Skills Marketplace (like MCP Marketplace) + Three-tiered architecture |
| **Integration with Other Features** | Independent system    | ✅ Deep integration with Modes, Commands, MCP |
| **Team Collaboration**     | ✅ Share via Git              | ✅ Share `.agent/skills/` via Git         |
| **Applicable Scenarios**   | Desktop writing application   | VSCode development environment            |

**Similarities**:

-   ✅ Both use local file storage
-   ✅ Both guide the AI through prompts (no code execution)
-   ✅ Both support Git version control and team sharing
-   ✅ Both support project-level and personal-level Skills

**Differences**:

-   🔄 Management method: Claude Code uses a plugin system, NovelWeave uses a built-in feature
-   🌍 Multi-model: NovelWeave supports all AI Providers
-   🎨 UI: NovelWeave provides a visual management interface
-   🔗 Integration: NovelWeave is deeply integrated with Modes and Commands

**Design Trade-off Analysis**:

| Consideration          | Plugin System (Claude Code) | Built-in Feature + Skills Marketplace (NovelWeave) | Reason for Choice                                  |
| ---------------------- | --------------------------- | -------------------------------------------------- | -------------------------------------------------- |
| **Extension Flexibility** | ⭐⭐⭐⭐⭐ Can publish plugins independently | ⭐⭐⭐⭐⭐ Skills Marketplace + Three-tiered architecture | NovelWeave achieves equivalent flexibility through the marketplace mechanism |
| **User Experience**    | ⭐⭐⭐ Requires installing plugins | ⭐⭐⭐⭐⭐ Built-in + One-click install from marketplace | Out-of-the-box, one-click installation from the marketplace |
| **Maintenance Cost**   | ⭐⭐ Requires maintaining a plugin ecosystem | ⭐⭐⭐⭐ Just need to maintain the registry | Only need to maintain `registry.json`, no code review required |
| **Architectural Consistency** | ⭐⭐⭐ Independent subsystem | ⭐⭐⭐⭐⭐ Consistent with other features | NovelWeave's Modes and Commands are all built-in |
| **Community Contribution** | ⭐⭐⭐⭐⭐ Plugin marketplace distribution | ⭐⭐⭐⭐⭐ Skills Marketplace (like MCP) | The Skills Marketplace offers equivalent capabilities, but is more lightweight |
| **Security**           | ⭐⭐⭐ Plugins can execute code | ⭐⭐⭐⭐⭐ Skills are just text files | Skills do not execute code, making them safer |

**Conclusion**: For a VSCode extension like NovelWeave, a **built-in feature + Skills Marketplace is the optimal choice** because:

1.  ✅ VSCode itself is a plugin system, and NovelWeave is an extension within it
2.  ✅ Avoids the architectural complexity of "plugins within plugins"
3.  ✅ Maintains consistency with existing features (Modes, Commands)
4.  ✅ The out-of-the-box user experience is better
5.  ✅ The **Skills Marketplace provides the same extension capabilities as a plugin system**
6.  ✅ Skills are just text files, which are safer than plugins
7.  ✅ The community can easily contribute (via PRs to the registry) without a release process

---

## Product Background

### Problem Statement

#### Current Pain Points

1.  **Scattered Knowledge**
    *   Writing techniques are scattered across various documents
    *   Context needs to be manually provided in conversations
    *   Domain-specific expertise cannot be automatically applied

2.  **Repetitive Work**
    *   The same instructions have to be repeated in every conversation
    *   Existing workflows cannot be reused
    *   Team members individually maintain similar prompts

3.  **Limitations of Existing Solutions**
    *   **Agent Rules (`.agent-rules`)**: Static text, lacks structure
    *   **Custom Modes**: Require manual switching, cannot be automatically combined
    *   **Custom Instructions**: Global, cannot be activated on-demand

#### Why choose Agent Skills?

Claude Code's Agent Skills is currently the most mature model-invoked knowledge system in the industry:

1.  **Model-Invoked vs. User-Invoked**
    *   Skills are used when the **AI autonomously decides** to
    *   Slash commands are **explicitly triggered by the user**
    *   This design makes the AI smarter and more proactive

2.  **Progressive Disclosure**
    *   Only the frontmatter (metadata) is read during scanning
    *   The full content is only loaded when the AI chooses to use it
    *   This optimizes performance and does not waste context

3.  **Mature Ecosystem**
    *   Community Skills are already available
    *   The standardized format is easy to share
    *   Officially supported by Anthropic with documentation

### Strategic Significance

1.  **Technical Leadership**
    *   Become the first multi-model IDE extension to support Agent Skills
    *   More advanced knowledge management than Cursor or Copilot

2.  **Novel Writing Optimization**
    *   Pre-set novel genre Skills (fantasy, romance, mystery, etc.)
    *   Writing technique Skills (dialogue, pacing, character development)
    *   Quality check Skills (consistency, timeline)

3.  **Community Ecosystem**
    *   Writers can share their creative experiences
    *   Editors can publish review standards
    *   Publishers can provide style guides

---

## Core Concepts

### What are Agent Skills?

**Agent Skills** are modular directories containing professional knowledge that the AI automatically discovers and uses based on the conversation content.

#### Basic Structure

```
my-skill/
├── SKILL.md          # Required: Skill description and instructions
├── reference.md      # Optional: Detailed reference document
├── examples.md       # Optional: Usage examples
├── scripts/          # Optional: Helper scripts
│   └── helper.py
└── templates/        # Optional: Template files
    └── template.txt
```

#### SKILL.md Format

```yaml
---
name: Fantasy World Building
description: Guide for creating consistent fantasy worlds with magic systems, geography, and cultures. Use when discussing fantasy novels, world-building, or magic systems.
allowed_tool_groups: [read, edit]  # Optional: Restrict tool group permissions (NovelWeave)
version: 1.0.0                     # Optional: Version number
---

# Fantasy World Building

## Instructions

1. Start with the magic system's rules and limitations
2. Design geography based on magical elements
3. Develop cultures influenced by magic availability
4. Create conflicts driven by magical disparities

## Key Principles

- Magic should have costs and limitations
- Geography should make logical sense
- Cultures should feel distinct yet connected
- Conflicts should arise naturally from the world's rules

## Examples

See [examples.md](examples.md) for detailed world-building examples.
```

### How It Works

**Core Philosophy**: ✅ **AI Autonomous Judgment** - The AI decides which Skill to use and when, based on the context, rather than mechanical keyword matching.

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Scanning Phase (at startup)                              │
│    SkillsManager scans three locations:                     │
│    - src/templates/skills/     (Built-in Skills)            │
│    - .agent/skills/            (Project Skills)             │
│    - globalStorage/skills/     (Personal Skills)            │
│                                                             │
│    Only reads the frontmatter (name, description, keywords, etc.) │
│    ⚡ Performance optimization: Full content is not loaded, scanning 100 Skills < 50ms │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ 2. Injection Phase (when generating System Prompt)          │
│    Injects a summary of the Skills into the System Prompt:  │
│                                                             │
│    ## Available Agent Skills                                │
│                                                             │
│    - **Fantasy World Building**: Guide for creating          │
│      consistent fantasy worlds. Use when discussing magic,   │
│      world-building, or fantasy novels.                       │
│                                                             │
│    - **Romance Writing**: Best practices for writing          │
│      romantic scenes. Use when writing love stories or        │
│      developing romantic relationships.                       │
│                                                             │
│    **How to use**: When a skill is relevant, announce:       │
│    [USING SKILL: skill-name]                                  │
│                                                             │
│    🎯 The AI autonomously decides which Skill to use based on the conversation content and description │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ 3. Activation Phase (AI autonomous decision)                │
│                                                             │
│    User: "Help me design a magic system"                      │
│    AI determines: Fantasy World Building Skill is relevant    │
│    AI responds: "[USING SKILL: Fantasy World Building]       │
│             Let me help you design a magic system..."        │
│                                                             │
│    System detects the declaration → Loads the full content of SKILL.md │
│    The AI works according to the detailed guidance of the Skill │
│                                                             │
│    ⚠️ No keyword matching! The AI understands the semantics and makes its own judgment │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│ 4. Continuous Use (in conversation)                         │
│    Activated Skills remain in the context                   │
│    The AI can combine multiple Skills                       │
│    The AI can autonomously deactivate Skills that are no longer needed │
│    The user can view the activated Skills in the UI         │
└─────────────────────────────────────────────────────────────┘
```

**Comparison with Keyword Matching**:

| Aspect               | ❌ Keyword Matching (Incorrect)                         | ✅ AI Autonomous Judgment (Correct)              |
| -------------------- | ----------------------------------------------------- | ------------------------------------------------ |
| **Trigger Method**   | "romance" keyword detected → automatic activation     | AI understands semantics → autonomously decides to use |
| **Flexibility**      | Fixed rules, cannot understand synonyms               | Understands variations like "love story," "romantic arc" |
| **Accuracy**         | May be triggered incorrectly ("romantic comedy" ≠ romance novel) | AI understands the specific scenario, makes accurate judgments |
| **Combination Ability** | Difficult to handle combinations of multiple Skills | AI can intelligently combine multiple relevant Skills |
| **User Experience**  | Mechanical, not intelligent                           | Natural, intelligent, in line with the "Agent" concept |

**The True Purpose of Keywords**:

The `keywords` field is **not** for mechanical matching, but to:

1.  Help the AI understand the applicable scenarios of the Skill (as a supplement to the `description`)
2.  Help users search and filter Skills (in the UI)
3.  Be used for Skill classification and recommendations in the marketplace

### Three Types of Skills

#### 1. Personal Skills

**Location**: `~/.claude/skills/` (using NovelWeave's `globalStorageUri`)

**Purpose**:

-   Personal writing style and preferences
-   Experimental new Skills
-   Private workflows

**Example**:

```
~/.claude/skills/
├── my-writing-style/
│   └── SKILL.md
├── my-research-workflow/
│   └── SKILL.md
└── my-editing-checklist/
    └── SKILL.md
```

#### 2. Project Skills

**Location**: `.agent/skills/` (following NovelWeave's `.agent/` specification)

**Purpose**:

-   Team-shared workflows
-   Project-specific writing guidelines
-   Setting knowledge base for a novel series

**Example**:

```
my-novel-project/
└── .agent/
    └── skills/
        ├── series-continuity/
        │   ├── SKILL.md
        │   ├── characters.md
        │   └── timeline.md
        ├── team-style-guide/
        │   └── SKILL.md
        └── world-bible/
            ├── SKILL.md
            ├── magic-system.md
            └── geography.md
```

#### 3. Built-in Skills

**Location**: `src/templates/skills/` (integrated into the NovelWeave project)

**Purpose**:

-   NovelWeave's official pre-set Skills
-   Released with the extension, available for users upon installation
-   Managed as part of the project, unified versioning

**Note**:

-   **Cannot directly use `novel-writer-skills`** - it is designed for Claude Code
-   **We should learn from its structure** - refer to its Skills content and organization
-   **Integrate into the NovelWeave project** - as built-in Skills, not a separate package

**Directory Structure** (integrated into the project):

```
src/
└── templates/
    └── skills/                   # Built-in Skills directory
        ├── genre-knowledge/
        │   ├── romance/
        │   │   └── SKILL.md      # Reference novel-writer-skills/romance
        │   ├── mystery/
        │   │   └── SKILL.md      # Reference novel-writer-skills/mystery
        │   └── fantasy/
        │       └── SKILL.md      # Reference novel-writer-skills/fantasy
        │
        ├── quality-assurance/
        │   ├── consistency-checker/
        │   │   └── SKILL.md      # Reference and improve
        │   ├── workflow-guide/
        │   │   └── SKILL.md      # Adapt to NovelWeave workflow
        │   └── requirement-detector/
        │       └── SKILL.md
        │
        └── writing-techniques/
            ├── dialogue-techniques/
            │   └── SKILL.md      # Reference and optimize
            └── scene-structure/
                └── SKILL.md
```

**Packaging and Release**:

-   Skills files are included in the VSIX package
-   Automatically available to users when the extension is installed
-   Updates are synchronized with the extension version

**Development Plan**:

1.  Study the Skills content of `novel-writer-skills`
2.  Extract common writing knowledge and techniques
3.  Rewrite for NovelWeave adaptation
4.  Place in the `src/templates/skills/` directory

---

## Technical Architecture

### System Architecture Diagram

```
┌───────────────────────────────────────────────────────────────┐
│                    NovelWeave Extension                        │
├───────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │                   ClineProvider                          │  │
│  │  - Manages AI conversations                             │  │
│  │  - Coordinates all services                             │  │
│  └────────────────────┬────────────────────────────────────┘  │
│                       │                                        │
│  ┌────────────────────▼────────────────────────────────────┐  │
│  │              SkillsManager (New)                         │  │
│  │  - scanSkills(): Scans all Skills                       │  │
│  │  - getSkillsForMode(mode): Gets Skills for a specific Mode │  │
│  │  - activateSkill(skillId): Activates a Skill            │  │
│  │  - getSkillsSummary(): Gets a list of Skills for the System Prompt │
│  └────────┬─────────────────────────┬─────────────────────┘  │
│           │                         │                         │
│  ┌────────▼─────────┐    ┌─────────▼────────┐               │
│  │ System Prompt    │    │  WebView UI       │               │
│  │ Generator        │    │  (React)          │               │
│  │                  │    │                   │               │
│  │ SYSTEM_PROMPT()  │    │ SkillsPanel.tsx   │               │
│  │ + Skills Section │    │ SkillsStatus.tsx  │               │
│  └──────────────────┘    └───────────────────┘               │
│                                                                │
└────────────────────────────────────────────────────────────────┘
                              │
              ┌───────────────┴───────────────┐
              │                               │
    ┌─────────▼─────────┐         ┌─────────▼─────────┐
    │  File System Layer  │         │   WebView Communication │
    ├───────────────────┤         ├───────────────────┤
    │ dist/templates/   │         │ postMessage       │
    │   skills/         │         │ messageHandler    │
    │ .agent/skills/    │         │                   │
    │ globalStorage/    │         │                   │
    │   skills/         │         │                   │
    └───────────────────┘         └───────────────────┘
```

### Core Module Design

#### 1. SkillsManager (Core Service)

**Location**: `src/services/skills/SkillsManager.ts`

```typescript
import * as vscode from "vscode"
import * as path from "path"
import * as fs from "fs/promises"
import matter from "gray-matter"
import { McpHub } from "../mcp/McpHub"

/**
 * Skill interface definition
 */
export interface Skill {
	id: string
	name: string
	description: string
	path: string
	source: "personal" | "project" | "extension"

	// Optional fields from YAML frontmatter
	allowedToolGroups?: string[] // NovelWeave tool groups: ['read', 'edit', 'browser', 'command', 'mcp']
	keywords?: string[] // Keywords to help the AI understand applicable scenarios (for AI reference, not mechanical matching)
	version?: string
	whenToUse?: string // Detailed explanation of when to use this Skill (for AI judgment)

	// Content loaded on demand
	content?: string
	supportFiles?: string[]

	// Integration fields
	mcpResources?: string[] // Associated MCP resources
	requiredModes?: string[] // Which modes this skill is relevant for
}

/**
 * Skills configuration
 */
export interface SkillsConfig {
	enabled: boolean
	disabledSkills: string[] // Skill IDs to disable
	perModeConfig: Record<
		string,
		{
			enabledSkills?: string[] // Skills enabled for a specific Mode (affects the list in the System Prompt)
		}
	>
}

/**
 * Skills Manager - The core service for managing Agent Skills
 */
export class SkillsManager {
	private static instance: SkillsManager

	private skills: Map<string, Skill> = new Map()
	private activeSkills: Set<string> = new Set()
	private contentCache: Map<string, string> = new Map()

	private constructor(
		private context: vscode.ExtensionContext,
		private mcpHub?: McpHub,
	) {}

	static getInstance(context: vscode.ExtensionContext, mcpHub?: McpHub): SkillsManager {
		if (!SkillsManager.instance) {
			SkillsManager.instance = new SkillsManager(context, mcpHub)
		}
		return SkillsManager.instance
	}

	/**
	 * Initialization - Scans all Skills
	 */
	async initialize(): Promise<void> {
		await this.scanSkills()
	}

	/**
	 * Scans all Skills directories
	 */
	async scanSkills(): Promise<void> {
		this.skills.clear()

		const paths = this.getSkillsPaths()

		for (const [source, basePath] of paths) {
			try {
				await this.scanDirectory(basePath, source)
			} catch (error) {
				console.warn(`Failed to scan skills from ${basePath}:`, error)
			}
		}

		console.log(`Scanned ${this.skills.size} skills`)
	}

	/**
	 * Gets the Skills search paths
	 */
	private getSkillsPaths(): Array<["personal" | "project" | "extension", string]> {
		const paths: Array<["personal" | "project" | "extension", string]> = []

		// 1. Built-in skills (extension built-in, highest priority)
		const builtinPath = path.join(this.context.extensionPath, "dist", "templates", "skills")
		paths.push(["extension", builtinPath])

		// 2. Project skills (project-specific)
		const workspaceFolders = vscode.workspace.workspaceFolders
		if (workspaceFolders && workspaceFolders.length > 0) {
			const projectPath = path.join(workspaceFolders[0].uri.fsPath, ".agent", "skills")
			paths.push(["project", projectPath])
		}

		// 3. Personal skills (global user configuration)
		const personalPath = path.join(this.context.globalStorageUri.fsPath, "skills")
		paths.push(["personal", personalPath])

		return paths
	}

	/**
	 * Scans a directory for Skills
	 */
	private async scanDirectory(basePath: string, source: "personal" | "project" | "extension"): Promise<void> {
		try {
			const entries = await fs.readdir(basePath, { withFileTypes: true })

			for (const entry of entries) {
				if (entry.isDirectory()) {
					const skillPath = path.join(basePath, entry.name)
					const skillFile = path.join(skillPath, "SKILL.md")

					try {
						await fs.access(skillFile)
						const skill = await this.parseSkillFile(skillFile, source)
						this.skills.set(skill.id, skill)
					} catch {
						// No SKILL.md in this directory
					}
				}
			}
		} catch (error) {
			// Directory doesn't exist
		}
	}

	/**
	 * Parses a SKILL.md file
	 */
	private async parseSkillFile(filePath: string, source: "personal" | "project" | "extension"): Promise<Skill> {
		const content = await fs.readFile(filePath, "utf-8")
		const { data: frontmatter, content: markdown } = matter(content)

		// Validate required fields
		if (!frontmatter.name || !frontmatter.description) {
			throw new Error(`Invalid SKILL.md: missing name or description in ${filePath}`)
		}

		const skillDir = path.dirname(filePath)
		const skillId = this.generateSkillId(skillDir, source)

		// Find support files
		const supportFiles = await this.findSupportFiles(skillDir)

		return {
			id: skillId,
			name: frontmatter.name,
			description: frontmatter.description,
			path: skillDir,
			source,
			allowedTools: this.parseAllowedTools(frontmatter["allowed-tools"]),
			version: frontmatter.version,
			whenToUse: frontmatter.when_to_use,
			supportFiles,
			mcpResources: frontmatter.mcp_resources,
			requiredModes: frontmatter.required_modes,
			// Don't load content yet - lazy loading
		}
	}

	/**
	 * Generates a Skill ID
	 */
	private generateSkillId(skillPath: string, source: string): string {
		const dirName = path.basename(skillPath)
		return `${source}:${dirName}`
	}

	/**
	 * Parses the allowed-tools field
	 */
	private parseAllowedTools(value: any): string[] | undefined {
		if (!value) return undefined
		if (typeof value === "string") {
			return value.split(",").map((s) => s.trim())
		}
		if (Array.isArray(value)) {
			return value.map((s) => String(s).trim())
		}
		return undefined
	}

	/**
	 * Finds support files
	 */
	private async findSupportFiles(skillDir: string): Promise<string[]> {
		const files: string[] = []

		try {
			const entries = await fs.readdir(skillDir, { withFileTypes: true, recursive: true })

			for (const entry of entries) {
				if (entry.isFile() && entry.name !== "SKILL.md") {
					const relativePath = path.relative(skillDir, path.join(entry.path, entry.name))
					files.push(relativePath)
				}
			}
		} catch (error) {
			// Ignore errors
		}

		return files
	}

	/**
	 * Gets a summary of Skills (for the System Prompt)
	 */
	getSkillsSummary(mode?: string): Array<{ id: string; name: string; description: string }> {
		const config = this.context.globalState.get<SkillsConfig>("skillsConfig")
		const disabled = new Set(config?.disabledSkills || [])

		return Array.from(this.skills.values())
			.filter((skill) => {
				// Filter out disabled skills
				if (disabled.has(skill.id)) return false

				// Filter by mode if specified
				if (mode && skill.requiredModes && !skill.requiredModes.includes(mode)) {
					return false
				}

				return true
			})
			.map((skill) => ({
				id: skill.id,
				name: skill.name,
				description: skill.description + (skill.whenToUse ? `. Use when: ${skill.whenToUse}` : ""),
			}))
	}

	/**
	 * Loads the full content of a Skill
	 */
	async loadSkillContent(skillId: string): Promise<string> {
		// Check cache first
		if (this.contentCache.has(skillId)) {
			return this.contentCache.get(skillId)!
		}

		const skill = this.skills.get(skillId)
		if (!skill) {
			throw new Error(`Skill not found: ${skillId}`)
		}

		const skillFile = path.join(skill.path, "SKILL.md")
		const content = await fs.readFile(skillFile, "utf-8")
		const { content: markdown } = matter(content)

		// Cache the content
		this.contentCache.set(skillId, markdown)

		return markdown
	}

	/**
	 * Activates a Skill
	 */
	async activateSkill(skillId: string): Promise<void> {
		this.activeSkills.add(skillId)

		// Load MCP resources if specified
		const skill = this.skills.get(skillId)
		if (skill?.mcpResources && this.mcpHub) {
			for (const resourceUri of skill.mcpResources) {
				try {
					await this.mcpHub.fetchResource(resourceUri)
				} catch (error) {
					console.warn(`Failed to load MCP resource ${resourceUri}:`, error)
				}
			}
		}
	}

	/**
	 * Deactivates a Skill
	 */
	deactivateSkill(skillId: string): void {
		this.activeSkills.delete(skillId)
	}

	/**
	 * Gets the active Skills
	 */
	getActiveSkills(): Skill[] {
		return Array.from(this.activeSkills)
			.map((id) => this.skills.get(id))
			.filter((skill): skill is Skill => skill !== undefined)
	}

	/**
	 * Gets all Skills
	 */
	getAllSkills(): Skill[] {
		return Array.from(this.skills.values())
	}

	/**
	 * Gets Skills by source
	 */
	getSkillsBySource(source: "personal" | "project" | "extension"): Skill[] {
		return Array.from(this.skills.values()).filter((skill) => skill.source === source)
	}

	/**
	 * Clears the cache
	 */
	clearCache(): void {
		this.contentCache.clear()
	}
}
```

#### 2. System Prompt Integration

**Location**: `src/core/prompts/system.ts` (modify existing file)

```typescript
// Add Skills support to the SYSTEM_PROMPT function

export const SYSTEM_PROMPT = async (
	context: vscode.ExtensionContext,
	cwd: string,
	supportsComputerUse: boolean,
	mcpHub?: McpHub,
	diffStrategy?: DiffStrategy,
	browserViewportSize?: string,
	inputMode: Mode = defaultModeSlug,
	customModePrompts?: CustomModePrompts,
	customModes?: ModeConfig[],
	globalCustomInstructions?: string,
	diffEnabled?: boolean,
	experiments?: Experiments,
	enableMcpServerCreation?: boolean,
	language?: string,
	rooIgnoreInstructions?: string,
	partialReadsEnabled?: boolean,
	settings?: SystemPromptSettings,
	todoList?: TodoItem[],
	modelId?: string,
	clineProviderState?: ClineProviderState,
	skillsManager?: SkillsManager, // New parameter
): Promise<string> => {
	// ... existing code ...

	const mode = getModeBySlug(inputMode, customModes)?.slug || defaultModeSlug

	// Generate Skills section
	const skillsSection = skillsManager ? await generateSkillsSection(skillsManager, mode) : ""

	return generatePrompt(
		context,
		cwd,
		supportsComputerUse,
		currentMode.slug,
		mcpHub,
		effectiveDiffStrategy,
		browserViewportSize,
		promptComponent,
		customModes,
		globalCustomInstructions,
		diffEnabled,
		experiments,
		enableMcpServerCreation,
		language,
		rooIgnoreInstructions,
		partialReadsEnabled,
		settings,
		todoList,
		modelId,
		clineProviderState,
		skillsSection, // Pass the Skills section
	)
}

/**
 * Generates the Skills prompt section
 */
async function generateSkillsSection(skillsManager: SkillsManager, mode: string): Promise<string> {
	const skills = skillsManager.getSkillsSummary(mode)

	if (skills.length === 0) {
		return ""
	}

	return `

## Available Agent Skills

You have access to specialized knowledge through Agent Skills. These are modular capabilities that you can use when relevant to the user's request.

**Available Skills:**

${skills.map((skill) => `- **${skill.name}**: ${skill.description}`).join("\n")}

**How to Use Skills:**

1. When a skill is relevant to the user's request, announce it:
   \`[USING SKILL: ${skills[0].name}]\`

2. I will then provide you with the full skill content

3. Follow the skill's instructions carefully

4. You can use multiple skills in a single response if needed

5. Skills are discoverable - only use them when they add value

**Important:** Only announce skills that are directly relevant to the current task. Don't use skills just because they're available.
`
}
```

**Location**: `src/core/prompts/sections/skills.ts` (new file)

```typescript
import { SkillsManager } from "../../../services/skills/SkillsManager"

/**
 * Skills prompt section generator
 */
export async function generateSkillsSection(skillsManager: SkillsManager, mode: string): Promise<string> {
	const config = await skillsManager.getConfig()

	if (!config.enabled) {
		return ""
	}

	const skills = skillsManager.getSkillsSummary(mode)

	if (skills.length === 0) {
		return ""
	}

	return `

## Agent Skills

You have access to the following specialized skills. Use them when relevant by announcing: [USING SKILL: skill-name]

${skills.map((s) => `- **${s.name}**: ${s.description}`).join("\n")}
`
}

/**
 * Extracts activated Skills from the AI's response
 */
export function extractActivatedSkills(response: string): string[] {
	const pattern = /\[USING SKILL:\s*([^\]]+)\]/gi
	const matches = Array.from(response.matchAll(pattern))
	return matches.map((m) => m[1].trim())
}
```

<... truncated ...>
