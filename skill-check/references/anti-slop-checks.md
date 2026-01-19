# Anti-Slop Detection (Category 5)

**Apply to skills that produce text output** (reports, summaries, plans, content).

Detect patterns that lead to AI-generated slop in skill outputs.

## 5.1 Identify Text-Producing Skills

Skills that need anti-slop checks:
- Description mentions: report, summary, plan, content, document, article, post
- Output includes: markdown files, messages, comments, text content
- Type: enhanced-edoc-update, slide-deck, ship-learn-next, tapestry, linkedin-post

## 5.2 Missing Anti-Slop Guidelines

| Check | Rule | Severity |
|-------|------|----------|
| Has style section | Text-producing skills need writing guidelines | Warning |
| DO list | Positive guidance present | Suggestion |
| DON'T list | Anti-patterns listed | Warning |
| Examples | Good/bad examples provided | Suggestion |

## 5.3 Slop Patterns in Skill Text

Scan the SKILL.md itself for slop (skills that contain slop will produce slop):

| Pattern | Detection | Severity |
|---------|-----------|----------|
| Em dashes | `—` character | Warning |
| Hedge stacking | "might potentially", "may perhaps" | Warning |
| Transition padding | "Furthermore,", "Additionally,", "Moreover," | Warning |
| Sycophantic openers | "Great question!", "Absolutely!" | Warning |
| Cliche intros | "In today's", "In the ever-evolving" | Warning |
| "Not just X, it's Y" | Pattern match | Warning |
| Essay closers | "In conclusion,", "To summarize," | Warning |
| Hope closers | "Hope this helps!" | Warning |
| Self-referential | "As an AI", "I'm here to help" | Warning |
| Filler phrases | "It's important to note that" | Warning |

**Regex patterns**:
```
/—/                                          # em dash
/might potentially|may perhaps|could possibly/i
/^(Furthermore|Additionally|Moreover),/m
/^(Great question|Absolutely|You're right)!/m
/In today's .*(world|landscape|era)/i
/it's not just .*, it's/i
/^(In conclusion|To summarize|Overall),/m
/Hope this helps/i
/As an AI|I'm here to help/i
/It's important to note that/i
```

## 5.4 Missing Anti-Slop Section Template

If a text-producing skill lacks anti-slop guidelines, suggest adding:

```markdown
## Writing Style (Anti-Slop)

**DO**:
- Use active voice
- Be specific with numbers and facts
- State facts directly

**DON'T**:
- Use em dashes; use commas or periods
- Use hedge words unless uncertainty is real
- Use transition padding (Furthermore, Additionally)
- Use filler phrases (It's important to note)
- End with "Hope this helps!" or similar

**Example bad**: [specific example from skill domain]
**Example good**: [rewritten example]
```
