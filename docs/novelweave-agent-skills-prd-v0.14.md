# NovelWeave Agent Skills Support Function PRD v0.14.0

## 📋 Document Information

| Item | Information |
| --- | --- |
| **Document Version** | v0.14.0 ⚠️ **Major Architectural Change** |
| **Creation Date** | 2025-10-20 |
| **Product Name** | NovelWeave - Agent Skills Support |
| **Target Version** | v0.14.0 |
| **Owner** | WordFlow Lab |
| **Status** | 📝 Design Review |
| **Previous Version** | v0.13.0 Design (Deprecated, contains design flaws) |

## Revision History

| Version | Date | Author | Change Description |
| --- | --- | --- | --- |
| v0.14.0 | 2025-10-20 | AI Assistant | **Major Architectural Change**: Adopt project initialization model (learning from Claude Code) |
| v0.13.0 | 2025-10-19 | AI Assistant | Initial Design (Deprecated, contains design flaws) |

---

## 🎯 Why is v0.14.0 Needed?

### Key Flaws in the v0.13.0 Design

The initial design of v0.13.0 had **fatal architectural problems**:

#### Problem 1: Built-in Skills Cannot Be Modified

```
Built-in Skills location: dist/templates/skills/  ← Build artifact
                                         ← Read-only
                                         ← Overwritten with each build
                                         ← User modifications will be lost!
```

**Impact**:

- ❌ Users cannot customize built-in Skills
- ❌ Even if it claims "project Skills override built-in", users have to write a Skill with the same name from scratch
- ❌ Does not meet the "out-of-the-box" promise

#### Problem 2: Three-Tier Priority is Overly Complex

```typescript
// v0.13.0 design
extension:romance  ← Built-in (read-only)
project:romance    ← Project (modifiable)
personal:romance   ← Personal (modifiable)

// Three different Skills! Cannot truly "override"
```

**Impact**:

- ❌ Skill ID includes a source prefix, cannot be overridden
- ❌ The logic is complex and difficult to understand
- ❌ User confusion: Why are there three romance Skills?

#### Problem 3: Does Not Follow Industry Best Practices

**How Cursor / Claude Code Does It**:

```bash
# On first use
.claude/
├── skills/      ← Copied from a template to the project
├── commands/    ← User has full control
└── config.json  ← Version controlled by Git
```

**The v0.13.0 Way** (Wrong):

```bash
# At runtime
dist/templates/skills/  ← Read-only, user cannot modify
.agent/skills/          ← Modifiable, but needs to be written from scratch
```

---

## ✅ Core Changes in v0.14.0

### Design Philosophy: Project Initialization Model

**Learning from Claude Code / Cursor**: Use built-in Skills as **initialization templates**, not runtime dependencies.

```
┌─────────────────────────────────────────────────────────┐
│ 1. First Use                                            │
│    User opens project → Detects it's not initialized    │
│    ↓                                                    │
│    Prompt: "Initialize Agent Skills for the project?"   │
│    ↓                                                    │
│    User confirms → Copy all built-in Skills to .agent/skills/ │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 2. Daily Use                                            │
│    The user can:                                        │
│    - ✅ Directly modify any Skill in .agent/skills/     │
│    - ✅ Delete unnecessary Skills                       │
│    - ✅ Add new Skills                                  │
│    - ✅ Commit to Git, share with the team              │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 3. Team Collaboration                                   │
│    Team members clone the project → .agent/skills/ already exists │
│    ↓                                                    │
│    Use directly, no initialization needed               │
│    ↓                                                    │
│    Modify Skills → Commit → Team syncs                  │
└─────────────────────────────────────────────────────────┘
```

---

## 📊 v0.13.0 vs v0.14.0 Comparison

| Dimension | v0.13.0 (Deprecated) | v0.14.0 (Current) |
| --- | --- | --- |
| **Built-in Skills Location** | `dist/templates/skills/` (read at runtime) | `dist/templates/skills/` (only for initialization) |
| **Built-in Skills Modifiable** | ❌ Read-only, overwritten on build | ✅ Modifiable after initialization to project |
| **Skill ID** | `extension:romance` (includes source) | `romance` (uses only directory name) |
| **Priority** | extension < project < personal (three-tier) | project > personal (two-tier) |
| **Override Mechanism** | ❌ Cannot override (different IDs) | ✅ Same name automatically overrides |
| **User Control** | ⚠️ Partial (built-in read-only, need to write project Skill from scratch) | ✅ Complete (all Skills are in the project) |
| **Team Collaboration** | ⚠️ Feasible but complex (need to understand three-tier logic) | ✅ Simple (share `.agent/skills/` via Git) |
| **First Use** | Immediately available (but cannot be modified) | Requires initialization (one-time, then full control) |
| **Update Mechanism** | ⚠️ Extension updates automatically (overwrites user modifications) | ✅ No automatic updates (user has full control) |
| **Learning Curve** | ⚠️ Medium (need to understand three-tier priority) | ✅ Low (project files, direct modification) |
| **Follows Industry Practice** | ❌ Self-created model | ✅ Learns from Cursor/Claude Code |

---

## 🏗️ Core Architecture Design

### Redefining Skill Sources

| Source | Location | Purpose | Modifiable | Git Managed | Priority |
| --- | --- | --- | --- | --- | --- |
| **Template** | `dist/templates/skills/` | Only for initializing to project | ❌ | ❌ | N/A |
| **Project Skills** | `.agent/skills/` | Main usage, shared with team | ✅ | ✅ | High |
| **Personal Skills** | `globalStorage/skills/` | Personal templates across projects | ✅ | ❌ | Low |

**Key Changes**:

- ✅ Templates are no longer scanned at runtime (only used for initialization)
- ✅ Project Skills become the primary way of use
- ✅ Personal Skills are used for cross-project reuse (e.g., "my writing style")

### Simplified Skill Scanning Logic

```typescript
// v0.14.0: Only scan two locations
async scanSkills(): Promise<void> {
    this.skills.clear()

    const paths = [
        // 1. Personal Skills (global)
        { source: 'personal', path: globalStorage/skills/ },

        // 2. Project Skills (current project)
        { source: 'project', path: .agent/skills/ }
    ]

    // The latter overrides the former (Map.set automatically overrides same key)
    for (const {source, path} of paths) {
        await this.scanDirectory(path, source)
    }
}

// Skill ID generation: use only directory name
generateSkillId(skillPath: string): string {
    return path.basename(skillPath)  // "romance" (without source)
}
```

**Removed Complex Logic**:

- ❌ No longer scan `dist/templates/skills/`
- ❌ No longer handle source prefixes
- ❌ No longer need complex priority judgments

---

## 🚀 Core Function: Skills Initializer

### SkillsInitializer Design

**Location**: `src/services/skills/SkillsInitializer.ts`

```typescript
import * as vscode from "vscode"
import * as path from "path"
import * as fs from "fs/promises"

/**
 * Skills Initializer
 * Responsible for copying built-in Skills templates to the project
 */
export class SkillsInitializer {
	constructor(private context: vscode.ExtensionContext) {}

	/**
	 * Detect if the project has initialized Skills
	 */
	async isInitialized(): Promise<boolean> {
		const workspaceFolders = vscode.workspace.workspaceFolders
		if (!workspaceFolders || workspaceFolders.length === 0) {
			return false
		}

		const skillsPath = path.join(workspaceFolders[0].uri.fsPath, ".agent", "skills")

		try {
			await fs.access(skillsPath)
			return true
		} catch {
			return false
		}
	}

	/**
	 * Initialize Skills to the project
	 * @param force Whether to force overwrite existing Skills
	 */
	async initializeSkills(force: boolean = false): Promise<void> {
		const workspaceFolders = vscode.workspace.workspaceFolders
		if (!workspaceFolders || workspaceFolders.length === 0) {
			throw new Error("No workspace folder open")
		}

		const projectRoot = workspaceFolders[0].uri.fsPath
		const targetPath = path.join(projectRoot, ".agent", "skills")

		// Check if it already exists
		if (!force && (await this.isInitialized())) {
			throw new Error("Skills already initialized. Use force=true to overwrite.")
		}

		// Get the template path
		const templatePath = path.join(this.context.extensionPath, "dist", "templates", "skills")

		// Copy all Skills
		await this.copyDirectory(templatePath, targetPath)

		// Create README
		await this.createReadme(targetPath)
	}

	/**
	 * Recursively copy a directory
	 */
	private async copyDirectory(src: string, dest: string): Promise<void> {
		await fs.mkdir(dest, { recursive: true })

		const entries = await fs.readdir(src, { withFileTypes: true })

		for (const entry of entries) {
			const srcPath = path.join(src, entry.name)
			const destPath = path.join(dest, entry.name)

			if (entry.isDirectory()) {
				await this.copyDirectory(srcPath, destPath)
			} else {
				await fs.copyFile(srcPath, destPath)
			}
		}
	}

	/**
	 * Create a README file
	 */
	private async createReadme(skillsPath: string): Promise<void> {
		const readme = `# NovelWeave Agent Skills

This directory contains your project's Agent Skills.

## Instructions

- ✅ You can freely modify, delete, or add Skills
- ✅ Skills files are tracked by Git and shared with the team
- ✅ Each Skill is a directory containing a SKILL.md file

## Directory Structure

\`\`\`
.agent/skills/
├── genre-knowledge/       # Genre Knowledge
│   ├── romance/
│   ├── mystery/
│   └── fantasy/
├── quality-assurance/     # Quality Assurance
│   ├── consistency-checker/
│   └── novelweave-workflow/
└── writing-techniques/    # Writing Techniques
    ├── dialogue-techniques/
    └── scene-structure/
\`\`\`

## How to Use

1. **View a Skill**: Open the SKILL.md file in any Skill directory
2. **Modify a Skill**: Directly edit SKILL.md or add support files
3. **Delete a Skill**: Delete the entire Skill directory
4. **Add a new Skill**: Create a new directory and add a SKILL.md file

## References

- [Skills Writing Guide](https://docs.novelweave.com/skills-guide)
- [SKILL.md Format Specification](https://docs.novelweave.com/skill-format)

---

_Initialization Time: ${new Date().toISOString()}_
_NovelWeave Version: ${this.context.extension.packageJSON.version}_
`

		await fs.writeFile(path.join(skillsPath, "README.md"), readme, "utf-8")
	}

	/**
	 * Check for new official Skills
	 * Compare Skills in the template and the project
	 */
	async checkForNewSkills(): Promise<string[]> {
		const templatePath = path.join(this.context.extensionPath, "dist", "templates", "skills")

		const projectPath = path.join(vscode.workspace.workspaceFolders![0].uri.fsPath, ".agent", "skills")

		const templateSkills = await this.listSkills(templatePath)
		const projectSkills = await this.listSkills(projectPath)

		// Find Skills that are in the template but not in the project
		return templateSkills.filter((skill) => !projectSkills.includes(skill))
	}

	/**
	 * List all Skills in a directory
	 */
	private async listSkills(basePath: string): Promise<string[]> {
		const skills: string[] = []

		try {
			const categories = await fs.readdir(basePath, { withFileTypes: true })

			for (const category of categories) {
				if (category.isDirectory() && !category.name.startsWith(".")) {
					const categoryPath = path.join(basePath, category.name)
					const skillDirs = await fs.readdir(categoryPath, { withFileTypes: true })

					for (const skillDir of skillDirs) {
						if (skillDir.isDirectory()) {
							skills.push(`${category.name}/${skillDir.name}`)
						}
					}
				}
			}
		} catch (error) {
			// Directory doesn't exist
		}

		return skills
	}

	/**
	 * Add missing Skills
	 */
	async addMissingSkills(skillPaths: string[]): Promise<void> {
		const templateBasePath = path.join(this.context.extensionPath, "dist", "templates", "skills")

		const projectBasePath = path.join(vscode.workspace.workspaceFolders![0].uri.fsPath, ".agent", "skills")

		for (const skillPath of skillPaths) {
			const src = path.join(templateBasePath, skillPath)
			const dest = path.join(projectBasePath, skillPath)

			await this.copyDirectory(src, dest)
		}
	}
}
```

### First Use Flow

```typescript
// src/extension.ts

export async function activate(context: vscode.ExtensionContext) {
	// ... existing initialization ...

	// Initialize SkillsInitializer
	const skillsInitializer = new SkillsInitializer(context)

	// Check if initialized
	const isInitialized = await skillsInitializer.isInitialized()

	if (!isInitialized && vscode.workspace.workspaceFolders) {
		// First use, prompt for initialization
		const action = await vscode.window.showInformationMessage(
			"This appears to be a new project. Initialize Agent Skills?",
			{
				modal: false,
				detail: "This will copy all built-in Skills to .agent/skills/, allowing you to modify them freely.",
			},
			"Initialize",
			"Later",
			"Don't Ask Again",
		)

		if (action === "Initialize") {
			try {
				await vscode.window.withProgress(
					{
						location: vscode.ProgressLocation.Notification,
						title: "Initializing Agent Skills...",
						cancellable: false,
					},
					async (progress) => {
						progress.report({ increment: 0, message: "Copying Skills templates..." })
						await skillsInitializer.initializeSkills()

						progress.report({ increment: 100, message: "Done!" })
					},
				)

				vscode.window
					.showInformationMessage(
						"Agent Skills initialized successfully! You can now modify them in .agent/skills/.",
						"Open Skills Directory",
					)
					.then((selection) => {
						if (selection === "Open Skills Directory") {
							const skillsUri = vscode.Uri.file(
								path.join(vscode.workspace.workspaceFolders![0].uri.fsPath, ".agent", "skills"),
							)
							vscode.commands.executeCommand("revealInExplorer", skillsUri)
						}
					})
			} catch (error) {
				vscode.window.showErrorMessage(`Initialization failed: ${error.message}`)
			}
		} else if (action === "Don't Ask Again") {
			// Record user's choice
			context.globalState.update("skills.dontAskAgain", true)
		}
	}

	// Initialize SkillsManager (only scans project and personal)
	const skillsManager = SkillsManager.getInstance(context)
	await skillsManager.initialize()

	// ... rest of activation ...
}
```

---

## 📋 SkillsManager Simplification

### Simplified Interface

```typescript
export interface Skill {
	id: string // Only use directory name: "romance"
	name: string
	description: string
	path: string
	source: "personal" | "project" // ❌ Remove 'extension'

	// Optional fields
	allowedToolGroups?: string[]
	keywords?: string[]
	version?: string
	whenToUse?: string

	// Content (lazy loaded)
	content?: string
	supportFiles?: string[]

	// Integration
	mcpResources?: string[]
	requiredModes?: string[]
}
```

### Simplified SkillsManager

```typescript
export class SkillsManager {
	private skills: Map<string, Skill> = new Map()

	/**
	 * Scan Skills (only scans project and personal)
	 */
	async scanSkills(): Promise<void> {
		this.skills.clear()

		const paths = this.getSkillsPaths()

		// Scan from low to high priority (latter overrides former)
		for (const [source, basePath] of paths) {
			try {
				await this.scanDirectory(basePath, source)
			} catch (error) {
				console.warn(`Failed to scan skills from ${basePath}:`, error)
			}
		}
	}

	/**
	 * Get Skills paths (only 2)
	 */
	private getSkillsPaths(): Array<["personal" | "project", string]> {
		const paths: Array<["personal" | "project", string]> = []

		// 1. Personal skills (low priority)
		const personalPath = path.join(this.context.globalStorageUri.fsPath, "skills")
		paths.push(["personal", personalPath])

		// 2. Project skills (high priority, overrides personal)
		const workspaceFolders = vscode.workspace.workspaceFolders
		if (workspaceFolders && workspaceFolders.length > 0) {
			const projectPath = path.join(workspaceFolders[0].uri.fsPath, ".agent", "skills")
			paths.push(["project", projectPath])
		}

		return paths
	}

	/**
	 * Generate Skill ID (use only directory name)
	 */
	private generateSkillId(skillPath: string): string {
		return path.basename(skillPath) // "romance" (without source)
	}

	// ... rest of the implementation (same as v0.13.0 design) ...
}
```

**Removed Code**:

- ❌ Logic for scanning extension Skills
- ❌ ID generation of `${source}:${dirName}`
- ❌ Three-tier priority judgment logic

---

## 🎨 User Experience Flow

### Scenario 1: First Use (New Project)

```
1. User installs the NovelWeave extension
   ↓
2. Opens a new project (without .agent/skills/)
   ↓
3. NovelWeave activates → Detects it's not initialized
   ↓
4. Displays a prompt:
   ┌─────────────────────────────────────────────┐
   │ 💡 This appears to be a new project. Initialize Agent Skills? │
   │                                             │
   │ This will copy all built-in Skills to .agent/skills/, │
   │ allowing you to modify them freely.         │
   │                                             │
   │ [Initialize]  [Later]  [Don't Ask Again]     │
   └─────────────────────────────────────────────┘
   ↓
5. User clicks "Initialize" → Progress notification
   ↓
6. Done! Displays:
   ┌─────────────────────────────────────────────┐
   │ ✅ Agent Skills initialized successfully!     │
   │                                             │
   │ You can now modify them in .agent/skills/.  │
   │                                             │
   │ [Open Skills Directory]                       │
   └─────────────────────────────────────────────┘
   ↓
7. Project structure:
   .agent/
   └── skills/
       ├── README.md
       ├── genre-knowledge/
       │   ├── romance/
       │   │   └── SKILL.md
       │   ├── mystery/
       │   │   └── SKILL.md
       │   └── fantasy/
       │       └── SKILL.md
       ├── quality-assurance/
       │   ├── consistency-checker/
       │   │   └── SKILL.md
       │   └── novelweave-workflow/
       │       └── SKILL.md
       └── writing-techniques/
           ├── dialogue-techniques/
           │   └── SKILL.md
           └── scene-structure/
               └── SKILL.md
```

### Scenario 2: Modifying a Skill

```
1. User opens .agent/skills/romance/SKILL.md in VS Code
   ↓
2. Directly edits the file (a normal Markdown file)
   ↓
3. Modifies frontmatter or content
   ---
   name: Romance Novel Conventions
   description: My customized romance writing guide  ← Modified
   keywords: [romance, love, romance novel]
   ---

   # My Romance Novel Writing Guidelines  ← Modified

   ## Core Elements (My Style)  ← Added
   ...
   ↓
4. Saves the file (Cmd+S / Ctrl+S)
   ↓
5. NovelWeave automatically detects file changes
   ↓
6. Rescans Skills
   ↓
7. The next time the AI is used, it uses the modified version ✅
```

### Scenario 3: Team Collaboration

```
[Developer A]
1. Initializes Skills for the project
   ↓
2. Customizes the team's Skills (e.g., modifies the romance Skill)
   ↓
3. Commits to Git
   $ git add .agent/skills/
   $ git commit -m "Custom romance skill for our project"
   $ git push

[Developer B]
1. Clones the project
   $ git clone <repo>
   ↓
2. Opens the project → NovelWeave activates
   ↓
3. Detects that .agent/skills/ already exists → Skips initialization prompt
   ↓
4. Directly uses the team's customized Skills ✅
   ↓
5. If modifications are needed → Directly edit → Commit to Git
```

### Scenario 4: After a NovelWeave Update

```
1. NovelWeave releases a new version (e.g., v0.14.0)
   - Adds a new "sci-fi" Skill
   - Improves the "dialogue-techniques" Skill
   ↓
2. User updates the extension
   ↓
3. Opens the project → NovelWeave activates
   ↓
4. User manually executes: NovelWeave: Check for New Skills
   ↓
5. Displays:
   ┌─────────────────────────────────────────────┐
   │ 💡 Found 1 new official Skill:              │
   │                                             │
   │  - genre-knowledge/sci-fi                   │
   │                                             │
   │ Add to the project?                         │
   │                                             │
   │ [Add]  [Later]  [View Details]              │
   └─────────────────────────────────────────────┘
   ↓
6. User selects "Add" → Copies only the new Skill
   ↓
7. Existing Skills are not affected (user's modifications are preserved) ✅
```

---

## 🛠️ Command Registration

### New Commands

```typescript
// src/activate/registerCommands.ts

export function registerCommands({ context, provider }: { context: vscode.ExtensionContext; provider: ClineProvider }) {
	// ... existing commands ...

	// Skills initialization command
	context.subscriptions.push(
		vscode.commands.registerCommand("novelweave.skills.initialize", async () => {
			const initializer = new SkillsInitializer(context)

			try {
				// Check if already initialized
				const isInitialized = await initializer.isInitialized()

				if (isInitialized) {
					const action = await vscode.window.showWarningMessage(
						"Skills are already initialized. Re-initialize (this will overwrite existing Skills)?",
						{ modal: true },
						"Re-initialize",
						"Cancel",
					)

					if (action !== "Re-initialize") {
						return
					}
				}

				await vscode.window.withProgress(
					{
						location: vscode.ProgressLocation.Notification,
						title: "Initializing Agent Skills...",
						cancellable: false,
					},
					async (progress) => {
						await initializer.initializeSkills(true)
					},
				)

				vscode.window
					.showInformationMessage("Agent Skills initialized successfully!", "Open Skills Directory")
					.then((selection) => {
						if (selection === "Open Skills Directory") {
							const skillsUri = vscode.Uri.file(
								path.join(vscode.workspace.workspaceFolders![0].uri.fsPath, ".agent", "skills"),
							)
							vscode.commands.executeCommand("revealInExplorer", skillsUri)
						}
					})

				// Rescan Skills
				await provider.skillsManager?.scanSkills()
			} catch (error) {
				vscode.window.showErrorMessage(`Initialization failed: ${error.message}`)
			}
		}),
	)

	// Check for new Skills command
	context.subscriptions.push(
		vscode.commands.registerCommand("novelweave.skills.checkNew", async () => {
			const initializer = new SkillsInitializer(context)

			try {
				const newSkills = await initializer.checkForNewSkills()

				if (newSkills.length === 0) {
					vscode.window.showInformationMessage("No new official Skills found.")
					return
				}

				const action = await vscode.window.showInformationMessage(
					`Found ${newSkills.length} new official Skills:\n\n${newSkills.join("\n")}\n\nAdd to the project?`,
					{ modal: true },
					"Add",
					"Cancel",
				)

				if (action === "Add") {
					await vscode.window.withProgress(
						{
							location: vscode.ProgressLocation.Notification,
							title: "Adding new Skills...",
							cancellable: false,
						},
						async (progress) => {
							await initializer.addMissingSkills(newSkills)
						},
					)

					vscode.window.showInformationMessage(`Successfully added ${newSkills.length} new Skills!`)

					// Rescan Skills
					await provider.skillsManager?.scanSkills()
				}
			} catch (error) {
				vscode.window.showErrorMessage(`Check failed: ${error.message}`)
			}
		}),
	)

	// Refresh Skills command (retained)
	context.subscriptions.push(
		vscode.commands.registerCommand("novelweave.skills.refresh", async () => {
			try {
				await provider.skillsManager?.scanSkills()
				vscode.window.showInformationMessage("Skills refreshed successfully")
			} catch (error) {
				vscode.window.showErrorMessage(`Refresh failed: ${error.message}`)
			}
		}),
	)
}
```

### package.json Command Definitions

```json
{
	"contributes": {
		"commands": [
			{
				"command": "novelweave.skills.initialize",
				"title": "NovelWeave: Initialize Agent Skills",
				"category": "NovelWeave"
			},
			{
				"command": "novelweave.skills.checkNew",
				"title": "NovelWeave: Check for New Skills",
				"category": "NovelWeave"
			},
			{
				"command": "novelweave.skills.refresh",
				"title": "NovelWeave: Refresh Skills",
				"category": "NovelWeave"
			}
		]
	}
}
```

---

## 📦 Build Configuration

### esbuild Configuration (Copy Template)

```javascript
// src/esbuild.mjs

const copySkillsPlugin = {
	name: "copy-skills",
	setup(build) {
		build.onEnd(async () => {
			const fs = require("fs-extra")
			const path = require("path")

			const src = path.join(__dirname, "templates/skills")
			const dest = path.join(__dirname, "dist/templates/skills")

			// Copy Skills templates to dist/
			await fs.copy(src, dest, {
				overwrite: true,
				errorOnExist: false,
			})

			console.log("✅ Skills templates copied to dist/")
		})
	},
}

// Add to plugins
export default {
	// ... other config
	plugins: [
		// ... other plugins
		copySkillsPlugin,
	],
}
```

### .gitignore Update

```bash
# .gitignore

# Build artifacts
dist/

# But keep the Skills templates in the source code
!src/templates/skills/
```

### .agent/.gitignore (User's Project)

```bash
# .agent/.gitignore

# Track Skills (team sharing)
!skills/

# But ignore temporary files
skills/**/*.tmp
skills/**/.DS_Store
```

---

## 📋 Implementation Plan

### Phase 1: Core Initialization Functionality (Week 1)

#### Sprint 1.1: SkillsInitializer Implementation (3 days)

**Tasks**:

- [ ] Create `SkillsInitializer` class
- [ ] Implement `isInitialized()` detection logic
- [ ] Implement `initializeSkills()` copy logic
- [ ] Implement `checkForNewSkills()` comparison logic
- [ ] Implement `addMissingSkills()` add logic
- [ ] Unit tests

**Deliverables**:

- ✅ `src/services/skills/SkillsInitializer.ts`
- ✅ `src/services/skills/__tests__/SkillsInitializer.test.ts`

#### Sprint 1.2: Extension Activation Integration (2 days)

**Tasks**:

- [ ] Add first-use initialization prompt in `extension.ts`
- [ ] Implement initialization flow UI
- [ ] Handle user choices (Initialize/Later/Don't Ask Again)
- [ ] Test first-use experience

**Deliverables**:

- ✅ Modified `src/extension.ts`
- ✅ First-use prompt flow

#### Sprint 1.3: SkillsManager Simplification (2 days)

**Tasks**:

- [ ] Remove extension Skills scanning logic
- [ ] Remove source prefix generation
- [ ] Simplify to two-tier priority (personal → project)
- [ ] Update test cases

**Deliverables**:

- ✅ Simplified `src/services/skills/SkillsManager.ts`
- ✅ Updated test cases

### Phase 2: Commands and UI (Week 2)

#### Sprint 2.1: Command Registration (2 days)

**Tasks**:

- [ ] Register `novelweave.skills.initialize` command
- [ ] Register `novelweave.skills.checkNew` command
- [ ] Update `package.json` command definitions
- [ ] Test command execution

**Deliverables**:

- ✅ Modified `src/activate/registerCommands.ts`
- ✅ Modified `src/package.json`

#### Sprint 2.2: WebView UI Update (3 days)

**Tasks**:

- [ ] Remove "Extension Skills" group
- [ ] Only show Project and Personal Skills
- [ ] Add "Initialize Skills" button (if not initialized)
- [ ] Add "Check for New Skills" button

**Deliverables**:

- ✅ Modified `webview-ui/src/components/skills/SkillsPanel.tsx`
- ✅ Updated UI logic

### Phase 3: Built-in Skills Creation (Weeks 2-3)

#### Sprint 3.1: Create Skills Templates (4 days)

**Tasks**:

- [ ] Create `src/templates/skills/` directory structure
- [ ] Rewrite core Skills from `novel-writer-skills`:
    - [ ] genre-knowledge/romance
    - [ ] genre-knowledge/mystery
    - [ ] genre-knowledge/fantasy
    - [ ] quality-assurance/consistency-checker
    - [ ] quality-assurance/novelweave-workflow (new)
    - [ ] writing-techniques/dialogue-techniques
    - [ ] writing-techniques/scene-structure
- [ ] Create Skills README template

**Deliverables**:

- ✅ `src/templates/skills/` directory and all Skills
- ✅ At least 7 core Skills

### Phase 4: Testing and Documentation (Week 3)

#### Sprint 4.1: End-to-End Testing (2 days)

**Tasks**:

- [ ] Test first-use initialization flow
- [ ] Test Skill modification flow
- [ ] Test team collaboration scenarios
- [ ] Test update detection

**Deliverables**:

- ✅ E2E test cases

#### Sprint 4.2: Documentation Writing (2 days)

**Tasks**:

- [ ] Write user guide
- [ ] Write developer guide
- [ ] Update README
- [ ] Create migration guide (from v0.13.0 design)

**Deliverables**:

- ✅ `docs/agent-skills-user-guide-v3.md`
- ✅ `docs/agent-skills-developer-guide.md`
- ✅ Updated main README

#### Sprint 4.3: Release Preparation (1 day)

**Tasks**:

- [ ] Update CHANGELOG
- [ ] Prepare release notes
- [ ] Update version number to v0.13.0

**Deliverables**:

- ✅ `CHANGELOG.md` update
- ✅ Release notes

---

## 🔄 Migration from v0.13.0 Design (If Already Developed)

### If Implemented According to v0.13.0 Design

**Migration Steps**:

1. **Retained Code**:
    - ✅ Skill interface definition (most fields)
    - ✅ SKILL.md parsing logic
    - ✅ Progressive loading
    - ✅ System Prompt integration
    - ✅ WebView base components

2. **Code to be Modified**:
    - ⚠️ SkillsManager.scanSkills() (remove extension scanning)
    - ⚠️ generateSkillId() (remove source prefix)
    - ⚠️ getSkillsPaths() (return only 2 paths)

3. **Code to be Added**:
    - ✅ SkillsInitializer class
    - ✅ Initialization prompt in extension.ts
    - ✅ New command registration

4. **Code to be Deleted**:
    - ❌ Three-tier priority logic
    - ❌ Extension Skills related code

### Migration Checklist

- [ ] Create SkillsInitializer
- [ ] Simplify SkillsManager (remove extension related)
- [ ] Modify Skill ID generation (remove source prefix)
- [ ] Update extension activation logic (add initialization prompt)
- [ ] Register new commands
- [ ] Update WebView UI (remove Extension Skills group)
- [ ] Create Skills template directory
- [ ] Update build configuration (copy templates)
- [ ] Update test cases
- [ ] Update documentation

---

## ❓ FAQ

### Q: What's the main difference between v0.14.0 and v0.13.0 design?

**A**: v0.14.0 adopts a **project initialization model**, where built-in Skills are templates, not runtime dependencies:

- ✅ Users have full control over Skills (modifiable)
- ✅ The architecture is simpler (only 2 tiers, not 3)
- ✅ Follows industry best practices (learning from Cursor/Claude Code)

### Q: Why can't we use Skills directly from dist/?

**A**: `dist/` is a build artifact and is overwritten with every `pnpm build`:

- ❌ User modifications would be lost
- ❌ Cannot be version controlled by Git (dist/ is in .gitignore)
- ❌ Does not support team collaboration

### Q: What if the user doesn't want to initialize?

**A**: They can choose "Later" or "Don't Ask Again":

- Choose "Later" → Prompt again the next time the project is opened
- Choose "Don't Ask Again" → Record the setting, no longer prompt automatically
- Can manually execute the `NovelWeave: Initialize Agent Skills` command at any time

### Q: Are personal Skills (globalStorage) still necessary?

**A**: Yes! For **cross-project** personal templates:

- e.g., "My writing style" (applies to all projects)
- e.g., "My proofreading checklist" (personal habit)
- Not suitable to be placed in a project (too personal, the team may not need it)

### Q: What if team members modify the same Skill?

**A**: Just like regular code, resolve conflicts via Git:

```bash
# Both modified romance/SKILL.md
$ git pull
Auto-merging .agent/skills/romance/SKILL.md
CONFLICT (content): Merge conflict in .agent/skills/romance/SKILL.md

# Manually resolve the conflict
$ code .agent/skills/romance/SKILL.md
# Edit, choose what to keep

$ git add .agent/skills/romance/SKILL.md
$ git commit -m "Merge romance skill changes"
```

### Q: Will NovelWeave updates overwrite my Skills?

**A**: **Absolutely not**!

- ✅ Project Skills (.agent/skills/) are completely under user control
- ✅ Extension updates only affect the templates (dist/templates/skills/)
- ✅ New Skills need to be added manually (via the "Check for New Skills" command)

### Q: Can I initialize only some Skills?

**A**: The initial version of v0.14.0 is "initialize all", but you can:

- Delete unnecessary Skills after initialization
- Or manually copy the Skills you need from the template

Future versions may add a "selective initialization" feature.

### Q: How do I back up my Skills?

**A**: Skills are in the project and are automatically backed up with Git:

```bash
$ git add .agent/skills/
$ git commit -m "Update skills"
$ git push
```

---

## 📊 Design Validation

### Alignment with Claude Code

| Feature | Claude Code | NovelWeave v0.14.0 | Consistency |
| --- | --- | --- | --- |
| **Initialize to project** | ✅ `.claude/` | ✅ `.agent/` | ✅ |
| **User has full control** | ✅ | ✅ | ✅ |
| **Git version control** | ✅ | ✅ | ✅ |
| **Team sharing** | ✅ | ✅ | ✅ |
| **Freely modifiable** | ✅ | ✅ | ✅ |
| **AI autonomous activation** | ✅ | ✅ | ✅ |
| **No automatic updates** | ✅ | ✅ | ✅ |

### Design Principle Check

- ✅ **User control first**: Users have full control over project Skills
- ✅ **Simplicity**: Two-tier architecture, easy to understand
- ✅ **Git-friendly**: Skills are in the project, naturally supporting version control
- ✅ **Team collaboration**: Shared via Git, no extra mechanism needed
- ✅ **Best practices**: Learning from mature products (Cursor/Claude Code)
- ✅ **Backward compatibility**: Retain support for personal Skills (globalStorage)
- ✅ **Progressive enhancement**: Optional initialization, optional update check

---

## 📚 Appendix

### A. Complete Directory Structure

```
novel/                                      # NovelWeave project
├── src/
│   ├── templates/
│   │   └── skills/                        # Skills templates (source code)
│   │       ├── README.md
│   │       ├── genre-knowledge/
│   │       │   ├── romance/
│   │       │   │   └── SKILL.md
│   │       │   ├── mystery/
│   │       │   │   └── SKILL.md
│   │       │   └── fantasy/
│   │       │       └── SKILL.md
│   │       ├── quality-assurance/
│   │       │   ├── consistency-checker/
│   │       │   │   └── SKILL.md
│   │       │   └── novelweave-workflow/
│   │       │       └── SKILL.md
│   │       └── writing-techniques/
│   │           ├── dialogue-techniques/
│   │           │   └── SKILL.md
│   │           └── scene-structure/
│   │               └── SKILL.md
│   │
│   ├── services/
│   │   └── skills/
│   │       ├── SkillsManager.ts           # Simplified version
│   │       ├── SkillsInitializer.ts       # New
│   │       └── __tests__/
│   │
│   └── dist/
│       └── templates/
│           └── skills/                    # Copied from src/templates/skills/
│
└── esbuild.mjs                            # Copy Skills during build

---

user-project/                             # User's novel project
├── .agent/
│   └── skills/                           # Initialized from template, user has full control
│       ├── README.md
│       ├── genre-knowledge/
│       │   └── romance/
│       │       └── SKILL.md              # Modifiable ✅
│       └── ...
│
└── .git/                                 # Git tracks .agent/skills/

---

globalStorage/                            # Cross-project personal Skills
└── skills/
    └── my-writing-style/
        └── SKILL.md
```

### B. Skill ID Comparison

**v0.13.0 Design (Wrong)**:

```typescript
// Three different Skills, cannot be overridden
skills.set("extension:romance", extensionRomanceSkill)
skills.set("project:romance", projectRomanceSkill)
skills.set("personal:romance", personalRomanceSkill)

// Result: 3 romance Skills exist
```

**v0.14.0 (Correct)**:

```typescript
// Same name automatically overrides (Map feature)
skills.set("romance", personalRomanceSkill) // Scan personal first
skills.set("romance", projectRomanceSkill) // Scan project later, overrides

// Result: Only 1 romance Skill (the project version)
```

---

## ✅ Acceptance Criteria

### Core Functionality

- [ ] ✅ Automatically prompts for initialization on first use
- [ ] ✅ Successful initialization copies all Skills to `.agent/skills/`
- [ ] ✅ User can directly modify project Skills
- [ ] ✅ Project Skills override personal Skills
- [ ] ✅ Skill ID uses only the directory name (no source prefix)
- [ ] ✅ No longer scans dist/templates/skills/ (only templates)

### Commands

- [ ] ✅ `NovelWeave: Initialize Agent Skills` command works correctly
- [ ] ✅ `NovelWeve: Check for New Skills` command works correctly
- [ ] ✅ Re-initializing when already initialized will show a warning

### User Experience

- [ ] ✅ First-use prompt is clear and easy to understand
- [ ] ✅ Initialization progress is displayed
- [ ] ✅ The Skills directory can be opened after successful initialization
- [ ] ✅ Changes to Skills take effect immediately (rescan)

### Team Collaboration

- [ ] ✅ `.agent/skills/` can be committed to Git
- [ ] ✅ Team members can use it directly after cloning
- [ ] ✅ Skill conflicts can be resolved via Git

### Documentation

- [ ] ✅ PRD v0.14.0 is complete and clear
- [ ] ✅ User guide is detailed
- [ ] ✅ Developer guide is complete
- [ ] ✅ Migration guide from v0.13.0 design

---

## 📝 Summary

v0.14.0, by adopting the **project initialization model**, completely resolves the flaws in the v0.13.0 design:

1. ✅ **User has full control**: Skills are in the project, can be freely modified
2. ✅ **Simplified architecture**: Two-tier structure, easy to understand
3. ✅ **Git-friendly**: Naturally supports version control and team collaboration
4. ✅ **Follows best practices**: Learns from mature models like Cursor/Claude Code
5. ✅ **Backward compatible**: Retains support for personal Skills

This is a **more correct, simpler, and more powerful** design.

---

**End of Document**

_This PRD is redesigned based on the lessons learned from the v0.13.0 design, adopting a project initialization model._
_References: Best practices from Cursor, Claude Code_
_If you have any questions, please contact: WordFlow Lab Team_
