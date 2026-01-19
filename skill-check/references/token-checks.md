# Token Efficiency (Category 9)

Source: Anthropic Agent Skills engineering blog

Skills use progressive disclosure: ~100 tokens for metadata scanning, <5k tokens full load. Token-heavy skills waste context and may not activate correctly.

## 9.1 Frontmatter Budget

| Check | Rule | Severity |
|-------|------|----------|
| Name length | ≤64 chars (~20 tokens) | Critical |
| Description length | ≤1024 chars (~300 tokens) | Critical |
| Combined frontmatter | <400 tokens | Warning |

**Measurement**:
```bash
# Rough token estimate (words × 1.3)
head -20 SKILL.md | wc -w | awk '{print int($1 * 1.3) " tokens (est)"}'
```

## 9.2 Body Budget

| Check | Rule | Severity |
|-------|------|----------|
| SKILL.md total | <5,000 tokens (~3,800 words) | Warning |
| SKILL.md total | >8,000 tokens (~6,000 words) | Critical |
| Line count proxy | <500 lines optimal | Warning |
| Line count proxy | >800 lines problematic | Critical |

**Token estimation**:
```bash
# Word count as token proxy
wc -w SKILL.md | awk '{tokens=int($1 * 1.3); print tokens " tokens (est)"; if(tokens > 5000) print "⚠️ Over budget"}'
```

## 9.3 Referenced Files Budget

| Check | Rule | Severity |
|-------|------|----------|
| Each referenced file | <3,000 tokens | Warning |
| Total skill (all files) | <15,000 tokens | Warning |
| Reference depth | Max 1 level (no nested refs) | Critical |

**Detection**:
1. Find all file references in SKILL.md: `See [file].md`, `Refer to [file]`, `[file].md`
2. Sum tokens across all referenced files
3. Check referenced files don't themselves reference other files

## 9.4 Token Efficiency Score

Calculate efficiency rating:

```
Frontmatter tokens: ___/400
Body tokens: ___/5000
Referenced files: ___/10000
Total: ___/15400

Efficiency Rating:
- 🟢 Lean: Total <5,000 tokens
- 🟡 Normal: Total 5,000-10,000 tokens
- 🟠 Heavy: Total 10,000-15,000 tokens
- 🔴 Bloated: Total >15,000 tokens
```

**Report format**:
```markdown
### Token Budget

| Component | Tokens (est) | Budget | Status |
|-----------|-------------|--------|--------|
| Frontmatter | 180 | 400 | ✅ |
| SKILL.md body | 2,400 | 5,000 | ✅ |
| forms.md | 800 | 3,000 | ✅ |
| reference.md | 1,200 | 3,000 | ✅ |
| **Total** | **4,580** | **15,000** | 🟢 Lean |
```
