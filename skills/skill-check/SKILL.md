---
name: skill-check
description: Validate Claude Code skills against Anthropic guidelines. Use when user says "check skill", "skillcheck", "validate SKILL.md", or asks to find issues in skill definitions. Covers structural and semantic validation. Do NOT use for anti-slop detection, security scanning, token analysis, enterprise checks, or Eval Kit generation; use skill-check-pro for those. Do NOT use for LinkedIn skill engagement; use skillcheck-engage for that.
license: MIT
allowed-tools: Read Glob
category: development
compatibility: claude-code
metadata:
  version: 3.20.1
  author: olgasafonova
---

# SkillCheck (Free)

Check skills against Anthropic guidelines and the agentskills specification. This file contains Free tier validation rules.

**Want deeper analysis?** [Upgrade to Pro](https://getskillcheck.com) for anti-slop detection, security scanning, token optimization, WCAG compliance, enterprise checks, and Eval Kit (auto-generated test prompts for your skills).

## Prerequisites

- Any AI assistant with file Read capability (Claude Code, Cursor, Windsurf, Codex CLI)
- Works on any platform (Unix/macOS/Windows)
- No special tools required (Read-only)

## How to Check a Skill

1. **Locate**: Find target SKILL.md file(s)
2. **Read**: Load the content
3. **Validate**: Apply each rule section below
4. **Report**: List issues found with severity and fixes

---

# Free Tier Validation Rules

> Worked valid/invalid examples for every check below are in [references/examples.md](references/examples.md). The rules are here; the examples are one file away to keep this file scannable.

Apply these checks in order. Each subsection defines patterns to match and issues to flag.

## 1. Frontmatter Structure

Every SKILL.md must start with YAML frontmatter between `---` markers.

### Required Fields

| Field | Required | Rules |
|-------|----------|-------|
| `name` | Yes | Lowercase, hyphens only, 1-64 chars, no reserved words |
| `description` | Yes | WHAT + WHEN pattern, 1-1024 chars |

### Frontmatter Security

**Check 1.9-xml-in-frontmatter** (Critical): Frontmatter values must not contain XML angle brackets (`<` or `>`). Frontmatter appears in Claude's system prompt; angle brackets could enable prompt injection.

**Detection**: Scan all frontmatter string values (name, description, compatibility, etc.) for `<` or `>` characters.

**Fix**: Remove angle brackets from frontmatter. Use plain text descriptions. Markdown formatting and XML tags are fine in the SKILL.md body.

### Optional Fields (Spec)

Fields defined in the [agentskills.io](https://agentskills.io) specification:

| Field | Purpose |
|-------|---------|
| `license` | License name or reference to bundled license file |
| `allowed-tools` | Tools the skill can use (space-separated or YAML list) |
| `compatibility` | Platform compatibility info (max 500 chars) |
| `metadata` | Additional key-value pairs |

### Claude Code Extensions

Recognized by Claude Code but not part of the agentskills.io spec. Other agents may ignore these fields.

| Field | Purpose |
|-------|---------|
| `category` | Skill domain(s) for discovery and filtering |
| `model` | Override model (full ID like claude-opus-4-6 or alias: opus, sonnet, haiku) |
| `effort` | Reasoning effort level: low, medium, or high |
| `maxTurns` | Maximum agent turns (positive integer; warns above 100) |
| `disallowedTools` | Tools the skill must not use (space-separated or YAML list) |
| `context` | Run context ("fork" for sub-agent) |
| `agent` | Agent type when context: fork |
| `hooks` | Lifecycle hooks (PreToolUse, PostToolUse, Stop) |
| `user-invocable` | Show in slash menu (default: true) |
| `disable-model-invocation` | Manual-only skill |
| `produces` | Artifact types this skill outputs (comma-separated) |
| `consumes` | Artifact types this skill reads from other skills (comma-separated) |

### Community Extensions

Not part of any spec. Used by community tools and registries.

| Field | Purpose |
|-------|---------|
| `type` | Skill type indicator |
| `author` | Skill author |
| `date` | Creation/update date |
| `argument-hint` | Hints for skill arguments |

### Category Validation

> **Note**: `category` is a Claude Code extension, not part of the agentskills.io spec. Do not flag a missing category field. Only validate format if present.

**Format**: String or array of strings, lowercase letters, numbers, and hyphens only.

**Pattern**: `^[a-z][a-z0-9-]*[a-z0-9]$` (same rules as skill name)

**Common categories**: `development`, `productivity`, `data`, `automation`, `writing`, `design`, `security`, `devops`, `api`, `testing`, `documentation`, `legal`, `financial`, `marketing`, `ai-ml`

### Artifact Passing Validation

> **Note**: `produces` and `consumes` are Claude Code extensions for inter-skill artifact passing. Do not flag missing fields. Only validate format if present.

**Format**: Comma-separated list of artifact type names. Each type must be lowercase with hyphens only.

**Known artifact types**: `content-brief`, `knowledge-note`, `reading-log-entry`, `sift-article`

**Check 1.10-artifact-types** (Warning): If `produces` or `consumes` contains a type not in the known list above, flag as a warning (not an error). New types are valid but should be registered in `rules/artifact-passing.md`.

**Check 1.11-consumes-without-tools** (Warning): If a skill declares `consumes:` but its `allowed-tools` does not include `Read` or `Glob`, flag as a warning. Consuming artifacts requires reading files.

### Name Validation

**Pattern**: `^[a-z][a-z0-9-]*[a-z0-9]$`

**Naming suggestions**: Avoid generic terms that don't describe what the skill does: `helper`, `utils`, `tools`, `misc`, `stuff`, `things`, `manager`, `handler`. Product-specific terms (`claude`, `anthropic`, `mcp`) are allowed but may limit portability across agents.

### Description Validation

Must contain:
1. **WHAT**: Action verb explaining what skill does
2. **WHEN**: Trigger phrase for when to use it
3. **Key capabilities** (recommended): Specific tasks or file types handled

**Recommended structure**: `[What it does] + [When to use it] + [Key capabilities]`

**Action verbs**: Create, Generate, Build, Convert, Extract, Analyze, Transform, Process, Validate, Format, Export, Import, Parse, Search, Find

**WHEN triggers**: "Use when", "Use for", "Use this when", "Invoke when", "Activate when", "Triggers on", "Auto-activates", "Run when", "Applies to", "Helps with"

### allowed-tools Validation

Both space-separated (`allowed-tools: Read Glob`) and YAML list formats are valid. See references/examples.md for both shapes.

### Directory Structure Validation

Skills can include optional subdirectories per the agentskills spec:

| Directory | Purpose | Validation |
|-----------|---------|------------|
| `references/` | Additional docs (REFERENCE.md, etc.) | Files should be .md format |
| `scripts/` | Executable code (Python, Bash, JS) | Should have execute permissions |
| `assets/` | Static resources (templates, data) | No validation required |

**Check 1.10-readme-in-folder** (Warning): Skill folder must not contain a `README.md` file. All documentation goes in SKILL.md or `references/`. For GitHub distribution, place the README at the repo root, outside the skill folder.

**Detection**: Use Glob to check if `{skill-dir}/README.md` exists.

**Skill path formats supported**:
- Standard: `~/.claude/skills/{skill-name}/SKILL.md`
- Namespaced: `~/.claude/skills/{namespace}/{skill-name}/SKILL.md`

**Namespace support**: Namespaces allow organizing skills by source (personal, team, project):
- `~/.claude/skills/internal/weekly-reports/SKILL.md`
- `~/.claude/skills/shared/code-review/SKILL.md`

**Directory name must match skill name**: The parent directory name must exactly match the `name` field in frontmatter.

---

## 2. Naming Quality

Names should be descriptive compounds, not single words.

**Length Guidelines**: Minimum 3 chars, optimal 10-30 chars, maximum 64 chars.

### Anti-Pattern Format Lint

**Check 2.8-antipattern-format** (Suggestion): When a skill documents anti-patterns (sections with headers matching "anti-pattern", "what not to do", "avoid", "common mistakes", "bad practices", "pitfalls"), the content should use structured formats (tables or bullet lists) rather than wall-of-text prose.

**Fires when**:
- Anti-pattern section has a long prose line (100+ chars) containing don't/avoid/never
- Anti-pattern section has 3+ prose lines with 3+ avoidance directives

**Does NOT fire when**:
- Section already uses tables (`| col | col |`) or bullet lists (`- item`)
- Section header doesn't match anti-pattern keywords

---

## 3. Semantic Checks

Validate logical consistency and clarity of skill instructions.

### Contradiction Detection

Flag conflicting instructions that simultaneously require and forbid the same action.

### Ambiguous Terms

Flag vague language that should be more specific. Terms like "multiple items" or "correct settings" lack precision. Use exact counts or specific criteria instead.

**Exceptions** (not flagged):
- Terms inside code blocks or blockquotes
- Content in example/usage/pattern sections
- Before/After and correct/incorrect comparison lines
- Terms followed by qualifiers (e.g., "some specific files")

### Output Format Specification

Skills that mention output should specify format with concrete examples.

**Detection**:
- Skill mentions "output/returns/produces" without `## Output` section
- Has output section but lacks code blocks, JSON, or tables

### Wisdom/Platitude Detection

**Check 4.6-wisdom-platitude** (Suggestion): Detects generic advice ("wisdom") that lacks actionable content. Skills should contain concrete instructions, not motivational prose.

**Three detection layers**:

1. **Opener patterns**: Lines starting with wisdom phrases like "Remember that", "It's important to", "Keep in mind that", "Think about", "Never forget that", "Always keep in mind", "Consider the importance of"
2. **Platitude structures**: Mid-line "[noun] is essential/crucial/important to [noun]" patterns
3. **Vague imperatives**: `"Ensure quality"`, `"maintain standards"`, `"strive for best practices"`

**Exceptions** (not flagged):
- Content inside code blocks or blockquotes
- Content in example/usage/pattern sections
- Before/After and positive/negative comparison lines

### Description Trigger Style

**Check 4.8-description-trigger-style** (Suggestion): The description field should read as a trigger condition, not a capability summary. Claude scans descriptions to decide "is there a skill for this request?" A trigger-oriented description activates when the request matches; a summary-oriented one gets overlooked.

**Detection**: Description opens with summary patterns instead of trigger patterns:
- Summary openers (flag): "This skill", "A tool that", "Provides", "Offers", "Handles", "Manages", "Enables"
- Trigger openers (pass): "Use when", "Generate", "Build", "Convert", "Extract", "Validate", "Run", "Create", "Find", "Search"

**Does NOT fire when**:
- Description has a summary opener but also contains a WHEN trigger phrase later ("Handles X. Use when user says Y" is fine)
- Description starts with an action verb

**Severity**: Suggestion

**Fix**: Rewrite to lead with action verb and include "Use when" clause. The description is a routing instruction, not documentation.

### Railroading Detection

**Check 4.9-railroading** (Suggestion): Skills should give Claude information and context, not dictate exact sequences. Excessive prescriptive language reduces Claude's ability to adapt to the user's actual situation.

**Detection**: Count prescriptive phrases outside example blocks, code blocks, and anti-pattern sections:
- "you must always", "always do exactly", "never deviate", "follow these exact steps", "do not change this", "this is the only way", "you are required to"

**Fires when**: 5+ prescriptive phrases in non-example content.

**Does NOT fire when**:
- Prescriptive language is inside code blocks, blockquotes, or example tags
- Prescriptive language is in anti-pattern/gotchas sections (where it describes what NOT to do)
- Skill is a safety-critical skill (security, compliance) where strict instructions are warranted

**Severity**: Suggestion

### Misplaced Routing Content

**Check 4.4**: Body contains trigger conditions that belong in the description field.

**Detection**: Body contains a heading matching `## When to Use` or `## When to Use This Skill`, or body text contains routing phrases like "Activate when user", "Trigger this skill when", "Use this skill when".

**Problem**: The skill body loads only AFTER the Skill tool is invoked. Trigger conditions placed here don't influence routing decisions. Claude reads the `description` field during routing; that's where "Use when" patterns, trigger keywords, and example phrases belong.

**Severity**: Warning

**Fix**: Merge unique trigger content from the body section into the `description` field, then remove the redundant body section.

### Hollow Content

**Check 22.7-hollow-content** (Suggestion): A gotchas/troubleshooting section that contains only generic filler and no concrete knowledge is hollow. It promises hard-won advice but delivers platitudes.

**Detection**: In a `## Gotchas` / `## Troubleshooting` / `## Tips` / `## Caveats` / `## Pitfalls` section, fire when 3+ lines match generic filler (`follow team standards`, `ensure proper handling`, `handle appropriately`, `consider relevant factors`, `use appropriate methods`, `maintain quality`) AND no line carries a concrete knowledge signal (a specific threshold/number-with-unit, a consequence "X because Y", a numbered debugging step, or a file/function reference).

**Exceptions** (not flagged): content inside code blocks; a section that includes at least one concrete threshold, consequence, or debugging step.

### Restraint Without Safety Carve-Out

**Check 28.2-restraint-without-carveout** (Warning): If a skill instructs restraint/minimalism but never carves out validation, security, or accessibility, flag it. Restraint without that carve-out can reward skipping non-negotiables, not just avoiding gold-plating. "Lazy, not negligent" is the line.

**Detection**: A restraint/anti-overbuild directive is present (YAGNI, "keep it minimal", "don't over-build / over-engineer", "simplest thing that works", "resist the urge to add", "prefer the stdlib"), AND no line pairs a keep/never-cut cue ("never cut", "always keep", "still required", "non-negotiable") with a safety noun (validation, security, accessibility / a11y). An incidental mention of "security" or "validation" *without* a keep cue does NOT count as a carve-out.

**Does NOT fire when**: no restraint directive is present; or an explicit carve-out clause exists.

**Severity**: Warning

---

## 4. Quality Patterns (Strengths)

Recognize positive patterns in skills. These are reported as "strengths" rather than issues.

### 8.1 Has Example Section

