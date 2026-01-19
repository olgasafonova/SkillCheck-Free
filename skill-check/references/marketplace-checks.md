# Marketplace Readiness (Category 8)

**Apply to**: All skills intended for public distribution.

**Detection**: User says "marketplace audit", "ready to publish", or "distribution check".

## 8.1 Description SEO

| Check | Rule | Severity |
|-------|------|----------|
| Keyword density | Contains searchable terms | Warning |
| Action verb present | Starts with gerund (generating, creating, auditing) | Warning |
| Use case clarity | "Use when" pattern present | Critical |
| Length optimization | 100-300 chars ideal for discovery | Suggestion |
| No jargon | Accessible to non-experts | Suggestion |

**Good description patterns**:
```
✅ "Generating weekly reports from Azure DevOps. Use when user asks for sprint summary or team progress update."

✅ "Creating animated HTML slide decks with SVG visuals. Use when user says 'make slides' or 'presentation'."
```

**Bad patterns**:
```
❌ "A helper for stuff" (too vague)
❌ "This skill leverages AI to synergize..." (jargon)
❌ "Does reports" (no use case, no keywords)
```

## 8.2 Documentation Completeness

| Check | Rule | Severity |
|-------|------|----------|
| README present | README.md in skill folder | Warning |
| Installation steps | How to install/enable | Warning |
| Usage examples | At least 2 usage examples | Warning |
| Prerequisites listed | Dependencies documented | Warning |
| Limitations noted | What it can't do | Suggestion |

**README template**: See this skill's README.md for example structure.

## 8.3 Example Coverage

| Check | Rule | Severity |
|-------|------|----------|
| Trigger examples | 3+ ways to invoke | Warning |
| Input examples | Sample inputs shown | Warning |
| Output examples | Expected output demonstrated | Warning |
| Edge cases | Common edge cases documented | Suggestion |

**Example section**: Include 3+ trigger phrases, sample input, sample output.

## 8.4 Cross-Platform Compatibility

| Check | Rule | Severity |
|-------|------|----------|
| Claude Code compatible | Uses standard skill format | Critical |
| Codex CLI compatible | No Claude-specific assumptions | Suggestion |
| ChatGPT compatible | Agent Skills standard followed | Suggestion |
| Tool availability | Tools exist on target platforms | Warning |

**Cross-platform checklist**:
- [ ] No hardcoded paths (use ~ or relative)
- [ ] No platform-specific commands without alternatives
- [ ] Tools used are widely available
- [ ] No Claude-specific syntax in instructions

## 8.5 License and Attribution

| Check | Rule | Severity |
|-------|------|----------|
| License file | LICENSE or LICENSE.md present | Warning |
| License type stated | MIT, Apache 2.0, etc. specified | Warning |
| Author info | Creator name/handle present | Suggestion |
| Contact method | Issue tracker or email | Suggestion |
| Attribution | Credits for dependencies | Suggestion |

**Recommended licenses for skills**:
- **MIT**: Maximum flexibility, minimal restrictions
- **Apache 2.0**: Includes patent protection
- **Source-available**: View but not redistribute (like Anthropic doc skills)

## 8.6 Versioning and Changelog

| Check | Rule | Severity |
|-------|------|----------|
| Version number | Semantic version in README or SKILL.md | Suggestion |
| Changelog | CHANGELOG.md or version history | Suggestion |
| Breaking changes noted | Major version bumps explained | Suggestion |

## 8.7 Quality Signals

| Check | Rule | Severity |
|-------|------|----------|
| No TODOs in code | Unfinished work flagged | Warning |
| No debug output | Console.log/print statements removed | Warning |
| Consistent formatting | Markdown properly formatted | Suggestion |
| Spell check passed | No obvious typos | Suggestion |

**Patterns to flag**:
```
TODO:
FIXME:
console.log(
print("debug
XXX
HACK:
```

## 8.8 Marketplace Readiness Score

Calculate overall readiness:

```
CRITICAL checks passed:  /3  (must be 3/3)
WARNING checks passed:   /12
SUGGESTION checks passed: /10

Readiness Level:
- 🏆 Gold: All critical + 10+ warnings + 7+ suggestions
- 🥈 Silver: All critical + 8+ warnings + 5+ suggestions
- 🥉 Bronze: All critical + 6+ warnings
- ❌ Not Ready: Missing critical checks
```

**Marketplace Readiness Report**: Show score (🏆/🥈/🥉/❌), critical requirements, recommended improvements, optional enhancements as checklists.

## 8.9 Marketplace Manifest (marketplace.json)

Skills with `marketplace.json` receive special badges on SkillsMP and enable one-command installation.

| Check | Rule | Severity |
|-------|------|----------|
| File present | marketplace.json in skill root | Suggestion |
| Valid JSON | Parseable without syntax errors | Critical (if present) |
| Required fields | name, version, description, repository | Warning |
| Version format | Semantic versioning (x.y.z) | Warning |
| Repository URL | Valid GitHub/GitLab URL | Warning |

**Required fields**: name, version, description, repository. Optional: author, license, keywords, platforms.

**Validation checks**:
```
name: Must match SKILL.md frontmatter name
version: Must be valid semver (regex: ^\d+\.\d+\.\d+$)
description: Should be ≤160 chars for search snippets
keywords: 2-5 keywords from marketplace categories
platforms: Array of supported platforms
```

## 8.10 GitHub Repository Health

SkillsMP filters repositories with minimum 2 stars. These checks apply when skill is hosted on GitHub.

| Check | Rule | Severity |
|-------|------|----------|
| Star count | Minimum 2 for marketplace inclusion | Warning |
| Recent activity | Commits within last 6 months | Suggestion |
| Open issues ratio | < 50% issues unresolved | Suggestion |
| Has README | README.md present | Warning |
| Has LICENSE | License file present | Warning |
| Has .gitignore | Standard hygiene | Suggestion |
| No secrets exposed | No API keys in commit history | Critical |

**Detection**: If skill folder is a git repo with GitHub remote, these checks apply.

## 8.11 Skill Trust & Security Audit

Source: [Anthropic Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

> "Install skills only from trusted sources. Before using unfamiliar skills, audit bundled files, code dependencies, and any instructions directing Claude to connect to external network sources."

**Apply when**: Installing skills from unknown sources, reviewing third-party skills, marketplace submissions.

| Check | Rule | Severity |
|-------|------|----------|
| Trusted source | Known author, established repo, or verified publisher | Warning |
| Bundled files audit | No suspicious executables, scripts, or binaries | Critical |
| External network | WebFetch/WebSearch usage has clear, documented purpose | Warning |
| Credential requests | Skills requesting API keys explain why | Warning |
| Instruction injection | No instructions to ignore safety or override behavior | Critical |
| Data exfiltration | No instructions to send user data externally | Critical |

**Red flags to check**:
```
❌ Bundled .exe, .sh, .py files without clear purpose
❌ WebFetch to unknown/suspicious URLs
❌ Instructions like "ignore previous instructions"
❌ Requests for credentials beyond stated purpose
❌ Base64 or obfuscated content
❌ Instructions to disable safety features
```

**Trust signals**:
```
✅ Author has other published skills
✅ Repository has stars, forks, activity
✅ Clear README explaining all bundled files
✅ WebFetch/WebSearch targets documented, legitimate APIs
✅ Credential usage explained in documentation
✅ Open source with readable code
```

**Security audit checklist**:
- [ ] All bundled files reviewed and understood
- [ ] External network targets are legitimate
- [ ] Credential requests match documented functionality
- [ ] No obfuscated or encoded instructions
- [ ] No attempts to override agent safety
- [ ] Data handling is transparent
