# Reddit Commenter - Batch Mode

> Executed when "Fill today's quota" command given
> See SKILL.md for single comment workflow

## ⚠️ CRITICAL WAIT TIME REQUIREMENT

**ALL waits in batch mode MUST be 8-12 minutes using the `sleep` command**
- Between comments: `sleep 480` to `sleep 720` (8-12 minutes)
- Between subreddits: `sleep 480` to `sleep 720` (8-12 minutes)
- **NEVER wait only 1-2 minutes** - this violates rate limiting strategy

---

## Batch Execution Trigger

Start batch mode with these commands:
- "Fill today's quota"
- "Consume all remaining quota"
- "Run in batch mode"

---

## Pre-Start Check

```
1. Check tracking/reddit/today's-date.md (create from template.md if missing)
2. Check current comment count by subreddit
3. Calculate remaining quota:
   - Total 24 (8 subreddits × 3)
   - Subtract today's written comments
   - Remaining quota = 24 - Today's written comments
```

---

## Batch Workflow

```
[Start]
    ↓
[1] Check tracking file (/tracking/reddit/(today's date).md) → Calculate remaining quota
    ↓
[2] Select subreddit under quota (by priority criteria)
    ↓
[3] Start comment writing loop for that subreddit
    ↓
    [3-1] Execute SKILL.md Step 1-8 (write single comment)
    ↓
    [3-2] Update tracking file
    ↓
    [3-3] Report progress
    ↓
    [3-4] Check that subreddit's quota
          - Under 3 → WAIT 8-12 minutes (sleep 480-720) → back to [3-1]
          - Completed 3 → to [4]
          - No suitable posts → to [4]
    ↓
[4] Move to next subreddit (wait 8-12 minutes using sleep command)
    ↓
[5] Check overall termination condition
    ↓
    YES → [End]
    NO  → Return to [2]
```

---

## Subreddit Selection Priority

| Priority | Criteria | Reason |
|----------|----------|--------|
| 1 | Subreddits with 0 activity today | Ensure variety |
| 2 | Subreddits with oldest last activity time | Distributed activity |
| 3 | Subreddits with available quota | Efficiency |

---

## Wait Time Rules

⚠️ **CRITICAL: These wait times MUST be followed exactly**

| Situation | Wait Time | Notes |
|-----------|-----------|-------|
| Between comments in same subreddit | 8-12 minutes | Use `sleep` command for exact duration |
| Between subreddit transitions | 8-12 minutes | NEVER use 1-2 minutes - this is too short |
| Can't find suitable post | Move to next subreddit (wait 8-12 minutes) | Apply wait before starting next subreddit |

### Implementation
- Use actual `sleep` command: `sleep 480` (8 min) to `sleep 720` (12 min)
- Pick random duration within range for each wait
- Report wait duration: "Waiting 9 minutes before next action..."
- ⚠️ **If waiting only 1-2 minutes, you are NOT following the correct configuration**

### Execution Example

```
Start r/WebDev
  → Comment 1/3 written
  → Wait 10 min (sleep 600)
  → Comment 2/3 written
  → Wait 9 min (sleep 540)
  → Comment 3/3 written ✓
  
r/WebDev complete → Wait 11 min (sleep 660) → Move to r/ClaudeAI

Start r/ClaudeAI
  → Comment 1/3 written
  → Wait 8 min (sleep 480)
  → Comment 2/3 written
  → Wait 12 min (sleep 720)
  → Comment 3/3 written ✓
  
r/ClaudeAI complete → Wait 9 min (sleep 540) → Move to r/Cursor
```

---

## Termination Conditions

Batch execution terminates when one of the following is met:

1. **Quota complete**: All subreddit quotas complete (24)
2. **No posts**: No suitable posts in all subreddits
3. **User interruption**: User requests stop
4. **Error occurred**: After 3 consecutive failures

---

### Progress Report
```
---
[Overall Progress] 6/24 completed

Completed subreddits:
✓ r/WebDev: 3/3
✓ r/ClaudeAI: 3/3

In progress:
→ r/Cursor: 0/3 (current)

Waiting:
- r/LocalLLaMA: 0/3
- r/ChatGPT: 0/3
- r/SideProject: 0/3
- r/Obsidian: 0/3
- r/Rag: 0/3

Next: Move to r/LocalLLaMA after r/Cursor quota complete
---
```

## Skip Conditions

When skipping specific subreddit:

**No suitable posts in New/Rising**: Skip if none after reviewing 5 posts

When skipping, report:
```
r/LocalLLaMA skipped - No suitable posts
→ Moving to r/ChatGPT
```

---

## Error Handling

⚠️ **Note: Error retry waits (30s) are ONLY for technical failures, NOT normal operation**

| Error | Response |
|-------|----------|
| Page loading failure | Wait 30s then retry (max 3 times) - TECHNICAL ERROR ONLY |
| Comment posting failure | Move to next post |
| Login session expired | Stop batch, notify user |
| Rate limit detected | Wait 30 min then resume |

**After error recovery**: Resume normal 8-12 minute wait schedule

---

## Batch Completion Report

```
---
## Batch Completion Report

**Total Written**: 18/24
**Time Spent**: 2 hours 35 minutes

### Results by Subreddit
| Subreddit | Written | Skip Reason |
|-----------|---------|-------------|
| r/WebDev | 3/3 | - |
| r/ClaudeAI | 3/3 | - |
| r/Cursor | 2/3 | No suitable posts |
| r/LocalLLaMA | 0/3 | All skipped (no technical discussion) |
| r/ChatGPT | 3/3 | - |
| r/SideProject | 3/3 | - |
| r/Obsidian | 2/3 | No suitable posts |
| r/Rag | 2/3 | No suitable posts |

### Potential Customers Discovered
- 2 (updated in leads/reddit.md)

### Special Notes
- r/LocalLLaMA: All skipped due to no technical discussion posts today
---
```

---

## Troubleshooting

### If Claude is waiting only 1-2 minutes instead of 8-12 minutes:

**Common causes:**
1. **Misreading wait time config** - Claude may be confusing error retry waits (30s) with normal waits (8-12 min)
2. **Not using sleep command** - Claude must use actual `sleep 480` to `sleep 720` commands
3. **Rushing through batch** - Claude may be trying to complete tasks too quickly

**Solution:**
- Explicitly remind: "Wait 8-12 minutes using sleep command between each comment"
- Verify in terminal: Look for `sleep 480` to `sleep 720` commands being executed
- Check tracking file: Last comment times should be 8-12 minutes apart

---

> Single comment workflow (Step 1-8): See SKILL.md
> Personalization review: See resources/personalization_reddit.md
