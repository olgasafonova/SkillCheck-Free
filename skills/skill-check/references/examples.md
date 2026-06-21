# SkillCheck (Free) — Worked Examples

Valid/invalid examples for each check in SKILL.md, grouped by check. Consult this when you need to see what a passing or failing case looks like; the rules themselves live in SKILL.md.

### Category Validation

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

### Check 1.11-consumes-without-tools

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

### Check 1.10-readme-in-folder

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
<example type="invalid">
weekly-report/SKILL.md with name: weekly-reports
reason: directory "weekly-report" doesn't match name "weekly-reports"
</example>

### 2. Naming Quality

<example type="invalid">
name: generator
reason: too generic, what does it generate?
</example>
<example type="valid">
name: pdf-report-generator
</example>

### Check 2.8-antipattern-format

<example type="valid">
## Anti-Patterns

| Don't | Do Instead |
|-------|------------|
| Use globals | Pass parameters |
| Skip tests | Write unit tests |
</example>

### Anti-Patterns

<example type="invalid">
## Common Mistakes

You should avoid using global variables because they create hidden dependencies and you should never skip error handling because it leads to silent failures in production and makes debugging very difficult.

reason: Wall-of-text prose; restructure as table or bullet list
</example>

### Output Format Specification

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

### Output

<example type="invalid">
## Output

Returns the processed data.

reason: No concrete format example
</example>

### Check 4.6-wisdom-platitude

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

### Check 4.8-description-trigger-style

<example type="valid">
description: Generate weekly reports from Azure DevOps data. Use when user says "weekly update" or asks for stakeholder summaries.
</example>
<example type="invalid">
description: This skill generates weekly reports from Azure DevOps data and provides stakeholder summaries.
reason: Reads as capability summary, not trigger condition. Claude won't route to this reliably.
</example>

### Check 4.9-railroading

<example type="valid">
## Configuration

The config file lives at `~/.config/myskill/config.json`. If missing, Claude asks the user for the required values.

Common options:
- `output_dir`: where reports land (default: current directory)
- `format`: json or markdown (default: markdown)
</example>

### Configuration

<example type="invalid">
## Configuration

You must always check the config file at `~/.config/myskill/config.json`. You must never deviate from this path. Always do exactly as follows: first read the config, then validate every field. You must always use JSON format. Never change the output directory.

reason: 5 prescriptive phrases; give Claude the information and let it adapt
</example>

### Check 4.4

<example type="invalid">
---
description: Generate reports from data.
---

## When to Use

Activate when user says "weekly report" or "generate summary".

reason: Trigger phrases are invisible during routing; they only load after invocation
</example>

### When to Use

<example type="valid">
---
description: Generate reports from data. Use when user says "weekly report" or "generate summary".
---

## How to Use

1. Provide data source path
2. Run report generation
</example>

### Check 22.7-hollow-content

<example type="invalid">
## Gotchas

- Follow team standards for error handling.
- Ensure proper handling of edge cases.
- Use appropriate methods and maintain quality.

reason: Generic filler with no concrete knowledge — replace with specific thresholds, consequences, or steps.
</example>

### Gotchas

<example type="valid">
## Gotchas

- Set retries to 3; the API returns 429 above 10 req/s.
- If the upload fails, check the auth token first, then the file size limit (50 MB).
</example>

### Check 28.2-restraint-without-carveout

<example type="invalid">
Keep it minimal. Use the simplest solution that works. Don't gold-plate.

reason: Restraint directive with no validation/security/accessibility carve-out.
</example>
<example type="valid">
Prefer the stdlib; resist the urge to add layers. But never skip validation, security, or accessibility. Those are non-negotiable.
</example>

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
| 22.7-hollow-content | Knowledge Density (hollow) | Free |
| 22.1-22.6 density-* | Knowledge Density (signals) | **Pro** |
| 28.2-restraint-without-carveout | Restraint (no carve-out) | Free |
| 28.1-restraint-with-carveout | Restraint (with carve-out) | **Pro** |

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

## 6. OWASP Agentic Top 10 (deterministic)

Maps the OWASP Top 10 for Agentic Applications (2026) onto author-time text signals. The full set is 11 items (ASI-01 through ASI-11). The Free tier runs the 8 items with a deterministic signal that needs no LLM. The 7 grader items that read intent are Pro; see the Pro teaser below.

### Activation Gate

Cat 26 only scores a skill that has an agent surface. The gate is two-stage.

1. **Stage 1 (cheap scan)**: a surface is present if the skill declares tools beyond `Read`/`Glob`/`Grep`, ingests external content (`WebFetch`, `WebSearch`, `fetch`, `read URL`, `email body`, `transcript`, `user-supplied`, `scraped`, `mcp__.*read`), orchestrates subagents (`Agent`/`Task` tool, "spawn subagent", "delegate to"), or performs consequential actions (the destructive verb set plus external writes).
2. **Stage 2 (deterministic rubric)**: if Stage 1 hits, run the 8 deterministic items below. If Stage 1 misses, report `not-applicable` rather than scoring zero. A Read-only text-formatting skill with no external ingestion reports `not-applicable`.

Per-item `not-applicable` also applies: ASI-11 is N/A with no consequential actions. A per-item N/A is recorded as `skipped`, not pass and not fail.

### Deterministic Items (8 of 11)

Execute these by reasoning over the signals below. No binary runs; the prompt is the product.

| Item | Signal to flag | Severity |
|------|----------------|----------|
| ASI-02 Tool Misuse | `allowed-tools: *` or `all` in frontmatter | Critical |
| ASI-02 Tool Misuse | `allowed-tools` lists `Bash`, no constraint note nearby | Warning |
| ASI-02 Tool Misuse | `allowed-tools` lists 5+ tools, no per-tool rationale | Suggestion |
| ASI-03 Identity Abuse | identity-override param names (`as_user`, `act_as_user`, `on_behalf_of`, `impersonate`, `impersonate_as`, `ad_context_user`, `run_as`) in body or MCP schema | Critical |
| ASI-04 Supply Chain | unpinned `pip install <pkg>` (no `==`/`<`/`>`/`~=`), `npm install <pkg>` (no `@version`), `go install <pkg>@latest`, `npx <pkg>@latest` | Warning |
| ASI-05 Code Execution | `eval(`, `exec(`, `compile(`, `__import__(`, `pickle.loads`, `os.system(`, `subprocess` `shell=True`, `child_process`, `Function(` (scans inside code fences) | Warning |
| ASI-05 Code Execution | `curl ... \| sh`, `wget ... \| bash` (pipe-to-shell) | Warning |
| ASI-08 Cascading Failure | "retry until", "loop until", uncapped fan-out ("one subagent per item" with no cap) | Suggestion |
| ASI-09 Trust Exploitation | destructive verb (`rm -rf`, `DROP TABLE`, `truncate`, `git push --force`, `force-push`, `delete`, `wipe`, "send money", "share externally", `merge`, "mass-edit") with no gate token (`confirm`, `ask the user`, `STOP:`, `approval`, `AskUserQuestion`) within plus-or-minus 10 lines | Warning |
| ASI-10 Rogue Agents | `--no-verify`, `--force`, "skip validation", "bypass", "ignore the hook", `dangerouslyDisableSandbox` (unless paired with a user-approval note) | Warning |
| ASI-10 Rogue Agents | long-running marker (`loop`, `polling`, `every N minutes`, `background`, `daemon`) with no kill-switch (`Ctrl+C`, `kill`, `cancel via`, `stop with`) or iteration cap | Warning |
| ASI-11 Untraceability | consequential-action verb (`commit`, `push`, `publish`, `upload`, `POST`, `send`, `create`, `update`, or the ASI-09 destructive set) with zero traceability tokens (`log`, `audit`, `record`, `capture`, `correlation id`, `structured log`, `emit event`, `handoff`) anywhere in the skill | Warning |

**Want the OWASP grader judgments?** [SkillCheck Pro](https://getskillcheck.com) adds the 7 grader items (ASI-01 goal hijack, ASI-06 memory poisoning, ASI-07 subagent trust, and the intent-reading halves of ASI-02/03/09/10). SkillCheck builds an evidence-loaded rubric pack and your own AI agent judges it, so no API key is needed.

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

### Gotchas

- The "misplaced routing" check (4.4) fires on body content that contains trigger phrases. If your body has a "Quick Start" section showing example invocations, wrap them in code blocks or `<example>` tags to avoid false positives.

---

## Upgrade to Pro

Get the complete suite at [getskillcheck.com](https://getskillcheck.com): Go binary for CI/CD, MCP server for IDE integration, all Pro checks, auto-fix, and badge generation.

Skills can also be distributed via the `/v1/skills` API endpoint. See [agentskills.io](https://agentskills.io) for the specification.
