---
name: skill-check
description: Validate Claude Code skills against Anthropic guidelines. Use when user says "check skill", "skillcheck", "validate SKILL.md", or asks to find issues in skill definitions. Covers structural and semantic validation. Do NOT use for anti-slop detection, security scanning, token analysis, or enterprise checks; use skill-check-pro for those.
license: MIT
allowed-tools: Read Glob
category: development
compatibility: claude-code
metadata:
  version: 3.12.0
  author: olgasafonova
---

# SkillCheck (Free)

Check skills against Anthropic guidelines and the agentskills specification. This file contains Free tier validation rules.

**Want deeper analysis?** [Upgrade to Pro](https://getskillcheck.com) for anti-slop detection, security scanning, token optimization, WCAG compliance, and enterprise checks.

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
| `model` | Override model (claude-*-YYYYMMDD format) |
| `context` | Run context ("fork" for sub-agent) |
| `agent` | Agent type when context: fork |
| `hooks` | Lifecycle hooks (PreToolUse, PostToolUse, Stop) |
| `user-invocable` | Show in slash menu (default: true) |
| `disable-model-invocation` | Manual-only skill |

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

<example type="valid">
category: development
</example>

<example type="valid">
category:
  - development
  - automation
</example>

<example type="invalid">
category: Dev_Tools
reason: uppercase and underscore not allowed
</example>

<example type="invalid">
category: my--category
reason: consecutive hyphens not allowed
</example>

### Name Validation

**Pattern**: `^[a-z][a-z0-9-]*[a-z0-9]$`

**Naming suggestions**: Avoid generic terms that don't describe what the skill does: `helper`, `utils`, `tools`, `misc`, `stuff`, `things`, `manager`, `handler`. Product-specific terms (`claude`, `anthropic`, `mcp`) are allowed but may limit portability across agents.

<example type="valid">
name: weekly-report-generator
</example>

<example type="invalid">
name: helper-tool
reason: vague term "helper"
</example>

<example type="invalid">
name: Claude_Skill
reason: uppercase and underscore not allowed
</example>

### Description Validation

Must contain:
1. **WHAT**: Action verb explaining what skill does
2. **WHEN**: Trigger phrase for when to use it
3. **Key capabilities** (recommended): Specific tasks or file types handled

**Recommended structure**: `[What it does] + [When to use it] + [Key capabilities]`

**Action verbs**: Create, Generate, Build, Convert, Extract, Analyze, Transform, Process, Validate, Format, Export, Import, Parse, Search, Find

**WHEN triggers**: "Use when", "Use for", "Use this when", "Invoke when", "Activate when", "Triggers on", "Auto-activates", "Run when", "Applies to", "Helps with"

<example type="valid">
description: Generate weekly reports from Azure DevOps data. Use when user says "weekly update" or asks for stakeholder summaries.
</example>

<example type="valid">
description: Helps with code review workflows for Pull Requests.
</example>

<example type="invalid">
description: A tool for reports.
reason: no WHAT verb, no WHEN trigger
</example>

### allowed-tools Validation

Both space-separated and YAML list formats are valid:

<example type="valid">
allowed-tools: Read Glob Bash
</example>

<example type="valid">
allowed-tools:
  - Read
  - Glob
  - Bash
</example>

<example type="invalid">
allowed-tools: Read, Glob, Bash
reason: comma separation is deprecated; use spaces or YAML list
</example>

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

<example type="valid">
my-skill/
├── SKILL.md
├── references/
│   └── advanced-usage.md
└── scripts/
    └── helper.py
</example>

<example type="invalid">
my-skill/
├── skill.md          # Wrong: must be SKILL.md (case-sensitive)
└── refs/             # Warning: use references/ not refs/
</example>

**Directory name must match skill name**: The parent directory name must exactly match the `name` field in frontmatter.

<example type="invalid">
weekly-report/SKILL.md with name: weekly-reports
reason: directory "weekly-report" doesn't match name "weekly-reports"
</example>

---

## 2. Naming Quality

Names should be descriptive compounds, not single words.

<example type="invalid">
name: generator
reason: too generic, what does it generate?
</example>

<example type="valid">
name: pdf-report-generator
</example>

**Length Guidelines**: Minimum 3 chars, optimal 10-30 chars, maximum 64 chars.

### Anti-Pattern Format Lint

**Check 2.8-antipattern-format** (Suggestion): When a skill documents anti-patterns (sections with headers matching "anti-pattern", "what not to do", "avoid", "common mistakes", "bad practices", "pitfalls"), the content should use structured formats (tables or bullet lists) rather than wall-of-text prose.

**Fires when**:
- Anti-pattern section has a long prose line (100+ chars) containing don't/avoid/never
- Anti-pattern section has 3+ prose lines with 3+ avoidance directives

**Does NOT fire when**:
- Section already uses tables (`| col | col |`) or bullet lists (`- item`)
- Section header doesn't match anti-pattern keywords

<example type="valid">
## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Use globals | Pass parameters |
| Skip tests | Write unit tests |
</example>

<example type="invalid">
## Common Mistakes

You should avoid using global variables because they create hidden dependencies and you should never skip error handling because it leads to silent failures in production and makes debugging very difficult.

reason: Wall-of-text prose; restructure as table or bullet list
</example>

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

<example type="valid">
## Output

Returns JSON:

```json
{
  "status": "success",
  "items": ["a", "b", "c"]
}
```
</example>

<example type="invalid">
## Output

Returns the processed data.

reason: No concrete format example
</example>

### Wisdom/Platitude Detection

**Check 4.6-wisdom-platitude** (Suggestion): Detects generic advice ("wisdom") that lacks actionable content. Skills should contain concrete instructions, not motivational prose.

**Three detection layers**:

1. **Opener patterns**: Lines starting with wisdom phrases like "Remember that", "It's important to", "Keep in mind that", "Think about", "Never forget that", "Always keep in mind", "Consider the importance of"
2. **Platitude structures**: Mid-line "[noun] is essential/crucial/important to [noun]" patterns
3. **Vague imperatives**: "Ensure quality", "maintain standards", "strive for best practices"

**Exceptions** (not flagged):
- Content inside code blocks or blockquotes
- Content in example/usage/pattern sections
- Before/After and Good/Bad comparison lines

<example type="invalid">
Remember that code quality is important.
Testing is essential to software development.
Always ensure quality in your deliverables.

reason: Generic advice; replace with specific, actionable directives
</example>

<example type="valid">
Run golangci-lint before committing.
Write at least one test per exported function.
Set timeout to 30 seconds for HTTP requests.
</example>

### Misplaced Routing Content

**Check 4.4**: Body contains trigger conditions that belong in the description field.

**Detection**: Body contains a heading matching `## When to Use` or `## When to Use This Skill`, or body text contains routing phrases like "Activate when user", "Trigger this skill when", "Use this skill when".

**Problem**: The skill body loads only AFTER the Skill tool is invoked. Trigger conditions placed here don't influence routing decisions. Claude reads the `description` field during routing; that's where "Use when" patterns, trigger keywords, and example phrases belong.

**Severity**: Warning

**Fix**: Merge unique trigger content from the body section into the `description` field, then remove the redundant body section.

<example type="invalid">
---
description: Generate reports from data.
---

## When to Use

Activate when user says "weekly report" or "generate summary".

reason: Trigger phrases are invisible during routing; they only load after invocation
</example>

<example type="valid">
---
description: Generate reports from data. Use when user says "weekly report" or "generate summary".
---

## How to Use

1. Provide data source path
2. Run report generation
</example>

---

## 4. Quality Patterns (Strengths)

Recognize positive patterns in skills. These are reported as "strengths" rather than issues.

### 8.1 Has Example Section

Skills with `## Example`, `## Usage`, or `<example>` tags demonstrate expected behavior clearly.

**Strength**: "Skill includes example section"

### 8.2 Has Error Handling

Skills documenting limitations, error cases, or edge cases set correct expectations.

**Patterns detected**: `## Error`, `## Limitation`, `does not support`, `will fail if`

**Strength**: "Skill documents error handling or limitations"

### 8.3 Has Trigger Phrases

Description includes activation triggers (Use when, Triggers on, Applies to, etc.)

**Strength**: "Description includes activation triggers"

### 8.4 Has Output Format

Skills specifying output format with concrete examples (code blocks, JSON, tables).

**Strength**: "Skill specifies output format with examples"

### 8.5 Has Structured Instructions

Skills using numbered steps or clear workflow sections.

**Strength**: "Skill uses structured instructions"

### 8.6 Has Prerequisites

Skills documenting setup requirements or dependencies.

**Strength**: "Skill documents prerequisites"

### 8.7 Has Negative Triggers

Description includes scope boundaries that prevent over-triggering.

**Patterns detected**: "Do NOT use for", "Not for", "Don't use when", "not intended for", "Do not use for"

**Strength**: "Description includes negative triggers to prevent over-triggering"

---

# Pro Tier Features

Available with [SkillCheck Pro](https://getskillcheck.com):

| Check | What it catches |
|-------|----------------|
| Anti-Slop | Em-dash abuse, hedge stacking, sycophantic openers, filler phrases, cliche intros, essay closers |
| Token Budget | Frontmatter >400 tokens, body >5K optimal / >8K max, total >15K |
| Security | Hardcoded secrets, command injection, PII in examples, unsafe paths |
| Workflow | Missing numbered steps in complex skills (2K+ tokens) |
| Enterprise | Hardcoded user paths, missing env var config, permission gaps |
| WCAG | Color contrast <4.5:1, color-only indicators, AI slop aesthetics |

---

## Error Handling

| Error | Fix |
|-------|-----|
| "No YAML frontmatter" | Add `---` markers at file start |
| "Missing required field: name" | Add `name: your-skill-name` |
| "Invalid name format" | Use lowercase letters, numbers, hyphens only |
| "Description missing WHEN trigger" | Add "Use when..." clause |
| "Unknown tool in allowed-tools" | Check spelling, use space separation |

If validation stalls on large files (1000+ lines), break the skill into smaller modules or check frontmatter syntax first.

---

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| Critical | Skill may not function | Must fix |
| Warning | Best practice violation | Should fix |
| Suggestion | Could be improved | Nice to have |

---

## Check IDs Reference

| ID | Category | Tier |
|----|----------|------|
| 1.0-dir-*, 1.1-name-*, 1.2-desc-* | Structure | Free |
| 1.3-tools-*, 1.4-category-* | Structure | Free |
| 1.9-xml-in-frontmatter, 1.10-readme | Structure | Free |
| 2.*-body-*, 2.8-antipattern-format | Body | Free |
| 3.*-name-* | Naming | Free |
| 4.*-*, 4.6-wisdom-platitude | Semantic | Free |
| 5.*-slop-*, 5.4-pii-* | Anti-Slop / Security | **Pro** |
| 6.*-wcag-* | WCAG | **Pro** |
| 7.*-security-* | Security | **Pro** |
| 9.*-token-*, 10.*-enterprise-* | Tokens / Enterprise | **Pro** |
| 12.*-workflow-* | Workflow | **Pro** |

---

## Autonomy Design

**Stop condition**: Stop after reporting all issues for the target SKILL.md. Do not iterate or re-check unless the user requests it.

**Budget**: Maximum attempts: 1. Single SKILL.md validation completes in one pass with maximum retries: 0. No looping.

**Idempotency**: Safe to re-run. Same input produces the same severity counts and issue list.

## Composability

**Input/Output contract**:
- Input: expects: path to a SKILL.md file or directory containing one.
- Output: returns: structured report with overall score (0-100), issue list (check_id, severity, line, message, fix), and passed count.

**Mode**: Runs identically standalone or in composition with other skills. Self-contained, read-only, no side effects.

## Observability

**Confidence level**: High confidence on structural and pattern-based checks. Semantic checks (contradiction detection, wisdom/platitude) are heuristic; ~5% false positive rate.

**Progress**: Step 1 of 4: Parse. Step 2 of 4: Validate. Step 3 of 4: Score. Step 4 of 4: Report.

## Phase Boundaries

Phase 1: Parse — Read file, extract frontmatter.
Phase 2: Validate — Run all Free tier checks.
Phase 3: Score — Calculate overall score.
Phase 4: Report — Format and return results.

---

## Upgrade to Pro

Get the complete suite at [getskillcheck.com](https://getskillcheck.com): Go binary for CI/CD, MCP server for IDE integration, all Pro checks, auto-fix, and badge generation.

Skills can also be distributed via the `/v1/skills` API endpoint. See [agentskills.io](https://agentskills.io) for the specification.
