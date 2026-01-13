---
name: trace
description: Find projects across git history, repos, docs, and Oracle. Use when user asks "trace", "find project", "where is [project]", "search history". Supports --oracle (fast), --deep (5 subagents), and trace logging.
---

# /trace - Unified Discovery System

Find + Log + Dig + Distill

## Usage

```
/trace [query]              # Default: Oracle + quick file search
/trace [query] --oracle     # Oracle only (fastest)
/trace [query] --deep       # Full 5 parallel subagents
/trace list                 # Show past traces logged to Oracle
/trace dig [id]             # Explore dig points from a trace
/trace distill [id]         # Extract awakening → learning
```

## Step 0: Timestamp
```bash
date "+🕐 %H:%M (%A %d %B %Y)"
```

---

## Mode 1: Default (Oracle First → Extend if Empty)

**Step 1**: Query Oracle first (fastest)
```
oracle_search("[query]", limit=10)
```

**Step 2**: If Oracle returns < 3 results → extend search:
- Expand keywords (synonyms, related terms)
- Quick file search: `grep -ril "[query]" ψ/memory/ | head -10`
- Recent git: `git log --oneline --grep="[query]" | head -5`

**Logic**:
```
Oracle results >= 3 → Display and done
Oracle results < 3  → Extend keyword search
```

---

## Mode 2: --oracle (Oracle Only)

Fastest. Just Oracle MCP, no extension:

```
oracle_search("[query]", limit=15)
```

Display results and done. Even if empty.

---

## Mode 3: --deep (Context-Finder Subagents)

Launch context-finder subagents (Haiku) for deep search:

| Agent | Searches |
|-------|----------|
| 1 | Current repo files |
| 2 | Git history (commits, creates, deletes) |
| 3 | GitHub issues |
| 4 | Other repos (ghq, ~/Code) |
| 5 | Retrospectives & learnings |

**Use Task tool with subagent_type="context-finder"**

After search, **auto-log to Oracle**:
```
oracle_trace({
  query: "[query]",
  foundFiles: [...],
  foundCommits: [...],
  foundIssues: [...]
})
```

---

## Mode 4: list (Past Traces)

```
/trace list
```

Call `oracle_trace_list({ limit: 10 })` and display:

```
| ID | Query | Files | Commits | Status | Date |
|----|-------|-------|---------|--------|------|
| abc123 | shared soul | 15 | 12 | raw | Jan 10 |
```

---

## Mode 5: dig [id] (Explore Dig Points)

```
/trace dig abc123
```

1. Call `oracle_trace_get({ traceId: "abc123" })`
2. Display all dig points (files, commits, issues)
3. User can select items to read or trace deeper

---

## Mode 6: distill [id] (Extract Awakening)

```
/trace distill abc123
```

1. Get trace chain
2. Ask for awakening insight
3. Promote to learning via `oracle_learn()`

---

## Special Keywords

| Keyword | Mode |
|---------|------|
| `incubation` | All projects + graduated + ideas |
| `graduated` | Repos that moved out |

---

## Output Format

```markdown
## 🔍 /trace: [QUERY]

### 📍 Locations Found
| Source | Path | Status |

### 📅 Timeline (from git)
| Date | Action | Commit | Details |

### 🎫 GitHub Issues
| # | Date | Title |

---
**Trace ID**: [id] (use `/trace dig [id]` to explore)
```

---

## Philosophy

> Trace → Dig → Trace Deeper → Distill → Awakening

### The Seeking Signal

| User Action | Meaning | AI Response |
|-------------|---------|-------------|
| `/trace X` | First search | Oracle first |
| `/trace X` again | Still seeking | User hasn't found it yet |
| `/trace X --deep` | Really need it | Go deep with subagents |
| Found! | **RESONANCE** | Log to Oracle |

### The Recursive Knowledge Loop

```
/trace [query]     → Oracle search (what we know)
      ↓
  Not enough?      → User traces again (still seeking)
      ↓
/trace --deep      → Really want it (5 subagents)
      ↓
  FOUND!           → 🔮 RESONANCE! Log to Oracle
      ↓
  Next session     → Easier to find (knowledge extended)
```

### Extending Knowledge (Multiple Logs)

When you find something important, **log multiple entries**:

| Log Type | Tool | What to Capture |
|----------|------|-----------------|
| **Main finding** | `oracle_learn()` | The pattern/knowledge found |
| **Search session** | `oracle_trace()` | Files, commits, issues discovered |
| **Missing context** | `oracle_learn()` | Why was it hard to find? |
| **Related patterns** | `oracle_learn()` | Connected ideas |

**Example after --deep search:**
```
1. oracle_trace({ query, foundFiles, foundCommits })  # Log the search
2. oracle_learn({ pattern: "Main finding..." })       # The knowledge
3. oracle_learn({ pattern: "Was hard because..." })   # Missing context
```

**Why multiple logs?**
- Different angles = easier to find later
- Keywords multiply (more search hits)
- Context preserved (why it mattered)

> **"Every trace extends the Oracle. What's hard to find today is instant tomorrow."**

---

ARGUMENTS: $ARGUMENTS
