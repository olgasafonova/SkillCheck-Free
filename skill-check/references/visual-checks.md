# Visual Output Skills - WCAG & Layout (Category 6)

**Apply to**: Skills producing presentations, diagrams, PDFs, images, charts, carousels.

**Detection**: Description mentions slide, presentation, diagram, chart, PDF, image, visual, export, carousel.

## 6.1 WCAG 2.2 Compliance

| Check | Rule | Severity |
|-------|------|----------|
| Contrast guidance | Text contrast requirements documented | Warning |
| Accent color safety | Low-contrast color warnings (yellow/lime/cyan) | Warning |
| Alt text guidance | Images need alt text instructions | Warning |
| Target size (2.5.5) | Interactive elements min 44x44px | Warning |

**Required section for visual skills**:
```markdown
## Accessibility (WCAG 2.2)

- Text contrast: 4.5:1 minimum (3:1 for 18pt+)
- Never use yellow/lime/cyan as text on light backgrounds
- Interactive targets: 44x44px minimum
- All images need alt text
```

## 6.2 Aspect Ratio and Element Sizing

| Check | Rule | Severity |
|-------|------|----------|
| Output dimensions | Aspect ratio documented | Warning |
| Minimum sizes per format | Elements scaled for output | Warning |
| Platform constraints | Platform-specific rules | Suggestion |

**Format-specific minimums**:

| Format | Dimensions | Min Text | Min Icon |
|--------|------------|----------|----------|
| Presentation 16:9 | 1920x1080 | 24px | 48px |
| LinkedIn carousel | 1080x1080 | 32px | 64px |
| Instagram story | 1080x1920 | 28px | 56px |
| PDF A4 | 595x842pt | 10pt | 24pt |
| Web diagram | variable | 14px | 32px |

**Sizing rules**:
- Text legible at final output size (not design size)
- Icons recognizable at 32px minimum
- Touch targets 44x44px (mobile: 48x48px)
- Line thickness 1.5px minimum
- Element spacing 8px minimum

## 6.3 Layout Checks

| Check | Rule | Severity |
|-------|------|----------|
| Text overflow | Long text handling documented | Warning |
| Font size minimums | Readable sizes for format | Warning |
| Safe zones | Edge margins specified | Suggestion |
| Information density | Not overcrowded | Suggestion |

## 6.4 Export Checks

| Check | Rule | Severity |
|-------|------|----------|
| Resolution | DPI for print (300) vs screen (72-150) | Suggestion |
| Format options | PNG/PDF/SVG documented | Suggestion |
| File size | Compression guidance | Suggestion |

## 6.5 Design Anti-Convergence

Source: [Improving Frontend Design through Skills](https://claude.com/blog/improving-frontend-design-through-skills)

Visual skills should explicitly counter AI aesthetic defaults. Models converge on "safe" choices; skills need guidance to break this pattern.

### Typography Anti-Patterns

| Check | Rule | Severity |
|-------|------|----------|
| Generic fonts | Flag Inter, Roboto, Arial, Open Sans as defaults | Warning |
| Font guidance | Skill should recommend distinctive alternatives | Suggestion |
| System fonts | "system font" without alternatives is lazy | Warning |

**Detection**: Search for font-family declarations or font recommendations in skill.

### Color Anti-Patterns

| Check | Rule | Severity |
|-------|------|----------|
| AI slop purple | Flag purple/blue gradients on white | Warning |
| Even distribution | Warn about equal color weights | Suggestion |
| Hardcoded hex | Prefer CSS variables over raw hex codes | Suggestion |
| Palette guidance | Skill should specify dominant + accent structure | Suggestion |

**Flag**: Purple/blue gradients (#667eea, #6366f1), equal color weights (33%/33%/33%). **Prefer**: 60/30/10 dominant/secondary/accent split.

### Motion Anti-Patterns

| Check | Rule | Severity |
|-------|------|----------|
| Scattered effects | Warn about hover effects everywhere | Suggestion |
| No priority | Missing animation hierarchy guidance | Suggestion |
| Page load | Should prioritize orchestrated entrance animations | Suggestion |

**Guidance for skills**:
- One well-orchestrated page load with staggered reveals beats scattered micro-interactions
- Prioritize high-impact moments over ambient motion

### Layout Anti-Patterns

| Check | Rule | Severity |
|-------|------|----------|
| Generic hero | Flag cookie-cutter hero sections | Warning |
| No context | Missing context-specific customization | Suggestion |
| Background flatness | Plain solid backgrounds without atmosphere | Suggestion |

**Detection**: Check if skill provides layout alternatives or just uses generic patterns.

If visual skill lacks anti-convergence guidance, suggest adding a "Design Distinctiveness" section covering typography, color, motion, and layout choices.
