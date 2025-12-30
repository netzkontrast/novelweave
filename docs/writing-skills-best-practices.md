# Best Practices for Writing Skills

This guide provides an in-depth introduction to writing high-quality Agent Skills, helping you create effective, easy-to-maintain, and reusable knowledge modules.

## Table of Contents

- [Core Principles](#core-principles)
- [Skill Structure Design](#skill-structure-design)
- [Metadata Writing](#metadata-writing)
- [Content Organization](#content-organization)
- [Examples and Templates](#examples-and-templates)
- [Testing and Validation](#testing-and-validation)
- [Version Management](#version-management)
- [Performance Optimization](#performance-optimization)
- [Common Pitfalls](#common-pitfalls)

## Core Principles

### 1. Single Responsibility

Each Skill should focus on a clear knowledge area.

**Good Examples**:

- `romance-dialogue` - Focuses on romance novel dialogue techniques
- `scene-pacing` - Focuses on scene rhythm control
- `character-consistency` - Focuses on character consistency checks

**Examples to Avoid**:

- `novel-writing-everything` - Tries to cover all writing knowledge
- `tips-and-tricks` - Unclear theme

### 2. Discoverability

The AI needs to be able to easily find and select the correct Skill.

**Key Elements**:

- Clear name (descriptive, easy to understand at a glance)
- Detailed description (explains the purpose)
- Accurate keywords (covers relevant terms)
- Clear whenToUse (explains usage scenarios)

### 3. Actionability

Skill content should provide specific, executable guidance, not abstract theories.

**Actionable Content**:

```markdown
## Dialogue Tag Usage

### When to Omit Tags

- When only two people are talking
- When the speaker is clear from the context
- In a rapid dialogue exchange

### When Tags Must Be Used

- In a conversation with three or more people
- When the speaker's identity might be confusing
- When the speaker's emotion or action needs to be emphasized
```

**Avoid Being Too Abstract**:

```markdown
## Dialogue Techniques

Dialogue is an important part of a novel, and good dialogue can advance the plot.
```

### 4. Moderate Scope

Skill content should be detailed but not lengthy.

**Appropriate Length**:

- Metadata: Concise and clear
- Main content: 500-2000 lines of Markdown
- Complex Skills: Can be split into multiple related Skills

**Signs That Splitting Is Needed**:

- Content exceeds 3000 lines
- Contains multiple unrelated topics
- Directory hierarchy exceeds 4 levels
- The AI has difficulty locating key information quickly after activation

## Skill Structure Design

### Standard Directory Structure

```
.agent/skills/romance-writing/
├── SKILL.md # Main Skill file (required)
├── examples/ # Example files (recommended)
│ ├── dialogue.md
│ └── scene.md
├── templates/ # Template files (optional)
│ └── chapter-outline.md
└── assets/ # Other resources (optional)
    └── character-worksheet.pdf
```

### Basic Structure of SKILL.md

```markdown
---
# === YAML Frontmatter (Metadata) ===
name: skill-name
description: Short description (1-2 sentences)
version: 1.0.0
keywords:
 - keyword1
 - keyword2
whenToUse: Detailed description of usage scenarios
requiredModes:
 - mode1
 - mode2
---

# Skill Title

## Overview

Briefly introduce the purpose and value of this Skill.

## Core Concepts

### Concept 1

Detailed explanation

### Concept 2

Detailed explanation

## Practical Guide

### Step 1

Specific operation

### Step 2

Specific operation

## Examples

Practical application examples

## Frequently Asked Questions

FAQ in Q&A format

## References

Related links and further reading
```

## Metadata Writing

### name

**Specification**:

- Use kebab-case (lowercase letters, hyphen-separated)
- 3-5 words are ideal
- Be descriptive, avoid abbreviations

**Good Examples**:

```yaml
name: romance-dialogue-techniques
name: character-arc-design
name: plot-twist-strategies
```

**Avoid**:

```yaml
name: RomanceDialogue # Used uppercase
name: rd # Too short
name: very-long-name-that-describes-everything # Too long
```

### description

**Specification**:

- 1-2 sentences
- Explain the core value of the Skill
- Include key terms

**Template**:

```
[Verb] + [Object] + [Purpose/Method]
```

**Example**:

```yaml
# Good
description: Provides techniques and specifications for creating romance novel dialogue, including emotional expression, subtext, and pacing.

# Avoid being too short
description: Dialogue techniques

# Avoid being too long
description: This Skill provides a comprehensive guide to creating romance novel dialogue, covering everything from basic dialogue tag usage to advanced subtext techniques, as well as detailed explanations and numerous examples of emotional expression, pacing, and character voice shaping.
```

### version

Follow Semantic Versioning:

```yaml
version: MAJOR.MINOR.PATCH
```

**Version Update Rules**:

- `MAJOR`: Major changes, incompatible modifications
- `MINOR`: New features, backward compatible
- `PATCH`: Bug fixes, minor improvements

**Example Changelog**:

```markdown
## v2.0.0 (2025-11-15)

- Refactor: Completely rewrote the dialogue techniques chapter
- Breaking Change: Removed the old classification system

## v1.2.0 (2025-10-20)

- New: Subtext techniques chapter
- New: 10 new examples

## v1.1.1 (2025-10-10)

- Fix: Corrected errors in the examples
- Improvement: Clarified ambiguous instructions
```

### keywords

**Specification**:

- 5-15 keywords
- Include synonyms and related terms
- Cover different levels of abstraction

**Strategy**:

```yaml
keywords:
 # Core terms
 - romance
 - dialogue

 # Synonyms
 - conversation
 - speech

 # Related concepts
 - emotion
 - subtext
 - character voice

 # Usage scenarios
 - love scene
 - conflict dialogue

 # Technical terms
 - dialogue tag
 - beat
```

**Avoid**:

```yaml
keywords:
 - the # Meaningless word
 - writing # Too broad
 - good # Subjective adjective
```

### whenToUse

**Specification**:

- Clearly state when to use it
- Use the "Use when..." format
- Provide specific scenarios

**Template**:

```yaml
whenToUse: |
 Use when [Scenario 1];
 Use when [Scenario 2];
 Suitable for [Use Case 1], [Use Case 2], and [Use Case 3]
```

**Example**:

```yaml
# Good
whenToUse: |
  Use when creating dialogue scenes for romance novels;
  Use when you need to improve the emotional expression in dialogue;
  Use when reviewing the authenticity and pacing of dialogue;
  Suitable for first draft creation, revision, and consistency checks

# Avoid being too vague
whenToUse: Use when writing dialogue

# Avoid being too specific and restrictive
whenToUse: Only use when writing the cafe dialogue where the male and female leads first meet in the third chapter of a contemporary romance novel
```

### requiredModes

**Specification**:

- Only use when Mode restrictions are necessary
- If not set, it is available in all Modes
- Consider the scope of the Skill

**Example**:

```yaml
# For romance writing only
requiredModes:
 - novelist

# Editing and proofreading tools
requiredModes:
 - editor
 - proofreader

# General Skill - do not set requiredModes
# (Omit this field)
```

### mcpResources (Experimental)

**Purpose**:

- Declare external resources needed by the Skill
- Automatically load when the Skill is activated

**Example**:

```yaml
mcpResources:
 - database://tropes/romance
 - reference://character-archetypes
 - examples://dialogue-samples
```

### allowedToolGroups (Advanced)

**Purpose**:

- Restrict the tools the AI can use when the Skill is activated
- For security or focus control

**Example**:

```yaml
# Read-only analysis Skill
allowedToolGroups:
 - read
 - search

# Full creation Skill
allowedToolGroups:
 - read
 - write
 - edit
 - search
```

## Content Organization

### Hierarchy

**Recommended Heading Levels**:

```markdown
# Skill Main Title (H1 - only one)

## Main Chapters (H2 - 3-7)

### Subchapters (H3 - use as needed)

#### Detailed Points (H4 - use with caution)
```

**Avoid**:

- More than 4 levels of nested headings
- More than one H1 level heading
- Skipping levels (H2 → H4)

### Content Patterns

#### 1. Concept + Explanation + Example

```markdown
## Subtext Techniques

### What is Subtext

Subtext is the true meaning beneath the surface of the dialogue...

### Why It's Important

Subtext can:

- Add depth to dialogue
- Create dramatic tension
- Reveal character personality

### How to Use

#### Technique 1: Contrast Surface and Inner Thoughts

**Surface**: "I'm fine."
**Inner thought**: I'm miserable, but I don't want you to worry.

**Example**:
「"I'm fine." She turned away, avoiding his concerned gaze.」
```

#### 2. Checklist Pattern

```markdown
## Dialogue Review Checklist

### Authenticity Check

- [ ] Does each character's way of speaking match their personality?
- [ ] Have you avoided overly formal language?
- [ ] Are dialects and catchphrases used consistently?

### Pacing Check

- [ ] Is there variation in the pace of the dialogue?
- [ ] Are there appropriate pauses and silences?
- [ ] Are long and short sentences used alternately?
```

#### 3. Comparison Table Pattern

```markdown
## Dialogue Tag Selection

| Scenario | Recommended | Avoid | Reason |
| --- | --- | --- | --- |
| Fast-paced conflict | "said" "stated" | "roared" "yelled" | Let the content convey the emotion |
| Emotional climax | "whispered" "trembled" | "said" | Need to emphasize the manner of speaking |
| Daily conversation | Omit tags | Frequent use | Maintain fluency |
```

#### 4. Step-by-Step Guide Pattern

```markdown
## Steps to Create Conflict Dialogue

### Step 1: Clarify the Core Conflict

Identify the real point of disagreement...

### Step 2: Design an Escalation Path

The conflict should escalate gradually:

1. Polite disagreement
2. Direct confrontation
3. Emotional outburst
4. Key breakthrough

### Step 3: Arrange the Pacing

...
```

### Code and Examples

#### Markdown Examples

Use code blocks to distinguish examples:

````markdown
**Good Example**:

```
"I didn't mean to." She lowered her head, her voice barely audible.

He was silent for a long time, finally just saying, "I know."
```

**Needs Improvement**:

```
"I didn't mean to!" she said loudly, looking very guilty.

"I know," he replied calmly, but he was actually very angry.
```
````

#### Comparison Display

```markdown
## Information Control

### ❌ Information Overload

"Hi, I'm Li Ming, 28 years old, a programmer at a tech company. I like to read and run in my spare time. I just moved to this city recently..."

### ✅ Natural Unfolding

"Hi, I'm Li Ming."

His handshake was firm, and his hands were calloused, not like a typical programmer.

"Just moved here?"

"Last month. Still getting used to the pace here."
```

### Support File References

```markdown
## Example References

For detailed dialogue examples, please refer to:

- [Love Confession Scene](./examples/confession-scene.md)
- [Conflict Dialogue Scene](./examples/conflict-scene.md)

Character dialogue worksheet:

- [Character Voice Style Sheet](./assets/character-voice-worksheet.md)
```

## Examples and Templates

### Complete Skill Example: Romance Dialogue Techniques

```markdown
---
name: romance-dialogue-techniques
description: Techniques and specifications for creating romance novel dialogue, including emotional expression, subtext, and pacing.
version: 1.0.0
keywords:
 - romance
 - dialogue
 - conversation
 - emotion
 - subtext
 - character voice
whenToUse: |
 Use when creating dialogue scenes for romance novels;
 Use when you need to improve the emotional expression in dialogue;
 Use when reviewing the authenticity and pacing of dialogue
requiredModes:
 - novelist
---

# Romance Dialogue Techniques

## Overview

Dialogue is the core of romance novels, carrying the important functions of emotional exchange, relationship development, and character presentation.
This Skill provides systematic guidance for dialogue creation, helping to create authentic, moving, and rhythmic dialogue.

## Core Principles

### 1. Authenticity First

Dialogue should be consistent with the character's personality and social background.

**Key Points**:

- Consider the character's education, profession, and age
- Pay attention to the historical background and regional characteristics
- Avoid all characters speaking in the same way

### 2. Less is More

Concise dialogue is often more powerful.

**Comparison**:

❌ **Lengthy**:
"I really, really like you, from the first time we met, I've been thinking about you, every day..."

✅ **Concise**:
"I miss you." He paused, "Every day."

### 3. The Power of Subtext

The best dialogue has a deeper meaning beneath the surface.

## Detailed Techniques

### Emotional Expression

#### Direct vs. Indirect

**Direct Expression**: Suitable for moments of emotional outburst
```

"I love you."

```

**Indirect Expression**: Suitable for daily interactions, more subtle
```

"I made your favorite porridge."
"It's raining today, remember to bring an umbrella."

```

### Conflict Dialogue

#### Escalation Pattern

1. **Polite Disagreement**
```

"I don't think that's a good idea."
"But we agreed..."

```

2. **Direct Confrontation**
```

"You never listen to me!"
"Because what you're saying makes no sense!"

```

3. **Emotional Outburst**
```

"Enough! I've had enough!"

```

4. **Calm Turning Point**
```

"..."
He took a deep breath: "Let's calm down."

```

### Dialogue Pacing

#### Combining Fast and Slow

**Fast Pacing** (conflict, tension):
```

"Go!"
"Wait--"
"No time!"

```

**Slow Pacing** (emotional, intimate):
```

He looked at her, and it was a long time before he spoke: "Do you remember the first time we met?"

She nodded, without speaking.

"It was raining then." he said softly, "Your dress was all wet."

```

## Frequently Asked Questions

### Q: How to avoid monotonous dialogue tags?

A:
1. Omit tags appropriately (when the context is clear)
2. Use actions instead of tags
3. Vary tag verbs (but don't overdo it)

### Q: How to handle dialects in dialogue?

A:
1. Hint at it rather than fully imitating
2. Use specific vocabulary rather than spelling variations
3. Maintain readability

## Resources

- [Dialogue Example Set](./examples/dialogue-samples.md)
- [Character Voice Style Sheet](./templates/character-voice.md)
```

### Skill Template

A basic template to get you started quickly:

```markdown
---
name: your-skill-name
description: Short description (1-2 sentences)
version: 1.0.0
keywords:
 - keyword1
 - keyword2
 - keyword3
whenToUse: When to use this Skill
---

# Skill Name

## Overview

Briefly explain the purpose of this Skill.

## Core Concepts

### Concept 1

Explanation

### Concept 2

Explanation

## Practical Guide

### Step 1

Detailed explanation

### Step 2

Detailed explanation

## Examples

**Example 1**:
...

**Example 2**:
...

## Frequently Asked Questions

### Q: Question 1?

A: Answer

### Q: Question 2?

A: Answer

## References

- Link 1
- Link 2
```

## Testing and Validation

### Self-Checklist

After creating a Skill, perform the following checks:

#### Metadata Check

- [ ] `name` uses kebab-case
- [ ] `description` is clear and concise (1-2 sentences)
- [ ] `version` follows semantic versioning
- [ ] `keywords` contain 5-15 relevant terms
- [ ] `whenToUse` clearly describes usage scenarios
- [ ] `requiredModes` are set reasonably (if needed)

#### Content Check

- [ ] There is a clear overview chapter
- [ ] The content is organized logically
- [ ] It contains specific, actionable guidance
- [ ] There are practical examples to illustrate key points
- [ ] The heading level does not exceed 4 levels
- [ ] The Markdown format is correct
- [ ] There are no typos or grammatical errors

#### Quality Check

- [ ] The content length is moderate (500-2000 lines)
- [ ] Each chapter focuses on a single topic
- [ ] The examples are authentic and relevant
- [ ] Avoid overly abstract theories
- [ ] The language is clear and easy to understand

#### Usability Check

- [ ] Can the AI find this Skill based on the `description` and `keywords`?
- [ ] Does `whenToUse` accurately describe the usage scenarios?
- [ ] Does the content truly meet the user's needs?
- [ ] Is it easy to quickly locate key information?

### Actual Testing

#### 1. Manual Testing

Talk to the AI to test if the Skill is activated correctly:

**Test Scenario**:

```
User: Help me write a dialogue for a romance novel
AI: [USING SKILL: romance-dialogue-techniques]
 According to the romance dialogue techniques...
```

**Checklist**:

- Does the AI correctly identify the usage scenario?
- Does the AI apply the guidance from the Skill?
- Does the generated content conform to the Skill's specifications?

#### 2. Keyword Testing

Ask questions using different keywords to verify the discoverability of the Skill:

```
Test 1: "write dialogue" → should activate
Test 2: "character dialogue" → should activate
Test 3: "emotional expression" → should activate
Test 4: "scene description" → should not activate (irrelevant)
```

#### 3. Boundary Testing

Test if `requiredModes` works correctly:

```
Current Mode: novelist → should be available
Current Mode: coder → should be unavailable (if requiredModes is set)
```

### Peer Review

If used in a team:

1. **Ask colleagues to review**:
 - Is the metadata clear?
 - Is the content accurate and useful?
 - Are the examples appropriate?

2. **Collect feedback**:
 - Does it meet the expected needs?
 - What needs to be improved?
 - Is there any missing content?

3. **Iterate and improve**:
 - Update the content based on feedback
 - Increment the version number
 - Record the changelog

## Version Management

### Changelog

Add a changelog at the bottom of SKILL.md:

```markdown
---

## Changelog

### v1.2.0 (2025-11-15)

- New: Subtext techniques chapter
- New: 10 new dialogue examples
- Improvement: Reorganized the "Emotional Expression" chapter

### v1.1.0 (2025-10-20)

- New: Dialogue pacing control chapter
- Fix: Corrected punctuation errors in the examples

### v1.0.0 (2025-10-01)

- Initial version release
```

### Git Commit Convention

```bash
# New Skill
git commit -m "feat(skills): add romance-dialogue-techniques skill"

# Update Skill
git commit -m "docs(skills): update romance-dialogue - add subtext section"

# Fix bug
git commit -m "fix(skills): correct examples in romance-dialogue skill"
```

### Version Upgrade Guide

**When to upgrade to a MAJOR version (x.0.0)**:

- Major refactoring that changes the core structure of the Skill
- Removing or significantly modifying important chapters
- Incompatible changes

**When to upgrade to a MINOR version (1.x.0)**:

- Adding new chapters or content
- Enhancing existing features
- Backward-compatible improvements

**When to upgrade to a PATCH version (1.0.x)**:

- Fixing bugs
- Correcting typos
- Minor clarifications and improvements

## Performance Optimization

### Control File Size

**Goal**:

- Single SKILL.md: < 100KB
- Complete Skill with support files: < 500KB

**Optimization Methods**:

1. **Split oversized Skills**:

 ```
 # Before splitting
 novel-writing-complete/
 └── SKILL.md (5000 lines)

 # After splitting
 novel-writing-dialogue/
 ├── SKILL.md (800 lines)
 novel-writing-description/
 ├── SKILL.md (900 lines)
 novel-writing-plot/
 └── SKILL.md (1000 lines)
 ```

2. **Externalize large examples**:

 ```markdown
 # Bad: Inline large examples

 ## Examples

 [5000 lines of example content]

 # Good: Reference external files

 ## Examples

 For detailed examples, please refer to:

 - [Dialogue Example 1](./examples/dialogue-01.md)
 - [Dialogue Example 2](./examples/dialogue-02.md)
 ```

3. **Avoid repetitive content**:
 Use internal links to reference concepts that have already been explained:
 ```markdown
 For character consistency, see the [Character Settings chapter](#character-settings)
 ```

### Optimize Loading Speed

**Strategy**:

1. **Metadata first**: Ensure the frontmatter is concise and clear
2. **Prioritize key content**: Put the most important information at the beginning
3. **Lazy loading**: Use external files for large resources

## Common Pitfalls

### 1. Too Broad

**Problem**:

```yaml
name: writing-tips
description: Various writing tips
keywords: [writing, tips, novel]
```

**Solution**:

```yaml
name: romance-dialogue-techniques
description: Techniques and specifications for creating romance novel dialogue
keywords: [romance, dialogue, emotion, subtext]
```

### 2. Lack of Specificity

**Problem**:

```markdown
## Dialogue Techniques

Dialogue should be well-written, paying attention to authenticity and emotional expression.
```

**Solution**:

```markdown
## Dialogue Techniques

### Authenticity Checklist

- [ ] Does the character's way of speaking match their educational background?
- [ ] Are period-appropriate words used?
- [ ] Are dialect features consistent?

### Methods of Emotional Expression

1. **Direct Expression**: For emotional outbursts
 Example: "I love you."

2. **Indirect Expression**: For daily interactions
 Example: "I made your favorite porridge."
```

### 3. Inaccurate Metadata

**Problem**:

```yaml
whenToUse: Use when writing a novel
```

**Solution**:

```yaml
whenToUse: |
 Use when creating dialogue scenes for romance novels;
 Use when you need to improve the emotional expression in dialogue;
 Use when reviewing the authenticity and pacing of dialogue
```

### 4. Overly Complex

**Problem**:

- More than 5 levels of nested headings
- Using complex terms without explanation
- Trying to cover too many topics

**Solution**:

- Limit heading levels to 3-4
- Define professional terms
- Split into multiple focused Skills

### 5. Lack of Examples

**Problem**:
Purely theoretical explanations without practical examples.

**Solution**:
Provide at least one specific example for each technique:

```markdown
### Technique: Using Silence

**Explanation**:
Appropriate silence can enhance the tension and emotional depth of dialogue.

**Example**:
"Do you still love me?"

He didn't answer, just looked out the window.

After a long time, she said softly, "I see."
```

### 6. Neglecting Maintenance

**Problem**:
Never updated after creation, content becomes outdated.

**Solution**:

- Regular review (quarterly or semi-annually)
- Collect usage feedback
- Update version numbers
- Record changelog

## Quick Reference

### Skill Creation Workflow

```
1. Determine the theme and scope
 ↓
2. Write metadata
 ↓
3. Create a content outline
 ↓
4. Fill in the detailed content
 ↓
5. Add examples
 ↓
6. Self-check
 ↓
7. Actual testing
 ↓
8. Publish and monitor
 ↓
9. Collect feedback
 ↓
10. Iterate and improve
```

### Metadata Cheat Sheet

| Field | Required | Format | Description |
| --- | --- | --- | --- |
| name | ✅ | kebab-case | 3-5 words |
| description | ✅ | String | 1-2 sentences |
| version | ✅ | x.y.z | Semantic version |
| keywords | Recommended | Array | 5-15 words |
| whenToUse | Recommended | String | Detailed scenarios |
| requiredModes | Optional | Array | List of Mode names |
| mcpResources | Optional | Array | Resource URI |
| allowedToolGroups | Optional | Array | Tool group names |

### Content Organization Cheat Sheet

| Element | Purpose | Example |
| --- | --- | --- |
| # H1 | Skill Title | `# Romance Dialogue Techniques` |
| ## H2 | Main Chapter | `## Core Principles` |
| ### H3 | Subchapter | `### Authenticity First` |
| #### H4 | Detailed Point | `#### Technique 1` |
| List | List of points | `- Point 1` |
| Code Block | Example display | ` ```Example``` ` |
| Table | Comparison | `\| A \| B \|` |
| Quote | Emphasis | `> Important Note` |

## Community Resources

### Reference Excellent Skills

Browse the NovelWeave Skills Registry for inspiration:

- https://github.com/wordflowlab/novelweave-skills-registry

### Get Help

- [Discord Community](https://discord.gg/novelweave) - Discussion and Q&A
- [GitHub Issues](https://github.com/wordflowlab/novelweave/issues) - Report issues
- [Documentation Website](https://docs.novelweave.com) - Full documentation

### Share Your Skills

Consider sharing your excellent Skills with the community:

1. Fork the Skills Registry repository
2. Add your Skill
3. Submit a Pull Request
4. Wait for review and merge

---

**Version**: v0.13.0
**Last Updated**: 2025-10-19

## Appendix: Complete Example Skill

Here is a complete Skill example that follows all best practices:

### File Structure

```
.agent/skills/character-consistency/
├── SKILL.md
├── examples/
│ ├── personality-tracking.md
│ └── appearance-consistency.md
└── templates/
 └── character-profile.md
```

### SKILL.md Full Content

```markdown
---
name: character-consistency-checker
description: Checks and maintains character consistency in a novel, including personality, appearance, abilities, and behavior patterns.
version: 1.0.0
keywords:
 - character
 - consistency
 - continuity
 - personality
 - appearance
 - behavior
whenToUse: |
 Use when reviewing a novel to check for character consistency;
 Use when you find that a character's behavior or description may be contradictory;
 Use when creating a new chapter and need to ensure character settings are consistent
requiredModes:
 - novelist
 - editor
---

# Character Consistency Check

## Overview

Character consistency is a key element of a novel's quality. This Skill provides a systematic checking method,
helping to find and fix contradictions in character settings, behavior, appearance, etc.

## Core Check Dimensions

### 1. Appearance Consistency

#### Checklist

**Basic Features**:

- [ ] Height and body shape descriptions are consistent
- [ ] Hair color and style do not change without explanation
- [ ] Eye color remains consistent
- [ ] Special marks (scars, birthmarks, etc.) are persistent

**Clothing Style**:

- [ ] Clothing style is consistent with the character's setting
- [ ] Special accessories (such as a frequently worn necklace) are consistent

**Age and Appearance**:

- [ ] Appearance description matches the timeline
- [ ] Age changes are reasonable (if there is a time span)

#### Common Errors

❌ **Contradictory Example**:
```

Chapter 1: "She had long, black hair."
Chapter 5: "Her golden curls shimmered in the sun."

```

✅ **Correction Method**:
1. If a change is needed, explain it clearly:
```

"She had dyed her hair from black to gold."

```
2. Or maintain consistency:
```

Chapter 5: "Her long, black hair shone in the sun."

```

### 2. Personality Consistency

#### Core Personality Trait Tracking

Create a personality profile for each main character:

| Trait Dimension | Setting | Key Behavior |
| --- | --- | --- |
| Introvert/Extrovert | Introvert | Avoids large social gatherings |
| Decision Making | Primarily rational | Important decisions are based on logical analysis |
| Emotional Expression | Restrained | Rarely shows emotions directly |
| Behavior Pattern | Cautious | Acts with a detailed plan |

#### Checking Method

**Scenario Test**:
```

If the character is in a similar situation, is their behavior consistent?

Chapter 3 scene: Calm and rational in a business negotiation
Chapter 7 scene: Suddenly makes an impulsive decision in another business situation ❌

Unless there is a clear character development or a special reason.

```

**Dialogue Style Test**:
- Is vocabulary choice consistent (formal/casual)?
- Is the tone consistent with the personality (humorous/serious)?
- Are speech habits consistent (catchphrases, way of speaking)?

### 3. Ability Consistency

#### Skills and Knowledge

**Checklist**:
- [ ] Professional skill level is consistent
- [ ] Scope of knowledge is consistent with education and experience
- [ ] Skill improvement has a reasonable learning process

**Example Analysis**:

❌ **Inconsistent**:
```

Chapter 2: She can't cook, she can even burn noodles.
Chapter 6: She made an exquisite French meal.
(No learning process in between)

```

✅ **Consistent (with growth)**:
```

Chapter 2: She can't cook.
Chapter 4: For him, she started to learn to cook.
Chapter 6: After a few months of practice, she made a decent French meal.

```

### 4. Behavior Pattern Consistency

#### Habitual Behaviors

Record and track:
- Small movements when nervous
- Habits when thinking
- Catchphrases and specific phrases
- Daily routines

**Example**:
```

Setting: The protagonist bites their nails when nervous

Chapter 1: She bit her nails, staring at the exam results. ✅
Chapter 8: Before the interview, she nervously bit her nails. ✅

```

#### Values and Stances

**Core values should be stable**, unless a major event changes them:

```

Character's core value: Honesty and integrity

❌ Suddenly lies and deceives (without sufficient reason)
✅ Faces a moral dilemma, but ultimately chooses honesty
✅ Values change after a major betrayal (with foreshadowing)

````

## Checking Process

### Step 1: Create a Character Profile

Use the [Character Profile Template](./templates/character-profile.md):

```markdown
# [Character Name] Profile

## Basic Information
- Name:
- Age:
- Occupation:

## Appearance
- Height:
- Body type:
- Hair style and color:
- Special marks:

## Personality Traits
- Core personality:
- Behavior patterns:
- Way of speaking:

## Abilities and Skills
- Professional skills:
- Special abilities:
- Weaknesses:
````

### Step 2: Scene Comparison Check

Check chapter by chapter, comparing with the profile:

1. Mark all scenes where the character appears
2. Extract the character's description and behavior in that scene
3. Compare with the profile to find contradictions

### Step 3: Timeline Verification

```
Event 1: Chapter 3, character is 18 years old
Event 2: Chapter 10, described as "three years later"
Event 3: Following Chapter 10, the character should be 21 years old

Check: Do age, appearance, and skill development match the timeline?
```

### Step 4: Correct Contradictions

After finding a contradiction, choose a correction strategy:

| Strategy | Applicable Scenario | Example |
| --- | --- | --- |
| Unify Description | Simple description error | Unify hair color description |
| Add Transition | Need to rationalize a change | Add a learning process |
| Adjust Plot | Contradiction affects the plot | Modify the event sequence |

## Practical Tools

### Consistency Checklist

Check after completing each chapter:

```markdown
## [Chapter X] Consistency Check

### Character A

- [ ] Appearance description is consistent
- [ ] Personality expression is consistent with settings
- [ ] Ability usage is reasonable
- [ ] Behavior is consistent with motivation

### Character B

- [ ] Appearance description is consistent
- [ ] Personality expression is consistent with settings
- [ ] Ability usage is reasonable
- [ ] Behavior is consistent with motivation
```

### Character Relationship Diagram

Track the changes in relationships between characters:

```
Initial: A and B are strangers
Chapter 5: Begin to cooperate
Chapter 10: Become friends
Chapter 15: Trust is established

Check: Does the interaction style match the relationship progress?
```

## Frequently Asked Questions

### Q: How to balance character growth and consistency?

A:

- Consistency does not mean static
- Characters can grow, but it requires:
 1. A reasonable triggering event
 2. A gradual process of change
 3. Retention of core traits

**Example**:

```
Character core: Kind (maintained)
Initial behavior: Easily deceived (can be changed)
After growth: Still kind, but has learned to protect themselves
```

### Q: How to handle differences in a character's perspective from different people?

A:

- Core features should be consistent
- Different perspectives can have different sides
- But they should not be completely contradictory

**Example**:

```
A's perspective: B is cold (B is indeed cold to A) ✅
C's perspective: B is enthusiastic (B is closer to C) ✅

But:
A's perspective: B is blonde (objective fact)
C's perspective: B is brunette (contradiction) ❌
```

## Advanced Techniques

### Personality Arc Tracking

Record the character's internal change trajectory:

```
Chapter 1: [Fear] → Chapter 5: [Hesitation] → Chapter 10: [Attempt] → Chapter 15: [Mastery]

The behavior at each stage should match the internal state.
```

### Multi-Character Consistency Matrix

| Chapter | Character A State | Character B State | Relationship State |
| --- | --- | --- | --- |
| 1-3 | Closed, defensive | Curious, proactive | Strangers, probing |
| 4-7 | Gradually opening up | Continuously approaching | Cooperation, getting closer |
| 8-10 | Trust established | Emotionally invested | Intimate, complex |

## Resources

- [Personality Tracking Example](./examples/personality-tracking.md)
- [Appearance Consistency Example](./examples/appearance-consistency.md)
- [Character Profile Template](./templates/character-profile.md)

---

## Changelog

### v1.0.0 (2025-10-01)

- Initial version release
- Includes four core check dimensions
- Provides a complete checking process and tools

```

This example shows:
- ✅ Complete metadata
- ✅ Clear structure
- ✅ Specific, actionable guidance
- ✅ Rich examples
- ✅ Practical tools and templates
- ✅ FAQ
- ✅ Version management

---

We hope this best practices guide helps you create high-quality Agent Skills!
