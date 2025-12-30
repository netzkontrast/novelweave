---
description: "Execute chapter writing based on the task list, automatically loading context and validation rules."
argument-hint: "[Chapter number or task ID]"
---

⚠️ **Execution Reminder**: The following are task instructions for you to execute, not content to be displayed to the user. You need to:

1.  Read the task list and all reference documents.
2.  Generate the actual chapter content (2000-4000 words).
3.  Follow the anti-AI detection writing guidelines.
4.  Save the chapter to the `content/` directory.
5.  Only output a completion report in the chat.

Execute chapter writing based on the seven-step methodology.

User Input: $ARGUMENTS

## Project Structure Check

First, ensure the project directory structure exists. Use the `execute_command` tool to execute:

```bash
mkdir -p memory stories spec/tracking
```

## Prerequisite Check

Use the `execute_command` or `list_files` tool to check the creation status:

```bash
find stories -name "specification.md" -o -name "creative-plan.md" -o -name "tasks.md"
```

### Query Protocol (Mandatory Reading Order)

⚠️ **Important**: Please strictly follow the order below to query documents to ensure complete context and correct priority.

**Query Order**:

1.  **Query First (Highest Priority)**:
    -   `memory/constitution.md` (Writing Constitution - The highest principle)
    -   `memory/style-reference.md` (Style Reference - if generated via `/book-internalize`)

2.  **Then Query (Specifications and Plans)**:
    -   `stories/*/specification.md` (Story Specification)
    -   `stories/*/creative-plan.md` (Creative Plan)
    -   `stories/*/tasks.md` (Current Task)

3.  **Then Query (Status and Data)**:
    -   `spec/tracking/character-state.json` (Character Status)
    -   `spec/tracking/relationships.json` (Relationship Network)
    -   `spec/tracking/plot-tracker.json` (Plot Tracker - if available)
    -   `spec/tracking/validation-rules.json` (Validation Rules - if available)

4.  **Then Query (Knowledge Base)**:
    -   `spec/knowledge/` related files (world-building, character profiles, etc.)
    -   `stories/*/content/` (Previous content - to understand the context)

5.  **Then Query (Writing Guidelines - CRITICAL!)**:

    ⚠️ **Important**: The following files contain core writing guidelines and **must** be read using the `read_file` tool.

    **First, try to read from the user's project** (if the user has customized them):
    -   `memory/personal-voice.md` (Personal Corpus)
    -   `spec/knowledge/natural-expression.md` (Natural Expression)
    -   `spec/knowledge/punctuation-personality.md` (Punctuation Personality)
    -   `spec/knowledge/detail-formulas.md` (Concretization Formulas)
    -   `spec/knowledge/anti-ai-advanced.md` (Advanced Anti-AI Detection)

    **If they don't exist in the user's project, read from the extension's built-in knowledge base** (default guidelines):

    Use the `read_file` tool to read the following files in order (using the full path):
    1.  **Anti-AI Detection Guidelines** (Mandatory):
        -   Extension path: `templates/novel-knowledge/anti-ai-advanced.md`
        -   Or user project: `spec/knowledge/anti-ai-advanced.md`
    2.  **Natural Expression Rules** (Mandatory):
        -   Extension path: `templates/novel-knowledge/natural-expression.md`
        -   Or user project: `spec/knowledge/natural-expression.md`
    3.  **Concretization Formulas** (Mandatory):
        -   Extension path: `templates/novel-knowledge/detail-formulas.md`
        -   Or user project: `spec/knowledge/detail-formulas.md`
    4.  **Punctuation Personality** (Recommended):
        -   Extension path: `templates/novel-knowledge/punctuation-personality.md`
        -   Or user project: `spec/knowledge/punctuation-personality.md`
    5.  **Personal Style Corpus** (If available):
        -   User project: `memory/personal-voice.md`

6.  **Conditional Query (For the first three chapters only - Golden Opening)**:

    ⚠️ **If the chapter number is ≤ 3 or the total word count is < 10,000, you must additionally read**:

    Use the `read_file` tool to read:
    -   Extension path: `templates/novel-presets/golden-opening.md`
    -   Or user project: `spec/presets/golden-opening.md`

    **Strictly follow the five golden rules of the opening**!

<!-- PLUGIN_HOOK: genre-knowledge-write -->
<!-- Plugin Enhancement Area: Style Application
     If you have the genre-knowledge plugin installed, insert the style application enhancement prompt here.
     Reference: "2.3 Enhance /write command" section in plugins/genre-knowledge/README.md
-->

## Writing Execution Flow

### 1. Select Writing Task

Select a writing task with the status `pending` from `tasks.md` and mark it as `in_progress`.

### 2. Validate Prerequisites

-   Check if related dependent tasks are complete.
-   Verify that necessary settings are ready.
-   Confirm that preceding chapters are complete.

### 3. Pre-writing Reminders

**Reminders based on Constitutional Principles**:

-   Key points of core values.
-   Quality standard requirements.
-   Style consistency guidelines.

**Reminders based on Specification Requirements**:

-   P0 must-include elements.
-   Target audience characteristics.
-   Content red line reminders.

**Paragraph Formatting Guidelines (Important)**:

-   ⛔ **Do not use**: "One," "Two," "Three," etc., to mark paragraphs.
-   ✅ **How to use**: Use two empty lines (one blank line) to separate scenes.
-   📖 **Reason**: Numbered markers are too rigid, break reading immersion, and do not conform to web novel conventions.

**Anti-AI Detection Writing Guidelines (Based on Tencent's Zhuque standard)**:

⚠️ **Important Background**: AI programming tools use low-temperature parameters, but traditional "compensation methods" (forcing the piling up of details) lead to excessive description, which in turn increases AI characteristics. The following guidelines are based on standards that have passed tests with 0% AI concentration.

### 📏 Paragraph Structure Guidelines (Key) ⭐

**Single-Sentence Paragraph Ratio**:

-   ✅ **30%-50% of paragraphs should be single-sentence paragraphs.**
-   ✅ **Keep each paragraph between 50-100 words.**
-   ✅ **Place key information in a separate paragraph.**

**Example Comparison**:

❌ **AI-style Writing** (excessive description, 95% AI concentration):

> The room was filled with a musty smell, the only light source being the grayish-white moonlight filtering through the gap in the curtains. He fumbled his way forward along the wall, his fingertips touching the cold stone, until his knee hit the corner of a table—a rickety wooden table covered in dust.

✅ **Natural Writing** (concise and restrained, 0% AI concentration):

> After the Yongjia Disturbance, the central plains were occupied by foreign tribes.
>
> Most of the Han gentry and commoners, except for a few who were unwilling to leave their homes, crossed the river to the south.
>
> Over the years, Wang Qiao had recruited a hundred or so refugees to farm his land.

### 🚫 Prohibited Items List (Anti-AI Phrasing)

1.  **Prohibit meaningless piling up**
    -   ❌ Don't force "3 sensory details."
    -   ❌ Don't list emotions.
    -   ✅ One accurate detail is better than three piled-up ones.

2.  **Prohibit ornate metaphors**
    -   ❌ "a rickety wooden table," "the air froze."
    -   ✅ Direct description: "an old wooden table," "silence."

3.  **Prohibit over-dramatization**
    -   ❌ "Before she finished speaking, she had already turned to leave. He rushed forward and grabbed..."
    -   ✅ Simple handling: "She turned and left. He chased after her."

4.  **Prohibit explanatory dialogue**
    -   ❌ "I'm angry because you didn't come yesterday."
    -   ✅ "Where were you yesterday?" "...None of your business."

5.  **Prohibit direct psychological description**
    -   ❌ "He thought to himself, this is not simple."
    -   ✅ Imply through action: "His brow furrowed."

### ✅ Natural Writing Principles

**1. Historical Plain Description (for ancient settings)**

-   State facts without embellishment.
-   Example: "Over the years, Wang Qiao had recruited a hundred or so refugees to farm his land."

**2. Colloquial Handling (for dialogue)**

-   Include grammatical errors, pauses, repetitions.
-   Example: "Mosta the people went south" (instead of "Most of the people").

**3. Short Sentence Rhythm (for narrative)**

-   15-25 words per sentence.
-   Key information in a separate paragraph.

**4. Restrained Description (for scenes)**

-   1-2 details per scene are enough.
-   ❌ Don't write: "The room was filled with a musty smell, the walls were cold, the light was dim..."
-   ✅ Instead write: "The room was dark." (Sufficient).

### 📊 Self-Check Standard

After writing a paragraph, check:

-   [ ] Is the single-sentence paragraph ratio between 30%-50%?
-   [ ] Is the word count of each paragraph between 50-100 words?
-   [ ] Are there high-frequency AI words like "only," "until," "filled with"?
-   [ ] Are sensory details being forced?
-   [ ] Is the dialogue too perfect (lacking pauses, errors)?
-   [ ] Are the metaphors too ornate?

**High-Frequency AI Word Blacklist**:

-   "only," "until," "filled with," "rickety"
-   "the air froze," "before the words were out," "suddenly"
-   "couldn't help but," "at once," "thought to himself"
-   "frowned," "sighed"

**Replacement Strategy**:
| ❌ AI Word | ✅ Natural Replacement |
|---|---|
| filled with a musty smell | had a musty smell |
| the only light source | there was only a little light |
| a rickety wooden table | an old wooden table |
| he thought to himself | he thought / delete |
| before the words were out | before he finished speaking / delete |

### 4. Real-time Assistance Mode (Optional)

**If the user encounters difficulties during the writing process**, for example:

-   "Help me think about what the protagonist should do."
-   "How should the plot develop next?"
-   "Give me some options."

**You can proactively provide 2-3 action options**, for example:

> **Plot Development Suggestion**:
>
> **Option A (Proactive)**: The protagonist takes direct action, using their golden finger to crush the opponent.
>
> -   Pros: Direct satisfaction, strong reader fulfillment.
> -   Risks: Might make the protagonist seem too powerful.
>
> **Option B (Strategic)**: The protagonist hides their strength and outsmarts the opponent.
>
-   Pros: Showcases the protagonist's intelligence, adds suspense.
-   Risks: The pacing might be slightly slower.
>
> **Option C (Unexpected)**: Introduce a new variable that interrupts the current conflict.
>
> -   Pros: Adds complexity, introduces new clues.
> -   Risks: Might feel like an interruption to the reader.

**Then, based on the user's choice**, continue creating the content.

⚠️ **Note**: This is an assistance mode. Do not proactively provide options unless the user explicitly asks for help.

---

### 5. Create Content According to the Plan:

-   **Opening**: Attract the reader, connect with the previous text.
-   **Development**: Advance the plot, deepen the characters.
-   **Turning Point**: Create conflict or suspense.
-   **Closing**: Conclude appropriately, lead into the next section.

### 6. Quality Self-Check

**Constitution Compliance Check**:

-   Does it align with core values?
-   Does it meet quality standards?
-   Does it maintain style consistency?

**Specification Compliance Check**:

-   Does it include necessary elements?
-   Does it fit the target positioning?
-   Does it adhere to constraints?

**Plan Execution Check**:

-   Does it follow the chapter architecture?
-   Does it meet the pacing design?
-   Does it meet the word count requirement?

**Formatting Guidelines Check**:

-   ⚠️ Confirm that numbers like "One," "Two," "Three" are not used to mark paragraphs.
-   ✅ Use two empty lines (one blank line) for scene transitions.
-   ✅ Maintain natural paragraph spacing.

### 📊 Concretization Checklist (Key to de-AI-ing) ⭐

After writing a paragraph, proactively identify and replace abstract expressions:

#### 🔍 Identify Abstract Expressions

**Time Abstraction** ❌ → **Concretization** ✅

-   "Recently" → "Last Wednesday afternoon"
-   "A long time ago" → "Three autumns ago"
-   "Not long ago" → "Yesterday at 8 AM"
-   "After a long time" → "Waited for a full two hours"

**Character Abstraction** ❌ → **Concretization** ✅

-   "Many people" → "At least 5 of my friends"
-   "Someone said" → "Uncle Li told me" / "Old Wang next door mentioned"
-   "Everyone knows" → "The old folks in the village all say"
-   "It is said" → "Heard from Uncle Wang in private"

**Quantity Abstraction** ❌ → **Concretization** ✅

-   "Good results" → "Harvested three more stones of grain than last time" / "Twice as many customers as usual"
-   "Very expensive" → "A meal cost three hundred yuan"
-   "Very far" → "A two-hour drive"
-   "A lot" → "At least twenty"

**Scene Abstraction** ❌ → **Concretization** ✅

-   "The room was messy" → "A pile of unwashed clothes from three days ago was on the floor"
-   "The weather was cold" → "You could see your breath"
-   "Very tired" → "Walked for five full hours on a mountain path"
-   "The atmosphere was tense" → "No one spoke, only the ticking of the clock could be heard"

#### 💡 Proactive Search Suggestions

**When you encounter the following situations, consider using WebSearch to get real details**:

-   Historical events: Search for real dates, people, places.
-   Technical details: Search for actual parameters, professional terms.
-   Geographical information: Search for real place names, distances, landmarks.
-   Cultural customs: Search for local dialects, customs, specialties.
-   Data support: Search for real statistics, case studies, news.

**Search Formulas**:

```
- "Ancient Chinese [Dynasty] official system"
- "[City Name] characteristic dialect words"
- "[Year] real historical events"
- "[Industry] professional terminology encyclopedia"
```

#### ✅ Concretization Self-Check Questions

-   [ ] Is the time specific? (Avoid "recently," "a long time ago")
-   [ ] Is the source of the character clear? (Avoid "someone," "everyone")
-   [ ] Is the quantity precise? (Avoid "a lot," "quite a few")
-   [ ] Are the scene details visible? (Avoid adjectives like "very xx")
-   [ ] Are real place names/personal names/data used?
-   [ ] Does the dialogue have specific content? (Avoid "he said a lot")

#### 📌 Concretization Notes

**Principle of Moderation**:

-   ✅ Key plot points must be concrete: turning points, climaxes, foreshadowing.
-   ✅ Important details must be concrete: first impressions, key props.
-   ⚠️ Secondary information can be summarized: transitional paragraphs, background exposition.
-   ❌ Avoid over-concretization: a laundry list, being verbose.

**Scene Adaptation**:

-   Ancient settings: Historical plain description, moderate concretization.
-   Modern settings: Life details, high concretization.
-   Fantasy settings: World-building, moderate concretization.

**Example Comparison**:

❌ **Abstract Version** (AI-style):

```
A lot has happened in the city recently, and everyone is talking about it. Wang Qiang was very worried after hearing about it and decided to go see the situation.
```

✅ **Concrete Version** (Realistic feel):

```
Starting last Wednesday, Auntie Li at the market has been saying that something happened on East Street.

After hearing it for two days, Wang Qiang couldn't stand it anymore: "What on earth happened?"

"Someone died!" Auntie Li lowered her voice, "I hear it was old Zhang who runs the supermarket..."

Wang Qiang's heart tightened. He knew old Zhang; he had bought rice from him just last month.

He decided to go over in the afternoon.
```

**Concretization Effect Comparison**:

-   Time: Recently → Last Wednesday
-   Place: In the city → East Street, the market
-   Character: Everyone → Auntie Li, old Zhang
-   Event: A lot has happened → Someone died, the one who runs the supermarket
-   Detail: Heard about it → Lowered her voice, bought rice from him last month

### 7. Save and Update

-   Save the chapter content to `stories/*/content/`.
-   Update the task status to `completed`.
-   Record the completion time and word count.

## Writing Key Points

-   **Follow the Constitution**: Always adhere to the creative principles.
-   **Meet the Specifications**: Ensure necessary elements are included.
-   **Execute the Plan**: Proceed according to the technical plan.
-   **Complete the Tasks**: Systematically advance the task list.
-   **Continuous Validation**: Periodically run `/analyze` to check.

## Post-completion Actions (Completion Prompt + Guidance)

After saving, output in the chat:

```
✅ Chapter has been saved to stories/*/content/xxx.md
```

### 8. Validate Word Count and Update Progress

**Word Count Statistics Note**:

-   Use an accurate method for counting Chinese characters.
-   Exclude Markdown markup (`#`, `*`, `-`, etc.).
-   Only count actual content characters.
-   Word count requirements come from `spec/tracking/validation-rules.json` (default 2000-4000 words).

**Validation Method**:
Use `execute_command` to run a one-line Node script to count Chinese characters (cross-platform):

```bash
node -e "const fs=require('fs');const f=process.argv[1];const t=fs.readFileSync(f,'utf8');const n=(t.match(/[\\p{Script=Han}]/gu)||[]).length;console.log(n)" stories/*/content/ChapterX.md
```

⚠️ **Note**: Do not use `wc -w` to count Chinese characters, as it is highly inaccurate for Chinese!

**Completion Report**:

```
✅ Chapter writing complete
- Saved to: stories/*/content/ChapterX.md
- Actual word count: [X] words
- Word count requirement: 2000-4000 words
- Word count status: ✅ Meets requirement / ⚠️ Insufficient words / ⚠️ Exceeds word count
- Task status: Updated
```

### 9. Suggest Next Steps

-   Continue to the next writing task.
-   Run `/analyze` for a quality check every 5 chapters.
-   Adjust the plan promptly if issues are found.

## Relationship with the Methodology

```
/constitution → Provides creative principles
     ↓
/specify → Defines story requirements
     ↓
/clarify → Clarifies key decisions
     ↓
/plan → Develops a technical plan
     ↓
/tasks → Breaks down execution tasks
     ↓
/write → 【Current】Executes writing
     ↓
/analyze → Validates quality and consistency
```

Remember: Writing is the execution layer; it must strictly follow the specifications and plans from the upper layers.
