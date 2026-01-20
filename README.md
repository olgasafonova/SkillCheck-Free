# SkillCheck Free

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-green?logo=anthropic&logoColor=white)](https://docs.anthropic.com/en/docs/claude-code)

[![skillcheck passed](https://raw.githubusercontent.com/olgasafonova/skillcheck-free/main/skill-check/passed.svg)](https://getskillcheck.com)

Validate Claude Code skills against the [agentskills specification](https://agentskills.io). Catch issues before your users do.

## Why SkillCheck?

Agent Skills are now an open standard adopted by Claude Code, Cursor, and dozens of coding agents. Your skill needs to work everywhere and follow best practices.

SkillCheck validates:
- Does the YAML frontmatter follow the spec?
- Will it trigger when users actually need it?
- Are the instructions clear and unambiguous?

## Installation

Copy the `skill-check` folder to your Claude Code skills directory:

```bash
cp -r skill-check ~/.claude/skills/
```

Or clone directly:

```bash
git clone https://github.com/olgasafonova/SkillCheck-Free.git
cp -r SkillCheck-Free/skill-check ~/.claude/skills/
```

## Usage

In Claude Code, say any of:
- "skillcheck my skill"
- "check skill at path/to/SKILL.md"
- "validate my skills"

## Example Output

```
## SkillCheck Results: my-awesome-skill

### Summary
- Critical: 1 | Warnings: 2 | Suggestions: 3 | Passed: 44

### Critical Issues
**[1.2-desc-what]** Line 3: Description missing action verb
**Fix**: Start description with Create, Generate, Build, Convert, etc.

### Warnings
**[4.2-ambiguous-term]** Line 47: Vague term "several" found
**Fix**: Specify exact count or range

### Strengths
- Skill includes example section
- Skill documents prerequisites
- Description includes activation triggers
```

## What It Checks

### Structure (1.x)
| Check | What It Catches |
|-------|-----------------|
| 1.1-name | Invalid name format, reserved words |
| 1.2-desc | Missing WHAT (action verb) or WHEN (trigger) |
| 1.3-tools | Unknown or deprecated tool formats |
| 1.4-category | Invalid category format |

### Body (2.x)
| Check | What It Catches |
|-------|-----------------|
| 2.1-lines | File too long (500+ lines warning) |
| 2.3-date | Hardcoded dates that will go stale |
| 2.4-empty | Empty sections with no content |

### Naming (3.x)
| Check | What It Catches |
|-------|-----------------|
| 3.1-vague | Generic names like "helper", "utils" |
| 3.2-length | Names too short or too long |
| 3.3-single | Single-word names lacking specificity |

### Semantic (4.x)
| Check | What It Catches |
|-------|-----------------|
| 4.1-contradiction | Conflicting instructions |
| 4.2-ambiguous | Vague terms like "several", "appropriate" |
| 4.3-output | Output mentioned but no format specified |

### Quality Patterns (8.x) - Strengths
SkillCheck also recognizes good practices:

| Check | What It Recognizes |
|-------|-------------------|
| 8.1 | Has example section |
| 8.2 | Documents error handling or limitations |
| 8.3 | Description includes activation triggers |
| 8.4 | Specifies output format with examples |
| 8.5 | Uses structured instructions (numbered steps) |
| 8.6 | Documents prerequisites |

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| Critical | Skill may not function | Must fix |
| Warning | Best practice violation | Should fix |
| Suggestion | Could be improved | Nice to have |
| Strength | Good practice detected | Keep it up |

## Free vs Pro

| Feature | Free | Pro |
|---------|------|-----|
| Structure validation | Yes | Yes |
| Body & naming checks | Yes | Yes |
| Semantic consistency | Yes | Yes |
| Quality patterns (basic) | Yes | Yes |
| Anti-slop detection | - | Yes |
| Security scanning | - | Yes |
| Token budget analysis | - | Yes |
| WCAG accessibility | - | Yes |
| Enterprise readiness | - | Yes |
| Quality patterns (advanced) | - | Yes |
| MCP server integration | - | Yes |
| CI/CD binary | - | Yes |

**Get Pro**: [getskillcheck.com](https://getskillcheck.com)

## Prerequisites

- Claude Code (or any AI assistant with file Read capability)
- Works on macOS, Linux, Windows
- No dependencies required

## How It Works

SkillCheck Free is itself a skill. When you say "skillcheck", Claude reads the SKILL.md file containing all validation rules and applies them to your target skill. No external API calls, no binaries - just Claude following instructions.

## Contributing

Found a bug or have a suggestion? Open an issue at [github.com/olgasafonova/SkillCheck-Free/issues](https://github.com/olgasafonova/SkillCheck-Free/issues)

## Links

- Website: [getskillcheck.com](https://getskillcheck.com)
- Specification: [agentskills.io](https://agentskills.io)
- Pro version: [SkillCheck Pro](https://getskillcheck.com)

## License

MIT - see [LICENSE](LICENSE)

---

Built by [Olga Safonova](https://github.com/olgasafonova)
