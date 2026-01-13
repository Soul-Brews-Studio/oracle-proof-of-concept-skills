---
name: recap
description: Fresh-start orientation—adaptive synthesis with bulletproof edge case handling. Use when starting a session, after /jump, lost your place, or before switching context.
trigger: /recap
---

# /recap — Fresh Start Context

**Goal**: Orient yourself in <10 seconds. Fast by default, rich on demand.

## Usage

```
/recap           # Fast mode (default) - 3 commands, instant
/recap --rich    # Full 18-path detection (see references/rich-paths.md)
```

---

## FAST MODE (Default)

**Option A**: Spawn Haiku subagent (fastest, saves context):
```
Task tool:
  subagent_type: Bash
  model: haiku
  prompt: |
    Gather recap data and output EXACTLY this format:

    Commands to run first:
    - git status -sb && git log --oneline -3
    - git status --short
    - git diff-tree --no-commit-id --name-only -r HEAD~3..HEAD | head -15
    - cat ψ/inbox/focus-agent-main.md
    - cat ψ/inbox/schedule.md | grep -E "$(date +%b) ($(date +%d)|$(($(date +%d)+1)))" | head -5
    - ls -t ψ/memory/retrospectives/**/*.md | grep -v CLAUDE | head -1
    - Read that retro file for What/Why/How/Open
    - ls -t ψ/inbox/handoff/*.md | grep -v CLAUDE | head -1
    - ls -t ψ/memory/learnings/*.md | head -3

    OUTPUT THIS EXACT FORMAT (fill in the [...]):

    # RECAP

    🕐 [HH:MM] | [DD Month YYYY]

    ---

    ## 🚧 FOCUS
    `[STATE]` — [TASK from focus file]

    ---

    ## 📅 TODAY
    - [schedule items for today]
    - [tomorrow preview if relevant]

    ---

    ## 📊 GIT
    | Branch | Modified | Untracked | Last Commit |
    |--------|----------|-----------|-------------|
    | [branch] | [count] | [count] | [commit msg] |

    ---

    ## 📝 LAST SESSION
    **From**: [retro or handoff filename]

    - **What**: [main deliverable from retro]
    - **Why**: [problem solved]
    - **How**: [key approach taken]
    - **Open**: [unfinished items or next steps]

    ---

    ## 📖 CONTEXT

    **Recently Committed** (last 3 commits):
    ```
    [output of: git diff-tree --no-commit-id --name-only -r HEAD~3..HEAD | head -15]
    ```

    **Changed** (uncommitted):
    ```
    [full paths of modified files, one per line]
    ```

    **New** (untracked):
    ```
    [full paths of untracked files, one per line]
    ```

    **Reading**:
    ```
    [full path to latest learning]
    [full path to latest retro]
    [full path to latest handoff]
    ```
```

Haiku outputs **final format** — Opus passes through directly.

**IMPORTANT**: Do NOT trim, summarize, or editorialize Haiku's output. Copy the entire response verbatim. The CONTEXT paths are clickable and valuable for navigation.

**Option B**: Run parallel Bash calls from Opus (if no subagent):

Run ALL these in **parallel** (multiple Bash calls):

```bash
# 1. Git + Focus + Activity (one block)
R=$(git rev-parse --show-toplevel)
echo "=== GIT ===" && git status -sb
echo "=== FOCUS ===" && cat "$R/ψ/inbox/focus-agent-main.md" 2>/dev/null || echo "No focus"
echo "=== ACTIVITY ===" && tail -3 "$R/ψ/memory/logs/activity.log" 2>/dev/null
```

```bash
# 2. Schedule today + upcoming (DuckDB markdown extension)
echo "=== SCHEDULE ==="
duckdb -markdown -c "
LOAD markdown;
SELECT title, LEFT(content, 100) as details
FROM read_markdown_sections('ψ/inbox/schedule.md')
WHERE content LIKE '%$(date +%b) $(date +%d | sed 's/^0//')%'
   OR content LIKE '%$(date +%b) $(($(date +%d | sed 's/^0//') + 1))%'
LIMIT 5;
" 2>/dev/null || grep -E "\| *$(date +%b) *$(date +%d | sed 's/^0//')" ψ/inbox/schedule.md | head -3
```

```bash
# 3. Latest handoff (parallel)
R=$(git rev-parse --show-toplevel)
echo "=== HANDOFF ==="
HANDOFF=$(ls -t "$R/ψ/inbox/handoff/"*.md 2>/dev/null | grep -v CLAUDE | head -1)
[ -f "$HANDOFF" ] && head -20 "$HANDOFF"
```

```bash
# 4. Hot tracks (DuckDB markdown extension - no CSV needed!)
echo "=== TRACKS ==="
duckdb -markdown -c "
LOAD markdown;
SELECT
    COALESCE(REGEXP_EXTRACT(file_path, '/([0-9]+)-', 1), '-') as id,
    REGEXP_EXTRACT(content, '^# Track[^:]*: ([^\\n]+)', 1) as name,
    REGEXP_EXTRACT(content, '\\*\\*Status\\*\\*: ([^\\n]+)', 1) as status,
    REGEXP_EXTRACT(content, '\\*\\*Created\\*\\*: ([0-9-]+)', 1) as created
FROM read_markdown('ψ/inbox/tracks/*.md', include_filepath=true)
WHERE file_path NOT LIKE '%INDEX%' AND file_path NOT LIKE '%CLAUDE%'
ORDER BY id DESC NULLS LAST;
" 2>/dev/null || ls -t ψ/inbox/tracks/*.md
```

---

## Output Format

```markdown
# RECAP

🕐 [time] | [date]

---

## 🚧 FOCUS (if exists)
[STATE] — [TASK]

---

## 📅 TODAY
- [schedule items]
- [tomorrow preview]

---

## 📊 GIT
| Branch | Modified | Untracked | Last Commit |
|--------|----------|-----------|-------------|

**Changed**:
```
[full clickable paths of modified files]
```

**New**:
```
[full clickable paths of untracked files]
```

---

## 🔥 TRACKS (all — chance to clear)

| id | Track | Status | Created | Edited |
|----|-------|--------|---------|--------|

*Sorted by edited desc — most recent activity first*

---

## 📝 LAST SESSION
**From**: [handoff or retro filename]

- **What**: [main deliverable/change]
- **Why**: [problem solved]
- **How**: [key approach]
- **Open**: [unfinished or next steps]

## 🤔 What's next?
[2-3 concrete options continuing from LAST SESSION]

---

## 📖 CONTEXT (clickable)

**Recently Committed**:
```
[files from last 3 commits]
```

**Changed**: [modified files]
**New**: [untracked files]

**Reading**:
```
ψ/memory/learnings/xxx.md
ψ/memory/retrospectives/xxx.md
ψ/inbox/handoff/xxx.md
```

---

## "What's next?" Rules

Generate 2-3 suggestions based on signals found:

| If you see... | Suggest... |
|---------------|------------|
| Untracked files | Commit them |
| Focus = completed | Pick from tracks or start fresh |
| Schedule items today | Work on scheduled item |
| Stale tracks | Review or close cold tracks |
| Branch diverged | Sync or create PR |

**Never leave blank. Always offer concrete options based on gathered data.**

---

## RICH MODE (`/recap --rich`)

For edge cases and detailed diagnosis. See [references/rich-paths.md](references/rich-paths.md) for:
- Data gathering script
- 18-path detection precedence table
- All output path templates (BLOCKER, DEEP_WORK, STALE_FOCUS, etc.)

---

## Hard Rules

1. **Blockers first** — GIT_CONFLICT_STATE → BLOCKER → CORRUPTED before all others
2. **One path only** — Never mix outputs
3. **Preserve tone** — Quote exactly from retro/focus
4. **Admit staleness** — Say "stale" if >4h
5. **Ask, don't suggest** — "What next?" not "You should..."
6. **Prose, no tables** — Use sentences for output
7. **Transparent** — If uncertain, use CONFUSED path
8. **Passthrough Haiku** — Never trim subagent output; copy verbatim including CONTEXT paths

---

**Philosophy**: Detect reality. Surface blockers. Preserve tone. Ask for direction.

**Version**: 5.4 (Cleaner LAST SESSION format + Recently Committed context)
**Updated**: 2026-01-13
