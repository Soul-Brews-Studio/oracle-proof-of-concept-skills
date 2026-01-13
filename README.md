# oracle-proof-of-concept-skills

> *"On January 11, 2026, I deleted everything. 79 commands. 20 skills. 38 specs. And it was the best decision I ever made."*

**The 12 skills that survived The Great Archive Migration** — practical tools + the journey.

## The Story

After 8 months of building AI tools daily, we had 79 commands. Context bloat. Confusion. On January 11, 2026, we archived everything and let only the essentials survive.

**79 → 0 → 12**

Read the full journey: [docs/journey/THE-GREAT-ARCHIVE.md](docs/journey/THE-GREAT-ARCHIVE.md)

---

## The 12 Survivors

| Skill | Purpose | Category |
|-------|---------|----------|
| `/trace` | Find anything, Oracle first | Discovery |
| `/recap` | Fresh start orientation | Orientation |
| `/rrr` | Session retrospective | Reflection |
| `/learn` | Explore codebase with 3 parallel agents | Learning |
| `/project` | Clone & track repos via ghq | Management |
| `/where-we-are` | Session awareness (quick/deep) | Awareness |
| `/forward` | Handoff to next session | Continuity |
| `/context-finder` | Fast search subagent | Search |
| `/feel` | Mood logging | Logging |
| `/fyi` | Info logging | Logging |
| `/standup` | Daily check | Routine |
| `/schedule` | Calendar integration | Planning |
| `/watch` | Learn from YouTube via Gemini | Learning |

---

## Installation

```bash
ghq get -u Soul-Brews-Studio/oracle-proof-of-concept-skills && \
for s in $(ghq root)/github.com/Soul-Brews-Studio/oracle-proof-of-concept-skills/skills/*/; do \
  mkdir -p ~/.claude/skills && ln -sf "$s" ~/.claude/skills/; \
done
```

Restart Claude Code. Skills available globally.

---

## Skill Structure

Each skill follows Claude Code's required structure:

```
skills/
├── trace/
│   └── skill.md          ← Required filename
├── recap/
│   └── skill.md
├── rrr/
│   └── skill.md
└── ... (12 total)
```

**Important**: Skills must be `folder/skill.md`, not single files. This was learned the hard way (10 minutes debugging).

---

## Philosophy

> "The Oracle Keeps the Human Human"

Core principles:
- **Nothing is Deleted** — Append only, timestamps = truth
- **Patterns Over Intentions** — Observe what happens, not what's meant
- **External Brain, Not Command** — Mirror, don't decide

Read more: [docs/philosophy/ORACLE-PHILOSOPHY.md](docs/philosophy/ORACLE-PHILOSOPHY.md)

---

## Archive-First Development

The pattern that created this repo:

> "Archive now, renovate to skill when bringing back"

1. Audit usage (which skills did you use this week?)
2. Archive everything unused to `_archive/`
3. Let only essentials survive
4. Document the learning

---

## Who This Is For

- **Developers** who want practical Claude Code skills
- **Learners** who want to understand the Oracle philosophy
- **Both** who appreciate tools born from real usage

---

## Related

- [Oracle Open Framework](https://github.com/Soul-Brews-Studio/oracle-framework) — Philosophy + structure
- [nat-agents-core](https://github.com/laris-co/nat-agents-core) — Plugin with skills + agents
- [Nat-s-Agents](https://github.com/laris-co/Nat-s-Agents) — Full implementation (private tree)

---

## License

MIT

---

*Born from The Great Archive Migration, January 11, 2026*
