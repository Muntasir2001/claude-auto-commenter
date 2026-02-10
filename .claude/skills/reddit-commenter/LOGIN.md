# Reddit Auto-Login Workflow

> Executed before Step 1 of main workflow to ensure Reddit login

---

## Prerequisites

1. Create `.env` file in project root (copy from `.env.example`)
2. Add your Reddit credentials:
   ```
   REDDIT_USERNAME=your_username
   REDDIT_PASSWORD=your_password
   ```
3. Ensure `.env` is in `.gitignore` (already configured)

---

## Login Check & Execution Flow

### Before Starting Main Workflow

```
[Start Reddit Activity]
    ↓
[Check if logged in]
    ↓
    Already logged in? → Proceed to main workflow
    ↓
    Not logged in? → Execute auto-login
    ↓
[Auto-Login Process]
```

---

## Auto-Login Workflow

### Step 0: Login Check

**Before any Reddit interaction:**

```
1. Navigate to Reddit
   → browser_navigate("https://www.reddit.com")
   
2. Check login status with snapshot
   → browser_snapshot()
   → Look for user menu/avatar or "Log In" button
   
3. Determine if login needed:
   → If user avatar/username visible → Already logged in ✓
   → If "Log In" button visible → Need to login
```

### Step 1: Navigate to Login Page

```
1. Click "Log In" button
   → browser_snapshot() to get button ref
   → browser_click(login button ref)
   
2. Wait for login form
   → browser_wait_for({ state: "networkidle", timeout: 5000 })
   → browser_snapshot()
```

### Step 2: Enter Credentials

```
1. Read credentials from environment
   → Get REDDIT_USERNAME from .env
   → Get REDDIT_PASSWORD from .env
   
2. Fill username field
   → browser_snapshot() to identify username input
   → browser_click(username field ref)
   → browser_type(username)
   
3. Fill password field
   → browser_snapshot() to identify password input
   → browser_click(password field ref)
   → browser_type(password)
```

### Step 3: Submit Login

```
1. Click login/submit button
   → browser_snapshot() to get submit button ref
   → browser_click(submit button ref)
   
2. Wait for login to complete
   → browser_wait_for({ state: "networkidle", timeout: 10000 })
   → browser_snapshot()
```

### Step 4: Verify Login Success

```
1. Check for login success indicators:
   → User avatar/menu visible
   → Username displayed
   → No error messages
   
2. Handle errors if any:
   → Wrong credentials → Stop and notify user
   → 2FA required → Stop and notify user (need manual intervention)
   → Rate limited → Wait and retry
   
3. On success:
   → Proceed to main workflow (SKILL.md Step 1)
```

---

## Error Handling

| Error | Action |
|-------|--------|
| **Wrong credentials** | Stop execution, notify user to check .env |
| **2FA required** | Stop execution, ask user to disable 2FA or login manually |
| **Captcha appeared** | Stop execution, ask user to login manually |
| **Rate limited** | Wait 5 minutes, retry once |
| **Network error** | Retry up to 3 times with 10s intervals |

---

## Security Best Practices

1. ✅ **Never commit .env** - Already in .gitignore
2. ✅ **Use strong password** - Consider app-specific password if Reddit supports it
3. ⚠️ **Disable 2FA** - Or use manual login option instead
4. ⚠️ **Keep .env file permissions restricted** - `chmod 600 .env` on Unix systems
5. ⚠️ **Rotate credentials regularly** - Change password periodically

---

## Alternative: Manual Login Detection

If you prefer not to store credentials:

```
1. Before batch execution, manually login to Reddit in browser
2. Skill checks if already logged in
3. If not logged in, prompts user to login manually
4. User logs in, then resumes execution
```

This is more secure but requires manual intervention.

---

## Integration with Main Skill

**In SKILL.md, before Step 1:**

```
[Pre-Step 0] Login Check
1. Check if logged in (see LOGIN.md)
2. If not logged in:
   - Execute auto-login (LOGIN.md Steps 0-4)
   - Or prompt user for manual login
3. Verify login success before proceeding

[Step 1] Check Activity Status and Select Subreddit
(Continue with existing workflow...)
```

---

## Testing Login

To test the login workflow in isolation:

```
User command: "Test Reddit login"

Agent should:
1. Navigate to reddit.com
2. Check login status
3. Execute login if needed
4. Report success/failure
```

---

## Notes

- **Login session persistence**: Reddit sessions typically last several hours/days
- **First run**: Login required
- **Subsequent runs**: May not need login if session still valid
- **Multiple accounts**: Create separate .env files (.env.account1, .env.account2) and specify which to use
