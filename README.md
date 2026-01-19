# SkillCheck Free

Validate Claude Code skills against the [agentskills specification](https://agentskills.io).

## Installation

Copy the `skill-check` folder to your Claude Code skills directory:

```bash
cp -r skill-check ~/.claude/skills/
```

## Usage

In Claude Code, say:
- "skillcheck my skill"
- "check my skill for issues"
- "validate SKILL.md"

## What It Checks

| Category | Focus |
|----------|-------|
| Structure | YAML frontmatter, required fields |
| Body | Content requirements, formatting |
| Naming | Conventions, reserved words |
| Semantic | Contradictions, clarity |

## Want More?

[SkillCheck Pro](https://getskillcheck.com) adds 6 extra check categories:

- Anti-slop detection
- Security scanning
- Token optimization
- WCAG compliance
- Enterprise readiness
- Workflow clarity

## Links

- Website: https://getskillcheck.com
- Specification: https://agentskills.io

## License

MIT
