---
description: "Intelligent Analysis: Automatically selects framework analysis (before write) or content analysis (after write)."
argument-hint: "[--type=framework|content]"
---

⚠️ **Execution Reminder**: The following are task instructions for you to execute, not content to be displayed to the user. You need to:

1.  Automatically detect the creation stage (number of chapters).
2.  Perform the actual analysis (framework or content).
3.  Generate a detailed analysis report.
4.  Save the analysis report (optional).
5.  Output the analysis summary and improvement suggestions in the chat.

Perform an intelligent comprehensive analysis of the novel project. Depending on the current creation stage, automatically choose to perform either **Framework Consistency Analysis** (before writing) or **Content Quality Analysis** (after writing).

User input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Core Concept

**One Command, Dual Intelligence**:

-   📐 **Framework Analysis**: Verifies the consistency of specifications, plans, and tasks before writing (similar to spec-kit).
-   📝 **Content Analysis**: Verifies the quality and compliance of completed content after writing.

**Restrained but not Simplistic**:

-   The user only needs to execute `/analyze`, and the system automatically determines which type of analysis to perform.
-   Supports manual mode specification: `$ARGUMENTS --type=framework` or `--type=content`.

## Execution Flow

### 1. Intelligent Stage Detection

**Detect the current creation status**:

Use `execute_command` or `list_files` to check project files (combining the logic from tracking scripts):

```bash
# If plot-tracker.json exists, prioritize judgment based on its currentState / completed nodes
test -f spec/tracking/plot-tracker.json && echo "plot-tracker-found" || echo "plot-tracker-not-found"

# Count the number of completed chapters (as a general fallback)
find stories -path "*/content/*.md" -type f | wc -l
# Check for the existence of specification, plan, and task files
find stories -name "specification.md" -o -name "creative-plan.md" -o -name "tasks.md"
```

**Automatically determine the analysis type**:

-   If `spec/tracking/plot-tracker.json` exists and records that the content stage has been entered → **Content Analysis**
-   If the number of chapters = 0 → **Framework Analysis**
-   If the number of chapters < 3 → **Framework Analysis** (but suggest that writing can continue)
-   If the number of chapters ≥ 3 → **Content Analysis**

### 2. Decision Logic

Parse the user arguments `$ARGUMENTS`:

**Manual Specification Mode** (highest priority):

-   Contains `--type=framework` → Force framework analysis.
-   Contains `--type=content` → Force content analysis.

**🆕 Specialized Analysis Mode** (new):

-   Contains `--focus=opening` → Specialized opening analysis (focus on the first 3 chapters).
-   Contains `--focus=pacing` → Specialized pacing analysis (focus on the distribution of high points/conflicts).
-   Contains `--focus=character` → Specialized character analysis (focus on character arcs).
-   Contains `--focus=foreshadow` → Specialized foreshadowing analysis (focus on planting and resolving foreshadowing).
-   Contains `--focus=logic` → Specialized logic analysis (focus on finding logical loopholes).
-   Contains `--focus=style` → Specialized style analysis (focus on checking writing style consistency).

**Automatic Judgment Mode**:

-   Number of chapters = 0 → **Framework Analysis**
-   Number of chapters < 3 → **Framework Analysis** (but suggest that writing can continue)
-   Number of chapters ≥ 3 → **Content Analysis**

### 3. Execute Corresponding Analysis

Based on the decision, perform one of the following two analyses.

---

## Mode A: Framework Consistency Analysis

**Objective**: To verify that preparation is sufficient before writing, ensuring no contradictions between specifications, plans, and tasks.

### A1. Load Benchmark Documents

-   Constitution file: `memory/constitution.md`
-   Specification file: `stories/*/specification.md`
-   Plan file: `stories/*/creative-plan.md`
-   Tasks file: `stories/*/tasks.md`

### A2. Coverage Analysis

Check if all specification requirements have corresponding plans and tasks:

```markdown
## Coverage Analysis Report

### P0 Requirement Coverage

- [Requirement 1: Protagonist's growth arc] → ✅ Plan Chapter 3, Tasks #5-8
- [Requirement 2: Villain's setting] → ⚠️ Mentioned in the plan, but no specific tasks
- [Requirement 3: Suspense setup] → ❌ Not covered in plan or tasks

### P1 Requirement Coverage

Coverage: 75% (3/4)

### P2 Requirement Coverage

Coverage: 50% (2/4)

### Task Completeness

- Do all planned chapters have corresponding tasks: ⚠️ Chapters 10-12 are missing tasks
- Do tasks cover all key scenes: ✅ Yes
```

### A3. Consistency Check

Verify if there are contradictions between documents:

```markdown
## Consistency Check Report

### Specification ↔ Plan

- ✅ Consistent theme expression
- ⚠️ Specification requires "fast pacing," but the plan for the first 5 chapters is slow-paced
- ❌ Specification prohibits "too much romance," but chapters 6-8 of the plan have extensive romantic plotlines

### Plan ↔ Tasks

- ✅ All planned chapters have tasks
- ⚠️ The total estimated word count for tasks is 150K, but the plan's target is 100K
- ❌ The plan requires Chapter 5 to be a climax, but the task is marked as a "transitional chapter"

### Constitution Compliance

- ✅ The plan aligns with the values of the writing constitution
- ✅ The task breakdown meets quality standards
```

### A4. Logical Issue Warnings

Analyze potential logical loopholes in the storyline design:

```markdown
## Logical Issue Warnings

### Timeline Conflicts

- ⚠️ Chapter 3 is "three years later," but a character in Chapter 5 mentions "an event from two years ago," which doesn't add up.

### Character Ability Contradictions

- ❌ The protagonist "doesn't know martial arts" in Chapter 2, but the task for Chapter 4 describes "defeating an enemy with swordsmanship."

### Unplanned Foreshadowing

- ⚠️ A "mysterious token" is foreshadowed in Chapter 1, but there is no plan to resolve it in subsequent chapters.
```

### A5. Readiness Assessment

Evaluate whether writing can begin:

```markdown
## Readiness Assessment

### Necessary Conditions (P0)

- [x] Specification is complete and clear
- [x] Plan covers all P0 requirements
- [ ] Task breakdown is incomplete (missing tasks for 3 chapters)
- [ ] No fatal logical contradictions (2 found)

### Recommended Conditions (P1)

- [x] Character profiles are well-developed
- [ ] World-building document is not detailed enough
- [x] Timeline planning is clear

### Overall Score: 6/10

**Recommendations**:

1.  🔴 Must Fix: Add tasks for chapters 10-12.
2.  🔴 Must Fix: Resolve timeline and character ability contradictions.
3.  🟡 Recommended Optimization: Supplement the world-building document.
4.  🟢 Optional: Adjust the pacing design of the first 5 chapters.

**Conclusion**: It is currently **not recommended to start writing**. Please resolve the P0 issues first.
```

---

## Mode B: Content Quality Analysis

**Objective**: To conduct a comprehensive quality verification of the completed content, ensuring it meets specifications and providing improvement suggestions.

### B1. Load Verification Benchmarks

-   Constitution file: `memory/constitution.md`
-   Specification file: `stories/*/specification.md`
-   Plan file: `stories/*/creative-plan.md`
-   Task list: `stories/*/tasks.md`
-   **Completed Content**: `stories/*/content/*.md` or `stories/*/chapters/*.md`

### B2. Constitution Compliance Check

Verify that the work adheres to the principles of the writing constitution:

```markdown
## Constitution Compliance Report

### Core Value Check

- [x] Value Principle 1: Positive theme ✅
- [x] Value Principle 2: Avoid vulgar content ✅
- [ ] Value Principle 3: Respect cultural traditions ⚠️ Controversial description in Chapter 7

### Quality Standard Verification

- Logical Consistency: 8/10 ⚠️ Minor contradictions in Chapters 3 and 6
- Character Depth: 7/10 (Protagonist is multi-layered, supporting characters are a bit flat)
- Writing Standard: 8/10 (Good fluency, some descriptions can be enhanced)

### Style Consistency

- Narrative Style: Consistent ✅
- Language Style: Consistent ✅
- Pacing Control: Slow at the beginning, faster later, overall reasonable ✅

**Overall Score: 8/10**

### Completion Prompt + Next Steps

Output in chat:

✅ Analysis complete (Content Mode/Framework Mode)

Provide targeted next-step suggestions:
- If it's a framework issue (missing tasks/logical conflicts) → Run `/plan` or `/tasks` to correct.
- If it's a content issue (text quality/style consistency) → Run `/write` to revise the corresponding chapter.
- If the current chapter count is 0 → Run `/specify` to define the specification first.
```

### B3. Specification Compliance Analysis

Check if the implementation meets the specification requirements:

```markdown
## Specification Compliance Analysis

### Core Requirement Coverage

#### P0 (Must Include)

- [Requirement 1: Father-son conflict] → ✅ Fully demonstrated in Chapters 2-4
- [Requirement 2: Suspense setup] → ⚠️ Insufficient suspense in Chapter 5
- [Requirement 3: Three-dimensional villain] → ❌ Villain has not yet officially appeared

Coverage: 67% (2/3)

#### P1 (Should Include)

Coverage: 75% (3/4)

#### P2 (Could Include)

Coverage: 50% (2/4)

### Goal Achievement

- Target Audience Fit: 85% (Pacing and plot align with target audience preferences)
- Market Positioning Compliance: 80% (Differentiating selling points are clear but need reinforcement)
- Success Criteria Achievement: 5/8 ⚠️ Some metrics not met

### Constraint Adherence

- Content Red Lines: ✅ No violations
- Creation Constraints: ✅ Word count, update frequency meet requirements
- Technical Constraints: ✅ Platform formatting standards met

**Overall Score: 7/10**
```

### B4. Plan Execution Analysis

Evaluate the deviation between actual execution and the plan:

```markdown
## Plan Execution Analysis

### Chapter Structure Comparison

| Plan | Actual | Deviation Analysis |
| --- | --- | --- |
| Chapter 1: Opening Hook | ✅ Complete | Meets expectations, strong opening appeal |
| Chapter 2: Conflict Development | ✅ Complete | Slightly adjusted, added foreshadowing |
| Chapter 3: Turning Point | ⚠️ Complete | Turning point moved up to the end of Chapter 2 |
| Chapter 4: Deepening Conflict | ✅ Complete | Fully aligns with the plan |
| Chapter 5: Prelude to Climax | ❌ Postponed | Actually became a transitional chapter |

### Character Development Trajectory

- Protagonist's Growth Arc: 85% compliance (Growth is slightly faster than planned)
- Supporting Character Functions: 70% fulfillment (Supporting Character B's role not fully realized)
- Relationship Evolution: 90% compliance (Father-son relationship evolves as expected)

### World-building Unfolding

- First-level setting (basic rules): ✅ Unfolded as planned
- Second-level setting (power structures): ⚠️ Revealed early (planned for Chapter 8, actually in Chapter 5)
- Third-level setting (ultimate secrets): To be unfolded

**Compliance Score: 8/10**
```

### B5. Content Quality Analysis

In-depth analysis of the work's quality:

```markdown
## Content Quality Analysis

### Text Statistics

- Total Word Count: 45,230 words
- Average Chapter Length: 6,461 words
- Completion Progress: 35% (7/20 chapters)

### Structural Analysis

- Plot Density: Medium (2-3 plot points per chapter)
- Conflict Frequency: Moderate (average of 1.5 conflicts per chapter)
- Pacing Variation: Slow in the first 3 chapters, accelerates in chapters 4-7, as expected

### Technical Issues

#### Logical Issues

1.  Chapter 3: A character mentions "an event from three years ago," but the timeline shows only two years have passed.
2.  Chapter 6: The protagonist uses an ability explicitly stated they "do not have" in Chapter 2.

#### Coherence Issues

1.  The cliffhanger at the end of Chapter 4 is not addressed at the beginning of Chapter 5.

#### Character Consistency

1.  The protagonist's reaction to similar events is contradictory in Chapters 2 and 5 (impulsive in Ch. 2, calm in Ch. 5).

### Strengths Identification

1.  Chapter 1: The opening hook is cleverly designed and introduced naturally.
2.  Chapter 4: The dialogue between the father and son is rich and emotionally sincere.
3.  Chapter 6: The action scene descriptions are fluid and create a strong visual sense.

**Quality Score: 7.5/10**
```

### 🆕 B5.1 Specialized Analysis (Optional)

**If the user specifies the `--focus` parameter, perform the corresponding in-depth specialized analysis**:

---

#### Specialization 1: Opening Analysis (`--focus=opening`)

**Objective**: To deeply analyze whether the first 1-3 chapters adhere to the golden opening rules.

**Analysis Dimensions**:

```markdown
## Specialized Opening Analysis Report

### Golden Rule Check

**If `spec/presets/golden-opening.md` exists, automatically read and apply the five golden rules.**

#### Rule 1: Dynamic Scene Entry

- ✅ Opening method of Chapter 1: [Action/Dialogue/Conflict] directly cuts in.
- ❌ Issue found: The opening has 200 words of static environmental description (violates the rule).
- Recommendation: Delete or shorten to under 50 words and go directly into the action.

#### Rule 2: Front-load the Core Conflict

- ✅ Timing of core conflict introduction: Chapter 1, Section [X].
- ⚠️ Conflict Intensity: Medium (recommend increasing to a level that "threatens the protagonist's survival/goal").
- Details: [Describe the conflict content].

#### Rule 3: Avoid Information Dumps

- ✅ Method of revealing world-building: Drip-feed, naturally integrated into the plot.
- ❌ Issue found: Chapter 1, Section 3 has a 500-word setting explanation (violates the rule).
- Recommendation: Split it across the first 5 chapters, revealing 100 words per chapter.

#### Rule 4: Limit the Number of Characters Introduced

- ✅ Named characters in Chapter 1: [X] (meets the ≤3 requirement).
- ❌ Issue found: 5 characters are introduced in Chapter 1, which is too many (violates the rule).
- Recommendation: Delay the introduction of [Character D] and [Character E] to Chapters 2-3.

#### Rule 5: Quickly Showcase the "Golden Finger" (Special Ability/Advantage)

- ✅ Timing of golden finger reveal: Chapter [X].
- ⚠️ Reveal method: Only mentioned, not actually used (recommend demonstrating its effect).
- Details: [Describe the reveal method].

### Opening Hook Assessment

- **First Sentence Hook Strength**: [Strong/Medium/Weak]
    - Current: [Quote the first sentence]
    - Analysis: [Is it engaging?]
    - Recommendation: [Direction for optimization]

- **Chapter 1 Ending Hook**: [Strong/Medium/Weak]
    - Current: [Quote the ending paragraph]
    - Analysis: [Does it create anticipation?]
    - Recommendation: [Direction for optimization]

### First Three Chapters Pacing Check

| Chapter | Objective | Actual Fulfillment | Score |
| --- | --- | --- | --- |
| Chapter 1 | Hook the reader, build anticipation | [Describe actual effect] | [X]/10 |
| Chapter 2 | Showcase abilities, strengthen the hook | [Describe actual effect] | [X]/10 |
| Chapter 3 | Initial high point, confirm reader interest | [Describe actual effect] | [X]/10 |

**Opening Score: [X]/10**
**Recommendation**: [Specific improvement directions]
```

---

#### Specialization 2: Pacing Analysis (`--focus=pacing`)

**Objective**: To analyze the pacing distribution throughout the text and evaluate the density of high points/conflicts.

**Analysis Dimensions**:

```markdown
## Specialized Pacing Analysis Report

### Pacing Parameters (if rhythm-config.json exists)

**Read `spec/presets/rhythm-config.json` (if it exists)**:

- Target chapter word count: [X] words
- Target mini-climax interval: [X] chapters
- Target major-climax interval: [X] chapters
- Target pacing style: [Fast/Moderate/Slow]

### Conflict Distribution Statistics

| Chapter | Conflict Count | Conflict Type | Conflict Intensity | Meets Expectations? |
| --- | --- | --- | --- | --- |
| Chapter 1 | 2 | Interpersonal/Internal | Medium/High | ✅ |
| Chapter 2 | 1 | Interpersonal | Low | ⚠️ Too few |
| Chapter 3 | 3 | Interpersonal/External | High/High/Medium | ✅ |
| ... | ... | ... | ... | ... |

**Average Conflict Density**: [X] per chapter
**Recommended Density**: [Y] per chapter (based on genre and pacing configuration)

### High Point Distribution Statistics

| Chapter | High Point Type | High Point Intensity | Interval (chapters) |
| --- | --- | --- | --- |
| Chapter 1 | - | - | - |
| Chapter 3 | Face-slapping | High | 3 chapters |
| Chapter 7 | Level-up | Medium | 4 chapters |
| ... | ... | ... | ... |

**Average High Point Interval**: [X] chapters
**Recommended Interval**: [Y] chapters (based on rhythm-config or genre standards)

### Climax Distribution

- **Mini-climaxes**: Chapters [X], [Y], [Z]
    - Interval Reasonableness: ✅ Meets the standard of one every 5 chapters.
- **Major Climax**: Chapter [X]
    - Positional Reasonableness: ⚠️ Recommended for Chapter 30, actually in Chapter 25 (too early).

**Pacing Evaluation**:

- ✅ Overall rhythm is reasonable.
- ⚠️ Chapters 10-15 are slightly dull.
- ❌ There is a pacing break in Chapter 20.

**Improvement Suggestions**:

1.  Add a medium-intensity conflict in Chapter 12.
2.  Add transitional content in Chapter 20 to avoid a disjointed feeling.

**Pacing Score: [X]/10**
```

---

#### Specialization 3: Character Analysis (`--focus=character`)

**Objective**: To evaluate character arcs, consistency, and growth trajectories.

```markdown
## Specialized Character Analysis Report

### Protagonist Arc Tracking

**Read the planned character arc from specification.md and creative-plan.md**

| Node | Planned State | Actual State | Compliance |
| --- | --- | --- | --- |
| Beginning | [State A] | [Actual A] | ✅/⚠️/❌ |
| Trigger | [State B] | [Actual B] | ✅/⚠️/❌ |
| Growth | [State C] | [Actual C] | ✅/⚠️/❌ |
| Transformation | [State D] | [To be developed] | - |

**Growth Reasonableness Assessment**:

- ✅ Growth is triggered by events.
- ⚠️ Growth is slightly too fast (too big a leap from Chapter 3 to Chapter 7).
- ✅ Growth is consistent with the character's personality.

### Protagonist Consistency Check

- **Personality Consistency**:
    - ✅ Chapters 1-5: Impulsive personality is consistent.
    - ❌ Chapter 6: Suddenly becomes calm in a similar situation (contradiction).

- **Ability Consistency**:
    - ✅ Martial ability improves gradually, as per the setting.
    - ❌ Uses an unlearned skill in Chapter 7.

- **Motivation Consistency**:
    - ✅ Core goal is clear and consistent throughout.

### Supporting Character Function Assessment

| Supporting Character | Planned Function | Actual Function | Fulfillment |
| --- | --- | --- | --- |
| Supp. Char. A | Mentor | Mentor | 90% ✅ |
| Supp. Char. B | Rival | Not fully realized | 40% ⚠️ |
| Supp. Char. C | Foil | Foil | 85% ✅ |

**Recommendations**:

-   Increase confrontational scenes for Supporting Character B (Chapters 8-10).
-   Clarify Supporting Character B's motivations and stance.

### Relationship Network Evolution

Chapter 1: Protagonist ←Hostile← Villain A
↓
Mentor-Mentee
↓
Supp. Char. A

Chapter 7: Protagonist ←Complex Relationship← Villain A
↓ ↑
Mentor-Mentee Misunderstanding
↓ ↓
Supp. Char. A → Supp. Char. B

**Relationship Evolution Reasonableness**: ✅ Meets expectations.

**Character Score: [X]/10**
```

---

#### Specialization 4: Foreshadowing Analysis (`--focus=foreshadow`)

**Objective**: To check the completeness of foreshadowing planting and resolution.

```markdown
## Specialized Foreshadowing Analysis Report

### Read the foreshadowing management table from specification.md section 5.4

### Foreshadowing Planting Check

| Foreshadow ID | Planned Planting Chapter | Actual Planting Chapter | Planting Quality |
| --- | --- | --- | --- |
| F-001 | Chapter 1 | Chapter 1 | ✅ Natural, not abrupt |
| F-002 | Chapter 3 | Chapter 5 | ⚠️ Delayed by 2 chapters, need to confirm subsequent impact |
| F-003 | Chapter 5 | Not planted | ❌ Missing |

### Foreshadowing Resolution Check

| Foreshadow ID | Planned Resolution Chapter | Actual Resolution Chapter | Resolution Completeness |
| --- | --- | --- | --- |
| F-001 | Chapter 10 | To be completed | - |
| F-002 | Chapter 15 | To be completed | - |

### Unplanned Foreshadowing

**New foreshadowing added during writing (not in the specification)**:

1.  Chapter 2: Hint of a mysterious character → ⚠️ Need to add a resolution plan in the specification.
2.  Chapter 6: Mention of an ancient prophecy → ⚠️ Need to decide whether to resolve it.

### Foreshadowing Density Assessment

-   One foreshadowing planted every [X] chapters on average.
-   Recommended density: One every [Y] chapters (based on genre standards).
-   Evaluation: ✅ Compliant / ⚠️ Too frequent / ❌ Too sparse

### Risk Alerts

-   🔴 Foreshadowing F-003 was not planted, which may affect the plot of Chapter 15.
-   🟡 2 new foreshadowing elements added, need to supplement with resolution plans.

**Foreshadowing Management Score: [X]/10**
```

---

#### Specialization 5: Logic Analysis (`--focus=logic`)

**Objective**: To deeply search for logical loopholes and contradictions.

```markdown
## Specialized Logic Analysis Report

### Timeline Check

**Construct a complete timeline**:

Absolute Time | Story Time | Chapter | Key Event
--- | --- | --- | ---
2020-01-01 | Day 0 | - | [Background]
2020-01-05 | Day 4 | Chapter 1 | Protagonist leaves home
2020-01-10 | Day 9 | Chapter 3 | Meets mentor
2023-01-10 | Three years later | Chapter 5 | ⚠️ Contradicts Chapter 7
2022-01-10 | Two years later | Chapter 7 | Character recalls "an event from three years ago"

**Timeline Contradictions**:
-   ❌ The timelines in Chapter 5 and Chapter 7 do not match (1 found).
-   Recommendation: Unify to "two years later."

### Causal Logic Check

| Event A (Cause) | Event B (Result) | Logical Reasonableness |
| --- | --- | --- |
| Protagonist trains in Ch. 2 | Strength increases in Ch. 4 | ✅ Reasonable |
| Treasure is lost in Ch. 3 | Treasure appears in Ch. 6 | ❌ No explanation of how it was recovered |
| Makes a vow in Ch. 5 | Breaks the vow in Ch. 7 | ⚠️ Lacks psychological buildup |

### Ability Consistency Check

| Chapter | Ability Setting | Contradiction? |
| --- | --- | --- |
| Chapter 2 | Protagonist doesn't know martial arts | - |
| Chapter 4 | Protagonist learns basic swordsmanship | ✅ Reasonable transition |
| Chapter 6 | Protagonist uses advanced swordsmanship | ❌ Leap is too large, lacks a learning process |

### World-building Consistency

- ✅ Magic rules are consistent.
- ❌ "Technology is forbidden" is mentioned in Chapter 3, but high-tech weapons appear in Chapter 8.
- ⚠️ Slight differences in social class settings between Chapter 5 and Chapter 9.

### Motivation Reasonableness

| Character | Action | Motivation Explanation | Reasonableness |
| --- | --- | --- | --- |
| Protagonist | Risks life to save someone in Ch. 5 | Sense of justice | ✅ Fits character |
| Supp. Char. A | Betrays in Ch. 7 | Unexplained | ❌ Abrupt, lacks foreshadowing |
| Villain | Spares protagonist in Ch. 9 | Admires their talent | ⚠️ A bit forced |

**Logical Rigor Score: [X]/10**
```

---

#### Specialization 6: Style Analysis (`--focus=style`)

**Objective**: To check writing style consistency and compare with `style-reference.md`.

```markdown
## Specialized Style Analysis Report

### If `style-reference.md` exists (from `/book-internalize`)

**Read `memory/style-reference.md` and compare with the actual writing style.**

### Vocabulary Consistency Check

**Reference Style Vocabulary Preferences**:

-   Target common modifiers: [List]
-   Actual common modifiers: [List]
-   Match rate: [X]%

**Forbidden Word Check (AI-like phrasing)**:

-   ❌ Found "pervaded with" used [X] times (forbidden in style-reference).
-   ❌ Found "on the verge of collapse" used [X] times (forbidden in style-reference).
-   Recommendation: Replace with common vocabulary from the reference work.

### Sentence Structure Consistency Check

-   Average sentence length: Actual [X] words vs. Target [Y] words.
-   Paragraph density: Actual [X] words/paragraph vs. Target [Y] words/paragraph.
-   Evaluation: ✅ Compliant / ⚠️ Significant deviation.

### Description Proportion Check

| Type | Target Proportion | Actual Proportion | Deviation |
| --- | --- | --- | --- |
| Dialogue | 35% | 40% | +5% ⚠️ |
| Action | 40% | 30% | -10% ❌ |
| Description | 15% | 20% | +5% ⚠️ |
| Psychology | 10% | 10% | 0% ✅ |

**Recommendation**: Increase the proportion of action descriptions, reduce dialogue and description.

### Narrative Style Consistency

-   Perspective: ✅ Third-person limited, consistent.
-   Language: ✅ Colloquial style, meets the target.
-   Pacing: ⚠️ First 3 chapters are "fast-paced," but chapters 4-7 are slower.
-   Emotional Tone: ✅ Passionate tone is consistent throughout.

### Inter-Chapter Style Comparison

| Chapter | Style Characteristics | Similarity to Reference Work |
| --- | --- | --- |
| Chapter 1 | Concise, powerful, verb-dense | 85% ✅ |
| Chapter 2 | Slightly wordy, too many modifiers | 60% ⚠️ |
| Chapter 3 | Returns to a concise style | 80% ✅ |

**Style Consistency Score: [X]/10**
**Recommendation**: Revise Chapter 2, referencing the style of Chapters 1 and 3.
```

---

### B6. Task Completion Audit

Check the status of task execution:

```markdown
## Task Completion

### Overall Progress

- Total Tasks: 28
- Completed: 12 (43%)
- In Progress: 2 (7%)
- Not Started: 14 (50%)

### Key Milestones

- [Milestone 1: First 5 chapters complete] → ✅ Achieved
- [Milestone 2: Main plot progressed to 50%] → ⚠️ Delayed (planned for Chapter 10, actually at 30% in Chapter 7)
- [Milestone 3: Volume 1 finished] → TBD

### Blockers and Risks

1.  The "climax scene" task for Chapter 5 was not executed as planned, affecting subsequent pacing.
2.  The villain character has not yet appeared, which may impact mid-term conflict design.
```

### B7. Generate Improvement Suggestions

Provide specific suggestions based on the analysis results:

```markdown
## Improvement Suggestions

### Urgent Fixes (P0)

1.  **Timeline Contradiction**
    -   Impact: Undermines reader trust, affects logical rigor.
    -   Recommendation: Unify the time references in Chapters 3 and 6 to "two years ago."
    -   Location: Chapter 3, Section 2; Chapter 6, Section 4.

2.  **Character Ability Contradiction**
    -   Impact: Seriously affects character credibility.
    -   Recommendation: Add a transitional plot point about "learning martial arts" between Chapters 4-5, or remove the martial arts description from Chapter 6.
    -   Location: Chapter 2, Section 5; Chapter 6, Section 3.

### Optimization Suggestions (P1)

1.  **Insufficient Suspense in Chapter 5**
    -   Current: The ending of Chapter 5 is flat and lacks a hook.
    -   Recommendation: Add an unexpected event or piece of information at the end to create anticipation.
    -   Expected Effect: Improve reader retention.

2.  **Supporting Character B's Function Not Realized**
    -   Current: Supporting Character B appears but their role is unclear.
    -   Recommendation: Arrange a key role for Supporting Character B in Chapters 8-9 to follow up on earlier setup.
    -   Expected Effect: Enhance the character's presence and enrich the story's layers.

### Long-term Improvements (P2)

1.  **Early Reveal of World-building**
    -   Reason: May affect the sense of mystery later on.
    -   Solution: Evaluate whether to adjust the subsequent reveal pace or add deeper settings.
    -   Timing: Decide before Chapter 10.

**Priority Order**: P0-1 (Timeline) → P0-2 (Ability Contradiction) → P1-1 (Suspense) → P1-2 (Supporting Character)
```

### B8. Generate Verification Report

Create `stories/*/analysis-report.md`:

```markdown
# Work Analysis Report

## Summary

- Analysis Date: 2025-10-01
- Scope of Analysis: Chapters 1-7
- Analyzed Word Count: 45,230 words
- Overall Score: 7.5/10
- Recommended Action: Continue writing, revise chapters 1-7 in batches.

## Core Metrics

| Dimension | Score | Notes |
| --- | --- | --- |
| Constitution Compliance | 8/10 | Values are correct, style is consistent, 1 point needs attention |
| Specification Compliance | 7/10 | P0 requirement coverage is 67%, villain scenes need to be added |
| Plan Execution | 8/10 | Generally compliant, local adjustments are reasonable |
| Content Quality | 7.5/10 | 2 logical issues, 1 character contradiction to be fixed |
| Reader Experience | 8/10 | Pacing is reasonable, highlights are prominent, highly readable |

**Average Score: 7.7/10**

## Key Findings

1.  ✅ Strong opening appeal, excellent hook design in Chapter 1.
2.  ✅ Father-son relationship evolves as expected, with rich emotional layers.
3.  ⚠️ 2 logical contradictions exist and need to be fixed.
4.  ⚠️ Insufficient suspense in Chapter 5, affecting reader retention.
5.  ❌ P0 requirement "three-dimensional villain" has not yet been realized.

## Next Steps

1.  🔴 **Immediate Fixes**: Timeline contradiction, character ability contradiction (Est. 2 hours).
2.  🟡 **Near-term Optimizations**: Add suspense to Chapter 5, arrange scenes for Supporting Character B in Chapters 8-9 (Est. 1 day).
3.  🟢 **Continue Writing**: Proceed with Chapters 8-10 as planned, focusing on adding villain scenes.
4.  📅 **Plan Check**: Rerun `/analyze` after completing Chapter 10 for a phase-gate review.
```

---

## Analysis Dimension Framework

### Seven Major Analysis Dimensions

1.  **Compliance**: Conforms to constitution and specifications.
2.  **Consistency**: Logic, characters, world-building.
3.  **Completeness**: Requirement coverage, task completion.
4.  **Quality**: Writing, structure, pacing.
5.  **Innovation**: Highlights, unique features, breakthroughs.
6.  **Readability**: Fluency, appeal, resonance.
7.  **Feasibility**: Progress, resources, risks.

## Scoring Criteria

```text
10: Perfect, exceeds expectations
8-9: Excellent, minor flaws
6-7: Good, needs optimization
4-5: Passable, many issues
1-3: Poor, needs rewriting
```

## Usage Scenarios

### Scenario 1: Preparing to Start Writing

```bash
User: /tasks
AI: 28 writing tasks have been generated.

User: /analyze
AI: No chapter content detected, performing framework consistency analysis...

[Coverage Analysis]
⚠️ Specification requirement "villain setting" has no corresponding task.
❌ Plan for Chapters 10-12 is missing task breakdown.

[Consistency Check]
❌ Specification requires "fast pacing," but the plan for the first 5 chapters is slow.

[Readiness Score] 6/10
Recommendation: Fix the above issues before starting to write.
```

### Scenario 2: Completed Some Chapters

```bash
User: /write (completes Chapters 1-7)

User: /analyze
AI: 7 chapters detected as complete, performing content quality analysis...

[Constitution Compliance] 8/10 ✅
[Specification Compliance] 7/10 ⚠️ P0 requirement coverage is 67%
[Content Quality] 7.5/10 ⚠️ 2 logical issues found

[Improvement Suggestions]
P0: Fix timeline and character ability contradictions.
P1: Add suspense to Chapter 5.

[Recommendation]: After fixing P0 issues, continue writing Chapters 8-10.
```

### Scenario 3: Manually Specifying Mode

```bash
User: /analyze --type=framework
AI: Forcing framework analysis mode...

(Performs framework analysis even if there is chapter content)

User: /analyze --type=content
AI: Forcing content analysis mode...

(Performs content analysis even if there are fewer than 3 chapters)
```

### 🆕 Scenario 4: Specialized Analysis

```bash
# Specialized opening analysis (checks if the first 3 chapters follow the golden opening rules)
User: /analyze --focus=opening
AI: Performing specialized opening analysis...

[Golden Rule Check] (automatically reads golden-opening.md)
❌ Rule 1 violated: 200 words of static environmental description in Chapter 1.
⚠️ Rule 5 delayed: Golden finger is only revealed in Chapter 4 (recommended in Ch. 2-3).

[Opening Hook Assessment]
First sentence hook: Medium strength.
Chapter 1 ending hook: Strong.

[Recommendation]: Delete the opening environmental description, move the golden finger reveal to Chapter 3.

---

# Specialized pacing analysis (checks high point distribution and conflict density)
User: /analyze --focus=pacing
AI: Performing specialized pacing analysis...

[Pacing Parameters] (automatically reads rhythm-config.json)
Target high point interval: 5 chapters
Actual average interval: 7 chapters ⚠️ Too long.

[Conflict Distribution]
Average conflict density: 1.2 per chapter
Recommended density: 2 per chapter ❌ Insufficient.

[Recommendation]: Add high point designs in Chapters 5, 10, and 15.

---

# Specialized logic analysis (deep search for logical loopholes)
User: /analyze --focus=logic
AI: Performing specialized logic analysis...

[Timeline Contradiction]
❌ "Three years later" in Ch. 5 vs. "two years later" in Ch. 7.

[Ability Contradiction]
❌ "Doesn't know martial arts" in Ch. 2 → "Uses advanced swordsmanship" in Ch. 6 (leap is too large).

[Causal Logic]
❌ Treasure is lost in Ch. 3 → Treasure appears in Ch. 6 (no explanation of how it was recovered).

[Recommendation]: Fix the above 3 logical contradictions.

---

# Specialized style analysis (compares with style-reference.md)
User: /analyze --focus=style
AI: Performing specialized style analysis...

[Vocabulary Consistency] (automatically reads style-reference.md)
❌ Found forbidden word "pervaded with" 12 times.
⚠️ Actual average sentence length is 35 words vs. target of 25 (too long).

[Description Proportion]
Dialogue: 40% (Target 35%) +5% ⚠️
Action: 30% (Target 40%) -10% ❌

[Recommendation]:
1. Replace forbidden words with common words from the reference work.
2. Shorten sentences, increase the proportion of short sentences.
3. Increase action descriptions, reduce dialogue.
```

## Relationship with Other Commands

```text
/constitution → Defines writing principles
     ↓
/specify → Defines story specifications
     ↓
/clarify → Clarifies key decisions
     ↓
/plan → Creates a writing plan
     ↓
/tasks → Breaks down execution tasks
     ↓
/analyze (Framework Mode) → Verifies readiness
     ↓
/write → Executes chapter writing
     ↓
/analyze (Content Mode) → Verifies quality and consistency
     ↓
(Loop: Revise → Continue Writing → Analyze Again)
```

## Precautions

### Intelligent but Controllable

-   Automatic mode covers 90% of use cases.
-   Manual mode handles special requirements.
-   Users do not need to memorize complex rules.

### 🆕 Usage Scenarios for Specialized Analysis

**When to use specialized analysis?**

1.  **`--focus=opening`**: Immediately after completing the first 3 chapters.
    -   The opening is key to reader retention.
    -   Golden opening rules have strict requirements.
    -   Finding problems early is less costly.

2.  **`--focus=pacing`**: Once every 10-15 chapters.
    -   Check if the pacing meets expectations.
    -   Evaluate the reasonableness of high point/conflict distribution.
    -   Adjust pacing based on `rhythm-config`.

3.  **`--focus=character`**: After major turning points.
    -   After the protagonist experiences a significant event.
    -   When supporting characters are introduced or exit.
    -   When character relationships change.

4.  **`--focus=foreshadow`**: After completing each volume.
    -   Check for missed foreshadowing.
    -   Evaluate the quality of foreshadowing planting.
    -   Plan resolution timing in advance.

5.  **`--focus=logic`**: After adjusting the outline.
    -   After modifying important settings.
    -   After adjusting the timeline.
    -   After adding or deleting chapter content.

6.  **`--focus=style`**: Before batch revisions.
    -   Check consistency against `style-reference`.
    -   Find AI-like phrasing and forbidden words.
    -   Ensure the style matches the reference work.

**Relationship between Specialized and Comprehensive Analysis**:

-   **Comprehensive Analysis** (default): Suitable for periodic checks (every 5-10 chapters).
-   **Specialized Analysis** (`--focus`): Suitable for targeted optimization (when a problem is found).

**Recommended Workflow**:

1.  After every 5-10 chapters → `/analyze` (comprehensive analysis).
2.  Find an issue with the opening → `/analyze --focus=opening`.
3.  Feel the pacing is off → `/analyze --focus=pacing`.
4.  Unsure about logic → `/analyze --focus=logic`.
5.  Check before revising → `/analyze --focus=style`.

### Objective and Constructive

-   Analysis is based on data and standards.
-   Avoid subjective judgments.
-   Provide specific, actionable suggestions.

### Progressive Improvement

-   Analysis is for improvement, not criticism.
-   Record the results of each analysis.
-   Track the effectiveness of improvements.

### 🆕 Synergy with Other Features

**Files automatically read by specialized analysis**:

-   `spec/presets/golden-opening.md` → opening analysis
-   `spec/presets/rhythm-config.json` → pacing analysis
-   `memory/style-reference.md` → style analysis
-   `stories/*/specification.md` → benchmark for all analyses

**Advantages**:

-   No need to manually specify reference files.
-   Automatically applies standards from the reference work.
-   Maintains consistency in analysis standards.

---

**Remember**: **One command, three modes (framework/content/specialized), intelligent and precise. The purpose of `analyze` is to make the work better, whether before writing, after writing, or focusing on a specific dimension.**
