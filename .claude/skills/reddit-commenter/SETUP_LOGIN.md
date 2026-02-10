# Setup Auto-Login for Reddit Commenter

Quick setup guide to enable automated Reddit login.

---

## Setup Steps

### 1. Create Your Credentials File

In your project root, create a `.env` file:

```bash
# Copy the example file
cp .env.example .env
```

### 2. Add Your Reddit Credentials

Edit the `.env` file and add your Reddit username and password:

```
REDDIT_USERNAME=your_actual_username
REDDIT_PASSWORD=your_actual_password
```

**Important:** Use your exact Reddit username (not email) and password.

### 3. Verify Security

Check that `.env` is in your `.gitignore`:

```bash
cat .gitignore | grep .env
```

You should see `.env` listed. This prevents accidentally committing credentials.

### 4. Test the Login

Ask Claude to test the login:

```
"Test Reddit login"
```

Claude will:
- Navigate to Reddit
- Check if you're logged in
- If not, use your credentials from `.env` to login
- Report success or any errors

---

## Important Security Notes

### ✅ Safe Practices

- `.env` file is already in `.gitignore`
- Credentials never leave your local machine
- File is only read by Claude when executing the skill

### ⚠️ Limitations

**This won't work if Reddit requires:**
- Two-factor authentication (2FA)
- Captcha verification
- Email/SMS verification

**Solutions:**
1. Disable 2FA temporarily (not recommended for security)
2. Use Option 1 (persistent browser context) instead - see main README
3. Login manually before running the skill

---

## Troubleshooting

### "Login failed" Error

**Possible causes:**
1. Wrong username/password in `.env`
2. 2FA is enabled on your account
3. Reddit detected automated login and requires captcha

**Solution:**
- Double-check credentials in `.env`
- Try logging in manually at reddit.com with same credentials
- If 2FA is enabled, consider disabling it or using persistent browser context instead

### "2FA Required" Message

Your Reddit account has two-factor authentication enabled.

**Options:**
1. Disable 2FA (less secure)
2. Use persistent browser context (recommended - see main README)
3. Login manually before each session

### ".env file not found" Error

**Solution:**
```bash
# Make sure you're in the project root
cd /Users/muntasirenamalhuda/Desktop/projects/doublespeed/auto-commenter

# Create .env from example
cp .env.example .env

# Edit and add your credentials
nano .env  # or use your preferred editor
```

---

## Alternative: Persistent Browser Context

If auto-login doesn't work (due to 2FA, captcha, etc.), use persistent browser context instead:

**See main README for Option 1 setup**

This method:
- Saves your session after manual login
- Reuses session for future runs
- Works with 2FA
- More secure (no credentials in files)

---

## Testing

### Test Commands

```bash
# Test login only
"Test Reddit login"

# Test full workflow (will auto-login if needed)
"Write a Reddit comment"
```

### Expected Behavior

**First time:**
1. Claude reads credentials from `.env`
2. Opens Reddit and attempts login
3. Fills username and password
4. Submits form
5. Verifies login success
6. Proceeds with commenting workflow

**Subsequent times:**
1. Claude checks if already logged in
2. If session valid, skips login
3. Proceeds directly to commenting

---

## Need Help?

If you encounter issues:

1. Check `.env` file exists and has correct format
2. Verify credentials are correct (test manual login at reddit.com)
3. Check if 2FA is enabled on your account
4. Look for error messages in Claude's response

For persistent issues, consider using Option 1 (persistent browser context) instead.
