---
name: skill-check
description: Validate Claude Code skills against Anthropic guidelines. Use when user says "check skill", "skillcheck", "validate SKILL.md", or asks to find issues in skill definitions. Covers structural and semantic validation. Do NOT use for anti-slop detection, security scanning, token analysis, enterprise checks, or Eval Kit generation; use skill-check-pro for those. Do NOT use for LinkedIn skill engagement; use skillcheck-engage for that.
license: MIT
allowed-tools: Read Glob
category: development
compatibility: claude-code
metadata:
  version: 3.20.0
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

### Artifact Passing Validation

> **Note**: `produces` and `consumes` are Claude Code extensions for inter-skill artifact passing. Do not flag missing fields. Only validate format if present.

**Format**: Comma-separated list of artifact type names. Each type must be lowercase with hyphens only.

**Known artifact types**: `content-brief`, `knowledge-note`, `reading-log-entry`, `sift-article`

**Check 1.10-artifact-types** (Warning): If `produces` or `consumes` contains a type not in the known list above, flag as a warning (not an error). New types are valid but should be registered in `rules/artifact-passing.md`.

**Check 1.11-consumes-without-tools** (Warning): If a skill declares `consumes:` but its `allowed-tools` does not include `Read` or `Glob`, flag as a warning. Consuming artifacts requires reading files.

<example type="valid">
produces: content-brief, knowledge-note
</example>

<example type="valid">
consumes: content-brief
</example>

<example type="invalid">
produces: Content_Brief
reason: uppercase and underscore not allowed
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
3. **Vague imperatives**: `"Ensure quality"`, `"maintain standards"`, `"strive for best practices"`

**Exceptions** (not flagged):
- Content inside code blocks or blockquotes
- Content in example/usage/pattern sections
- Before/After and positive/negative comparison lines

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

### Description Trigger Style

**Check 4.8-description-trigger-style** (Suggestion): The description field should read as a trigger condition, not a capability summary. Claude scans descriptions to decide "is there a skill for this request?" A trigger-oriented description activates correctly; a summary-oriented one gets overlooked.

**Detection**: Description opens with summary patterns instead of trigger patterns:
- Summary openers (flag): "This skill", "A tool that", "Provides", "Offers", "Handles", "Manages", "Enables"
- Trigger openers (pass): "Use when", "Generate", "Build", "Convert", "Extract", "Validate", "Run", "Create", "Find", "Search"

**Does NOT fire when**:
- Description has a summary opener but also contains a WHEN trigger phrase later ("Handles X. Use when user says Y" is fine)
- Description starts with an action verb

**Severity**: Suggestion

<example type="valid">
description: Generate weekly reports from Azure DevOps data. Use when user says "weekly update" or asks for stakeholder summaries.
</example>

<example type="invalid">
description: This skill generates weekly reports from Azure DevOps data and provides stakeholder summaries.
reason: Reads as capability summary, not trigger condition. Claude won't route to this reliably.
</example>

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

<example type="valid">
## Configuration

The config file lives at `~/.config/myskill/config.json`. If missing, Claude asks the user for the required values.

Common options:
- `output_dir`: where reports land (default: current directory)
- `format`: json or markdown (default: markdown)
</example>

<example type="invalid">
## Configuration

You must always check the config file at `~/.config/myskill/config.json`. You must never deviate from this path. Always do exactly as follows: first read the config, then validate every field. You must always use JSON format. Never change the output directory.

reason: 5 prescriptive phrases; give Claude the information and let it adapt
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

### 8.9 Has Gotchas Section

Skill documents common failure points, edge cases, or footguns. Per Anthropic's internal skill design guidance, gotchas sections are "the highest-signal content in any skill."

**Patterns detected**: Section headers matching "Gotchas", "Pitfalls", "Common Mistakes", "Watch Out", "Known Issues", "Caveats", "Footguns", "Common Errors"

**Strength**: "Skill documents gotchas or common failure points"

---

# Plugin Manifest Validation (Category 24)

When the user provides a `.claude-plugin/plugin.json` file (Claude Code plugin manifest) instead of a SKILL.md, validate the manifest against Anthropic's reference schema.

**When to apply:** the input file path ends in `.claude-plugin/plugin.json`, or the user explicitly asks to "check this plugin" or "validate plugin manifest".

**Reference schema (Anthropic):** four required top-level fields. Any rule that rejects Anthropic's own reference plugins is mis-calibrated.

```json
{
  "name": "product-management",
  "version": "1.2.0",
  "description": "Write feature specs, plan roadmaps...",
  "author": { "name": "Anthropic" }
}
```

### Check 24.1 — name format

**Rule:** `name` is required and must be kebab-case: lowercase letters, digits, and hyphens; must start with a letter; no leading/trailing hyphens; no double hyphens.

**Detection regex:** `^[a-z][a-z0-9]*(-[a-z0-9]+)*$`

**Severity:** Critical. **Fix:** rename to lowercase-with-hyphens, e.g. `product-management`.

### Check 24.2 — version is semver

**Rule:** `version` is required and must be canonical semver (`MAJOR.MINOR.PATCH`, optional pre-release or build metadata). No `v` prefix.

**Severity:** Critical. **Fix:** use `1.0.0`, not `v1.0` or `1.0`.

### Check 24.3 — description present

**Rule:** `description` is required and must be non-empty.

**Severity:** Critical. **Fix:** add a one-paragraph summary of what the plugin does.

### Check 24.4 — author.name present

**Rule:** `author.name` is required and must be non-empty.

**Severity:** Critical. **Fix:** add `"author": { "name": "Your Name" }`.

### Check 24.5 — command name collision

**Rule:** files in `commands/<name>.md` must not collide with bundled Claude Code slash commands.

**Reserved names:** `simplify`, `batch`, `debug`, `loop`, `claude-api`, `security-review`.

**Severity:** Critical. **Fix:** rename the colliding command file.

### Check 24.6 — `.claude-plugin/` directory layout

**Rule:** `.claude-plugin/` must contain only `plugin.json`. Skills, commands, hooks, and agents live at the plugin root, not nested under `.claude-plugin/`.

**Severity:** Warning. **Fix:** move misplaced subdirectories to the plugin root.

### Out of scope (Pro tier)

- Cross-plugin dedup detection (description similarity)
- Naming convention enforcement against an org's role taxonomy
- Change-gate eval integration
- Maintainers and deprecation provenance recommendations
- Cross-skill dependency graphs

These ship in [SkillCheck Pro Cat 24](https://getskillcheck.com).

---

# MCP Tool List Analysis (Category 23, Free hook)

When the user provides an MCP server's `tools/list` response (a JSON file with the standard MCP shape `{"tools": [{"name": ..., "description": ...}, ...]}`), surface two Cat 23 (Agent Integration Readiness) signals.

**When to apply:** the input file looks like an MCP `tools/list` response, OR the user asks to "check this MCP server's tools" or "is this server too big".

### Check 23.1-tool-count-high

**Rule:** servers exposing more than 20 tools should consider intent grouping or code-orchestration.

**Severity:** Warning. **Fix:** group related operations into intent-shaped tools, or expose a `search_tools` + `execute` pair (Cloudflare reference: 2 tools total).

### Check 23.1-tool-count-strong

**Rule:** servers exposing more than 40 tools are strong candidates for the search+execute pattern.

**Severity:** Warning. **Fix:** replace the tool surface with a 2-tool search+execute architecture.

### Check 23.1-api-mirror

**Rule:** when a single noun appears with 3 or more CRUD verb prefixes (`list_`, `get_`, `create_`, `update_`, `delete_`, `add_`, `remove_`, `set_`, `fetch_`, `patch_`), the tool surface looks like a 1:1 OpenAPI mirror rather than intent-shaped tools.

**Detection:** for each tool name matching `^[a-z]+_(.+)$` where the prefix is in the API verb list, group by the noun. Flag any noun with 3+ distinct verb prefixes.

**Severity:** Warning. **Fix:** rename tools toward intent (e.g. `find_user` or `manage_user_roles`) instead of mirroring REST verbs.

### Out of scope (Pro tier)

- CIMD-based OAuth detection
- MCP Apps usage detection (resources, prompts, links)
- Code-orchestration adoption check (search_tools + execute pattern presence)
- Server-delivered skills pairing (Canva/Notion/Sentry cohort)
- Remote vs local availability declaration
- Destructive flag annotations per tool
- User-vs-agent distinction enforcement

These ship in [SkillCheck Pro Cat 23](https://getskillcheck.com) (Agent Integration Readiness, four pillars from Sam Morrow's framework populated with detection heuristics from Anthropic's six MCP production patterns).

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
| 4.*-*, 4.6-wisdom, 4.8-trigger-style, 4.9-railroading | Semantic | Free |
| 5.*-slop-*, 5.4-pii-* | Anti-Slop / Security | **Pro** |
| 6.*-wcag-* | WCAG | **Pro** |
| 7.*-security-* | Security | **Pro** |
| 9.*-token-*, 10.*-enterprise-* | Tokens / Enterprise | **Pro** |
| 12.*-workflow-* | Workflow | **Pro** |
| 13-18.*-readiness-* | Agent Readiness | **Pro** |
| 19.1-pattern-detected | Design Pattern Classification | Free |
| 19.2-19.7 pattern-* | Design Pattern Deep Checks | **Pro** |
| 20.*-collision-* | Trigger Collision Detection | **Pro** |

---

## 5. Design Pattern Classification

SkillCheck classifies each skill into one of five design patterns from the Google ADK taxonomy:

- **Reviewer**: evaluates output against criteria (e.g., skill-check, code-review)
- **Generator**: produces structured artifacts from templates (e.g., linkedin-post, brand-assets)
- **Inversion**: asks user questions before acting (e.g., grill-me, feature-scoping)
- **Pipeline**: chains multiple steps with checkpoints (e.g., sift, tapestry)
- **Tool Wrapper**: wraps an API with context-aware instructions (e.g., lmwtfy)

**Check 19.1-pattern-detected** (Strength): Pattern identified. Reports primary pattern and any secondary patterns. Most skills are hybrids.

**Pro checks 19.2-19.7**: Validate pattern-specific requirements. A Reviewer without criteria, a Generator without output format, an Inversion without question framework, a Pipeline without stages, or a Tool Wrapper without API boundaries.

---

## Autonomy Design

**Stop condition**: Stop after reporting all issues for the target SKILL.md. Do not iterate or re-check unless the user requests it.

**Budget**: Maximum attempts: 1. Single SKILL.md validation completes in one pass with maximum retries: 0. No looping.

**Idempotency**: Safe to re-run. Same input produces the same severity counts and issue list.

## Composability

**Input/Output contract**:
- Input: expects: path to a SKILL.md file or directory containing one.
- Output: returns: structured report with overall score (0-100), issue list (check_id, severity, line, message, fix), and passed count.

**Output structure**:

```
# SkillCheck Report: {skill-name}
## Summary (score, issue/strength counts)
## Issues (check_id, severity, line, message, fix per issue)
## Strengths (check_id, detail per strength)
```

**Mode**: Runs identically standalone or in composition with other skills. Self-contained, read-only, no side effects.

## Platform Requirements

- Code Execution: No
- File Creation: No
- Network Access: No
- Required tools: Read, Glob

## Observability

**Confidence level**: High confidence on structural and pattern-based checks. Semantic checks (contradiction detection, wisdom/platitude) are heuristic; ~5% false positive rate.

**Progress**: Step 1 of 4: Parse. Step 2 of 4: Validate. Step 3 of 4: Score. Step 4 of 4: Report.

## Phase Boundaries

Phase 1: Parse — Read file, extract frontmatter.
Phase 2: Validate — Run all Free tier checks.
Phase 3: Score — Calculate overall score.
Phase 4: Report — Format and return results.

---

## Gotchas

- YAML frontmatter with multi-line descriptions may trigger false positives on line-based checks. Use a single-line description or pipe (`|`) syntax.
- Skills using non-standard frontmatter fields (custom extensions) get informational notices, not errors. These are expected and safe to ignore.
- Check 4.6 (wisdom/platitude detection) has a ~5% false positive rate on technical content that uses advisory language. If flagged incorrectly, the content is fine; the heuristic is conservative.
- The "misplaced routing" check (4.4) fires on body content that contains trigger phrases. If your body has a "Quick Start" section showing example invocations, wrap them in code blocks or `<example>` tags to avoid false positives.

---

## Upgrade to Pro

Get the complete suite at [getskillcheck.com](https://getskillcheck.com): Go binary for CI/CD, MCP server for IDE integration, all Pro checks, auto-fix, and badge generation.

Skills can also be distributed via the `/v1/skills` API endpoint. See [agentskills.io](https://agentskills.io) for the specification.
