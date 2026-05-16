# Enterprise Readiness (Category 10)

Source: Anthropic org-wide skill management guidelines

Skills deployed across organizations need additional checks for security, auditability, and multi-user support.

## 10.1 Path Safety

| Check | Rule | Severity |
|-------|------|----------|
| No hardcoded usernames | Paths use ~ or $HOME, not /Users/john | Critical |
| No absolute paths | Use relative paths or env vars | Warning |
| Cross-platform paths | Forward slashes only | Warning |

**Anti-patterns to flag**:
```
/Users/specific-user/
/home/specific-user/
C:\Users\specific-user\
```

**Correct patterns**:
```
~/
$HOME/
./relative/path
${SKILL_DIR}/
```

## 10.2 Credential Safety

| Check | Rule | Severity |
|-------|------|----------|
| No hardcoded secrets | No API keys, tokens, passwords | Critical |
| Environment variables | Secrets via env vars only | Critical |
| No config file secrets | Don't read secrets from shared configs | Warning |
| Secret masking | Log outputs mask sensitive data | Warning |

**Detection patterns**:
```regex
api[_-]?key\s*[:=]\s*["'][^"']+["']
token\s*[:=]\s*["'][^"']+["']
password\s*[:=]\s*["'][^"']+["']
secret\s*[:=]\s*["'][^"']+["']
sk-[a-zA-Z0-9]{20,}
ghp_[a-zA-Z0-9]{36}
```

**Required guidance for secret-using skills**:
```markdown
## Configuration

Set these environment variables before use:
- `API_KEY`: Your API key (get from [provider dashboard])
- `API_SECRET`: Your API secret

Never commit credentials. Use `.env` files (gitignored) or secrets managers.
```

## 10.3 Audit Trail Support

| Check | Rule | Severity |
|-------|------|----------|
| Action logging guidance | Sensitive actions should log | Suggestion |
| No silent failures | Errors reported, not swallowed | Warning |
| User attribution | Multi-user contexts identify actor | Suggestion |

**Sensitive actions requiring logs**:
- File modifications outside skill folder
- External API calls
- Data exports
- Configuration changes

## 10.4 Multi-User Considerations

| Check | Rule | Severity |
|-------|------|----------|
| No user-specific state | Skill works for any user | Warning |
| Configurable outputs | Output paths not hardcoded | Warning |
| Permission documentation | Required permissions listed | Suggestion |

**Required section for enterprise skills**:
```markdown
## Permissions Required

- File system: Read/write to project directory
- Network: Outbound HTTPS to api.example.com
- Environment: ACCESS_TOKEN variable must be set
```

## 10.5 Enterprise Readiness Score

```
CRITICAL (must pass all):
- [ ] No hardcoded usernames in paths
- [ ] No hardcoded secrets
- [ ] Credentials via environment variables

WARNING (should pass 4+/5):
- [ ] Cross-platform paths
- [ ] No config file secrets
- [ ] Errors reported (not swallowed)
- [ ] Output paths configurable
- [ ] Secret masking in logs

SUGGESTION (nice to have):
- [ ] Action logging guidance
- [ ] Permission documentation
- [ ] Multi-user attribution
- [ ] Capability requirements documented

Enterprise Rating:
- 🏢 Enterprise-ready: All critical + 4+ warnings
- ⚠️ Needs work: All critical + <4 warnings
- ❌ Not enterprise-safe: Missing critical checks
```

## 10.6 Capability Requirements

Source: [Organization Skills and Directory](https://claude.com/blog/organization-skills-and-directory)

Skills require specific platform capabilities to function. Enterprise deployments need clear documentation.

| Check | Rule | Severity |
|-------|------|----------|
| Capabilities listed | Documents required platform settings | Warning |
| Code execution noted | Skill documents if it runs code | Warning |
| File creation noted | Skill documents if it creates files | Warning |
| Network access noted | Skill documents external connections | Warning |

**Required documentation for enterprise skills**:
```markdown
## Platform Requirements

This skill requires:
- Code Execution: Yes (runs validation scripts)
- File Creation: Yes (generates reports)
- Network Access: api.example.com (for data sync)
```

**Common capabilities**:
- Code Execution and File Creation (most skills)
- Network access (API/MCP skills)
- Browser automation (visual testing skills)
- System commands (DevOps skills)
