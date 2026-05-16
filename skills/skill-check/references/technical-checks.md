# Technical Skills - Security & Performance (Category 7)

**Apply to**: MCP servers, API integrations, automation scripts, code generators, workflows.

**Detection**: Description mentions MCP, API, integration, code, script, server, automation, workflow, build, tool.

Source: MCP Best Practices, Anthropic Tool Use Guidelines

## 7.1 Security (Critical)

| Check | Rule | Severity |
|-------|------|----------|
| Input validation | All user input validated/sanitized | Critical |
| No hardcoded secrets | No API keys/passwords/tokens in examples | Critical |
| Command injection | Shell commands escape user input | Critical |
| Path traversal | File paths validated | Critical |
| URL validation | External URLs validated | Warning |
| Credential handling | Use environment variables | Warning |

**Anti-patterns to flag**:
```
api_key = "sk-..."        # hardcoded secret
password = "actual123"    # hardcoded credential
$(user_input)             # unescaped shell
exec(user + string)       # unsafe exec
```

If skill lacks security guidance, suggest adding a "Security" section covering credential handling, input validation, shell escaping, and path validation.

## 7.2 Error Handling

| Check | Rule | Severity |
|-------|------|----------|
| Error types listed | Expected errors documented | Warning |
| Recovery steps | What to do on failure | Warning |
| User feedback | Clear error messages | Suggestion |
| Graceful degradation | Fallback behavior defined | Warning |
| Resource cleanup | Cleanup on error conditions | Warning |

If skill lacks error handling, suggest adding section covering timeouts, retries, validation messages, and resource cleanup.

## 7.3 Performance

| Check | Rule | Severity |
|-------|------|----------|
| Timeout handling | Long ops have timeouts | Warning |
| Batch operations | Prefer batch over loops | Suggestion |
| Pagination | Large results paginated | Warning |
| Streaming | Large data uses streaming | Suggestion |
| Rate limiting | API rate limits respected | Warning |
| Caching | Repeated lookups cached | Suggestion |
| Resource cleanup | Connections/files closed | Warning |

## 7.4 Logging and Monitoring

| Check | Rule | Severity |
|-------|------|----------|
| Structured logging | Log levels documented (DEBUG/INFO/WARN/ERROR) | Suggestion |
| No sensitive data in logs | Secrets excluded from logs | Warning |
| Request tracing | Context in logs for debugging | Suggestion |

## 7.5 Testing Requirements

| Check | Rule | Severity |
|-------|------|----------|
| Test types mentioned | Unit, integration, e2e | Suggestion |
| Error scenario tests | Edge cases covered | Suggestion |
| Security tests | Auth/validation tested | Suggestion |

## 7.6 MCP-Specific Checks (for MCP skills)

| Check | Rule | Severity |
|-------|------|----------|
| Protocol version | MCP version specified | Warning |
| Lifecycle methods | Init/shutdown documented | Warning |
| Transport type | stdio/SSE/WebSocket specified | Suggestion |
| Schema validation | Request/response schemas defined | Warning |
| State management | Minimize server state, cleanup on disconnect | Suggestion |

## 7.7 Dependencies

| Check | Rule | Severity |
|-------|------|----------|
| Version pinning | Versions specified | Warning |
| Install instructions | Setup steps clear | Warning |
| Fallback options | Alternatives documented | Suggestion |
| Cross-platform | macOS/Linux/Windows notes | Suggestion |
| Existence checks | Check tool exists before use | Warning |

## 7.8 Subagent Compatibility

Source: [Skills Explained](https://claude.com/blog/skills-explained)

Skills can delegate tasks to specialized subagents. Check for proper integration patterns.

| Check | Rule | Severity |
|-------|------|----------|
| Task tool usage | If skill spawns agents, tool restrictions documented | Suggestion |
| Delegation patterns | Clear handoff to specialized agents | Suggestion |
| Agent types | Uses appropriate subagent_type for task | Suggestion |
| Result handling | How agent results are processed | Suggestion |

**Subagent integration patterns**:
- Use Task tool with specific subagent_type for specialized work
- Document which agent types the skill delegates to
- Define how results flow back to the main workflow

**Available agent types**: general-purpose, Explore, Plan, claude-code-guide, fact-checker, technical-researcher, ai-engineer, error-detective, api-documenter, backend-architect, technical-writer
