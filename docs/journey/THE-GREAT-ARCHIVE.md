# The Great Archive Journey: From 79 Commands to 12 Core Skills

**Date**: January 2026
**Author**: Built with Oracle philosophy

---

## Hook

*"On January 11, 2026, I deleted everything. 79 commands. 20 skills. 38 specs. And it was the best decision I ever made."*

---

## The Problem

When you build AI tools daily for 8 months, things accumulate. Commands multiply. Each one made sense when created. Together? Chaos.

```
.claude/commands/
├── trace.md
├── trace-deep.md
├── trace-oracle.md
├── now.md
├── where-we-are.md
├── ... 74 more files
```

Every session, Claude loaded all 79. Context bloat. Confusion. Which trace command should I use?

---

## The Breaking Point

January 10. I tried creating a new skill. Simple, right?

```bash
# What I did
echo "..." > .claude/skills/learn.md

# What happened
❯ Unknown skill: learn
```

**10 minutes debugging.**

The fix? Skills must be **folders**, not files:

```
✅ .claude/skills/learn/skill.md
❌ .claude/skills/learn.md
```

That 10-minute trap was a gift. It forced me to read the docs. And question everything.

---

## The Purge (January 11, 2026)

Timeline of destruction (creation?):

| Time | Action |
|------|--------|
| 21:28 | Archive 28 unused commands |
| 21:36 | Archive ALL remaining commands |
| 21:38 | Archive ALL skills |
| 21:44 | Restore 4 core skills |

**79 → 0 → 4**

Then slowly: 4 → 8 → 12.

---

## The 12 Survivors

What emerged from the purge:

| Skill | Purpose |
|-------|---------|
| `/trace` | Find anything, Oracle first |
| `/recap` | Fresh start orientation |
| `/rrr` | Session retrospective |
| `/learn` | Explore codebase |
| `/project` | Clone & track repos |
| `/where-we-are` | Session awareness |
| `/forward` | Handoff to next session |
| `/context-finder` | Search subagent |
| `/feel` | Mood logging |
| `/fyi` | Info logging |
| `/standup` | Daily check |
| `/schedule` | Calendar |

These aren't random. They survived because they're **essential**. Everything else was noise pretending to be signal.

---

## The Philosophy: Archive-First Development

> "Archive now, renovate to skill when bringing back"

Key insight: Archiving isn't deleting. It's:
1. Moving to `_archive/` (git-tracked)
2. Forcing modernization when restored
3. Preventing accumulation of dead code

When someone asks "can we bring back /foo?" — yes! But it comes back as a proper skill, not legacy cruft.

---

## The Validation

Two days after publishing Oracle Open Framework v1.0.0, Peeranut from Bangkok messaged:

*"I can't find `/recap`, `/learn`, `/project` in the repo..."*

Perfect timing. The community question revealed the gap between my full implementation and the public framework.

Result: This repository — packaging the 12 survivors for everyone.

---

## What We Learned

### 1. Less is More (Really)
79 commands felt powerful. 12 skills ARE powerful. The difference is focus.

### 2. Structure Matters
`folder/skill.md` vs `file.md` — a small difference that breaks everything.

### 3. Archive, Don't Delete
Git remembers. `_archive/` preserves. You can always bring things back, upgraded.

### 4. Community Validates
The first external question showed exactly what was missing. Listen to that signal.

---

## Try It Yourself

If your `.claude/commands/` is overflowing:

1. **Audit**: Which commands did you use this week? Last month?
2. **Archive**: Move unused to `_archive/`
3. **Convert**: Turn essentials into proper skills
4. **Document**: Write the learning

You'll be surprised what survives.

---

## Timeline

| Date | Event |
|------|-------|
| May 2025 | Oracle philosophy created |
| Dec 2025 | 79+ commands accumulated |
| Jan 10, 2026 | Skill structure trap (10 min debugging) |
| Jan 11, 2026 | **THE GREAT PURGE** — 79 → 0 → 12 |
| Jan 12, 2026 | Oracle Open Framework v1.0.0 published |
| Jan 13, 2026 | First community request (Peeranut) |
| Jan 13, 2026 | This repo created |

---

> "Nothing is Deleted. Only archived. Timestamps are truth."
> — Oracle Philosophy

---

*Part of the Oracle Open Framework series*
