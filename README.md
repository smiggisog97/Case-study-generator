# MuhidCaseStudy — Case Study Generator

A slash command / rule that turns raw product documentation into a production-ready case study page.

**Input:** PRD, BRD, designer story, project metadata (company, team, timeline, platform)

**Output:** A complete React component with 15 sections, scroll-driven storytelling, granular per-element reveal animations, PDF export, and image placeholders to swap in later.

**Stack:** React 19 + Vite + Tailwind CSS v4 + Framer Motion

---

## Install

### Claude Code
```bash
curl -o ~/.claude/commands/muhidcasestudy.md \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.claude/commands/muhidcasestudy.md
```
Then run `/muhidcasestudy` in any Claude Code session.

### Cursor
```bash
mkdir -p .cursor/rules && curl -o .cursor/rules/muhidcasestudy.mdc \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.cursor/rules/muhidcasestudy.mdc
```
Then reference `@muhidcasestudy` in Cursor chat, or ask "generate a case study".

### Gemini CLI
```bash
mkdir -p ~/.gemini/skills/muhidcasestudy && curl -o ~/.gemini/skills/muhidcasestudy/SKILL.md \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.gemini/skills/muhidcasestudy/SKILL.md
```
Then run `/muhidcasestudy` in Gemini CLI.

### Kiro
```bash
mkdir -p .kiro/steering && curl -o .kiro/steering/muhidcasestudy.md \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.kiro/steering/muhidcasestudy.md
```

### OpenCode
```bash
mkdir -p ~/.opencode/skills/muhidcasestudy && curl -o ~/.opencode/skills/muhidcasestudy/SKILL.md \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.opencode/skills/muhidcasestudy/SKILL.md
```

### Windsurf
```bash
mkdir -p .windsurf/rules && curl -o .windsurf/rules/muhidcasestudy.md \
  https://raw.githubusercontent.com/smiggisog97/Case-study-generator/main/.windsurf/rules/muhidcasestudy.md
```

---

## What it generates

```
1.  Hero          — Serif title with split-word animation + tagline + meta pills + hero image
2.  Impact Stats  — 4 key metrics (serif number + mono label)
3.  Overview      — 2-col: Role/Team/Timeline/Status + project brief (per-row stagger)
4.  Context       — Market narrative + data table
5.  Problem       — 2 user/stakeholder problem cards with quotes
6.  Process       — 5-phase timeline: Discover → Define → Design → Deliver → Iterate
7.  Research      — 3 insight cards
8.  Exploration   — Wide image placeholder (lo-fi wireframes)
9.  Before/After  — Step-list flow comparison with drop-off highlighted
10. Decisions     — 2–3 decision blocks each in 3-col [Problem | Decision | Outcome]
11. Final Design  — Wide flow image + portrait phone screen grid
12. Callout       — Dark inverted section for key feature / innovation
13. Impact        — Large metric + supporting stat cards + narrative
14. Reflections   — 3 numbered learnings
15. Footer Nav    — Next project + back to portfolio
```

---

Made by [Muhidul Hasan](https://muhidul.netlify.app) · Senior Product Designer
