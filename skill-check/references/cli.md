# SkillCheck CLI (v2.0.0)

A standalone Go CLI tool with additional features beyond the skill.

## Installation

```bash
# From source
go install github.com/olgasafonova/skillcheck-cli/cmd/skillcheck@latest

# Or download binary from GitHub releases
# https://github.com/olgasafonova/skillcheck-cli/releases
```

## CLI Features

| Feature | Command | Description |
|---------|---------|-------------|
| Check | `skillcheck check <name>` | Run all checks (same as this skill) |
| Auto-fix | `skillcheck fix <name> --apply` | Automatically fix common issues |
| Templates | `skillcheck init --template visual` | Bootstrap new skills from templates |
| Baselines | `skillcheck baseline save <name>` | Save results for regression testing |
| Dashboard | `skillcheck dashboard` | Generate HTML report for all skills |
| Badges | `skillcheck badge <name>` | Generate shields.io-style SVG badges |

## Auto-Fixable Issues

The CLI can automatically fix:
- Name too long (truncates to 64 chars)
- Uppercase names (converts to lowercase)
- Missing version field (adds `version: 1.0.0`)
- Em dashes (replaces with semicolons)
- Hardcoded paths (replaces `/Users/x/` with `~/`)

## Available Templates

| Template | Best For |
|----------|----------|
| `basic` | Simple automations, one-shot tasks |
| `visual` | Presentations, diagrams, charts |
| `mcp` | MCP server integrations, API wrappers |
| `text` | Content generation, documentation |
| `enterprise` | Org-wide deployment, regulated environments |

## CI Integration

```yaml
# .github/workflows/skillcheck.yml
- name: Check for regressions
  run: skillcheck baseline compare my-skill --fail-on-regression
```

## GitHub

https://github.com/olgasafonova/skillcheck-cli
