---
name: skill-check
version: 3.1.0
description: Validate Claude Code skills against Anthropic guidelines. Use when user says "check skill", "skillcheck", "validate SKILL.md", or asks to find issues in skill definitions. Contains complete validation knowledge.
license: MIT
allowed-tools: Read Glob
category: development
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
| `name` | Yes | Lowercase, hyphens only, 1-64 chars |
| `description` | Yes | WHAT + WHEN pattern, 1-1024 chars |

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
2. **WHEN**: Trigger phrase describing when to invoke the skill (can appear anywhere in description)

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
- Before/After and Good/Bad comparison lines
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

---

# Pro Tier Features

The following checks are available with [SkillCheck Pro](https://getskillcheck.com):

## Anti-Slop Detection (Pro)

Detects AI writing patterns that waste tokens and reduce clarity:
- Em-dash abuse
- Hedge stacking ("might potentially")
- Sycophantic openers ("Great question!")
- Filler phrases ("It's important to note that")
- Cliche intros ("In today's fast-paced world")
- Essay closers ("In conclusion")

**Why it matters**: Skills that use slop patterns teach Claude bad habits. Pro catches 7 anti-patterns.

## Token Budget Analysis (Pro)

Ensures skills stay within optimal size limits:
- Frontmatter: 400 tokens max
- Body: 5,000 tokens optimal, 8,000 max
- Total: 15,000 tokens max

**Why it matters**: Oversized skills consume context window and slow Claude down.

## Security Scanning (Pro)

Detects security issues in technical skills:
- Hardcoded secrets and API keys
- Command injection vulnerabilities
- PII in examples (emails, phone numbers, SSNs)
- Unsafe path patterns

**Why it matters**: Public skills with leaked credentials are a liability.

## Workflow Clarity (Pro)

Validates instruction structure:
- Complex skills (2000+ tokens) should use numbered steps
- Clear task decomposition

**Why it matters**: Well-structured instructions perform better.

## Enterprise Readiness (Pro)

Checks for team/org deployment:
- No hardcoded user paths
- Environment variable configuration
- Permission documentation
- Audit trail support

**Why it matters**: Enterprise skills need to work across different environments.

## WCAG Compliance (Pro)

For visual-output skills:
- Color contrast ratios (4.5:1 for AA)
- Non-color indicators
- AI slop aesthetic detection

**Why it matters**: Accessible output works for everyone.

---

## Error Handling

Troubleshooting guide for validation failures.

### Common Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| "No YAML frontmatter" | Missing `---` markers | Add frontmatter block at file start |
| "Missing required field: name" | No name in frontmatter | Add `name: your-skill-name` |
| "Invalid name format" | Uppercase, underscore, or special chars | Use lowercase letters, numbers, hyphens only |
| "Description missing WHEN trigger" | No activation phrase | Add "Use when..." clause |
| "Unknown tool in allowed-tools" | Typo or invalid tool name | Check tool spelling, use space separation |

### Timeout Behavior

- Validation completes in under 2 seconds for files under 1000 lines
- Large skills (1000+ lines) may hit context limits
- If validation stalls, break skill into smaller modules

### Recovery Steps

1. Run validation on individual sections if full validation fails
2. Check frontmatter syntax first (most common failure point)
3. Use `--verbose` flag with MCP server for detailed diagnostics

---

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| Critical | Skill may not function | Must fix |
| Warning | Best practice violation | Should fix |
| Suggestion | Could be improved | Nice to have |

---

## Reporting Format

```markdown
## SkillCheck Results: [skill-name]

### Summary
- Critical: X | Warnings: Y | Suggestions: Z | Passed: N

### Critical Issues
**[Check ID]** Line N: [Issue description]
**Fix**: [How to resolve]
```

### Badge Output

When a skill passes with 0 critical issues, include this badge snippet:

```markdown
### Badge

Add this to your README:

[![skillcheck passed](https://raw.githubusercontent.com/olgasafonova/skillcheck-free/main/skill-check/passed.svg)](https://getskillcheck.com)
```

This links to getskillcheck.com when clicked.

---

## Check IDs Reference

| ID | Category | Tier | Description |
|----|----------|------|-------------|
| 1.0-dir-* | Structure | Free | Directory structure issues |
| 1.1-name-* | Structure | Free | Name field issues |
| 1.2-desc-* | Structure | Free | Description issues |
| 1.3-tools-* | Structure | Free | allowed-tools issues |
| 1.4-category-* | Structure | Free | Category field issues |
| 2.*-body-* | Body | Free | File length, format issues |
| 3.*-name-* | Naming | Free | Name quality issues |
| 4.*-* | Semantic | Free | Logic/contradiction/output format/routing issues |
| 5.*-slop-* | Anti-Slop | **Pro** | Writing pattern issues |
| 5.4-pii-* | Security | **Pro** | PII detection issues |
| 6.*-wcag-* | WCAG | **Pro** | Accessibility issues |
| 7.*-security-* | Security | **Pro** | Path/credential/injection issues |
| 9.*-token-* | Tokens | **Pro** | Budget issues |
| 10.*-enterprise-* | Enterprise | **Pro** | Org deployment issues |
| 12.*-workflow-* | Workflow | **Pro** | Step-by-step instruction issues |

---

## Upgrade to Pro

Get the complete validation suite at [getskillcheck.com](https://getskillcheck.com):

- **Go binary** for CI/CD integration
- **MCP server** for IDE integration (protocol 2024-11-05)
- **All Pro checks** (anti-slop, security, tokens, WCAG, enterprise)
- **Auto-fix** suggestions
- **Badge generation** for marketplace-ready skills

```json
{
  "mcpServers": {
    "skillcheck": {
      "command": "skillcheck-mcp",
      "env": { "SKILLCHECK_LICENSE": "your-license-key" }
    }
  }
}
```
