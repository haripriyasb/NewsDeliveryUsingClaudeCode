# NewsDeliveryUsingClaudeCode
# Daily Database & AI News Digest - Setup Documentation

**Created:** February 12, 2026
**Status:** ✅ Fully Automated
**Next Run:** Every day at 5:00 AM

---

## What We Built

An automated system that generates a daily digest of database and AI news from curated sources, formatted as markdown and saved to your Obsidian vault.

### Features

- 🔍 **Curated Sources:** Brent Ozar, SQLServerCentral, Planet PostgreSQL, Crunchy Data, Anthropic, OpenAI
- 📰 **Industry Pulse:** Top Hacker News stories about databases and AI
- 💡 **Content Ideas:** 2-3 blog post ideas for your Substack
- 🔄 **Deduplication:** Same story from multiple sources shown once
- 📖 **Visible URLs:** All links shown in full (clickable and copyable)
- ⚡ **Error Resilient:** Continues even if some sources are down

---

## How It Works

### Daily Schedule

1. **5:00 AM** - Windows Task Scheduler triggers automation
2. **5:00-5:02 AM** - Python script runs Claude Code to search all sources
3. **5:02 AM** - Digest saved to `C:\Haripriya\News\digest-YYYY-MM-DD.md`
4. **When you wake up** - Fresh news ready in Obsidian vault

### Sources Checked Daily

**SQL Server:**
- brentozar.com/archive
- sqlserver.blog (Microsoft official)
- SQLServerCentral.com

**PostgreSQL:**
- Planet PostgreSQL
- Crunchy Data blog
- pgvector GitHub releases

**AI Infrastructure:**
- anthropic.com/news
- OpenAI blog

**Community:**
- Hacker News (filtered: database, PostgreSQL, SQL Server, AI, vector, embeddings)

---

## File Locations

### Core Files

```
C:\Haripriya\
├── News\
│   ├── digest-YYYY-MM-DD.md          # Daily digest output
│   ├── research-log.txt               # Automation log
│   └── README-DailyNews-Setup.md      # This file
├── auto-research.py                   # Main automation script
├── run-research.ps1                   # PowerShell wrapper (backup)
└── run-research.bat                   # Batch wrapper (backup)

C:\Users\harip\.claude\
├── skills\
│   └── research.txt                   # /research skill definition
└── settings.json                      # Auto-approval permissions
```

### What Each File Does

**`auto-research.py`**
- Main automation script that runs at 5 AM
- Calls Claude Code with research prompt
- Handles errors and logging

**`research.txt`** (Skill)
- Defines the `/research` command
- You can run manually: Just type `/research` in Claude Code
- Contains all source lists and formatting rules

**`settings.json`**
- Auto-approves WebSearch, WebFetch, Read, Write permissions
- Prevents permission prompts during automation

**`research-log.txt`**
- Tracks every automation run
- Shows timestamps, errors, success/failure
- Check this if digest doesn't generate

---

## The /research Skill

You can run research manually anytime:

```bash
# In Claude Code, just type:
/research
```

This will generate a fresh digest for today.

### Skill Configuration

Location: `C:\Users\harip\.claude\skills\research.txt`

The skill automatically:
1. Searches all configured sources
2. Filters Hacker News stories
3. Deduplicates content
4. Formats as markdown
5. Generates blog post ideas
6. Saves to Obsidian vault

---

## Scheduled Task Details

**Task Name:** `DailyDatabaseResearch`
**Trigger:** Daily at 5:00 AM
**Action:** Run Python script `C:\Haripriya\auto-research.py`

### View Task Status

```powershell
# Check task details
schtasks /query /tn "DailyDatabaseResearch" /fo LIST

# Check next run time
schtasks /query /tn "DailyDatabaseResearch"
```

### Run Manually

```powershell
# Trigger the task now (for testing)
schtasks /run /tn "DailyDatabaseResearch"

# Check the log after running
cat C:\Haripriya\News\research-log.txt
```

### Disable/Enable

```powershell
# Temporarily disable (e.g., on heavy usage days)
schtasks /change /tn "DailyDatabaseResearch" /disable

# Re-enable
schtasks /change /tn "DailyDatabaseResearch" /enable
```

### Delete Task

```powershell
# Remove automation completely
schtasks /delete /tn "DailyDatabaseResearch" /f
```

---

## Claude Pro Usage

### Daily Usage Impact

The automated research uses **~5-10% of your daily Claude Pro allowance**.

**Timeline:**
- Your limit resets at **11 AM Eastern Time**
- Task runs at **5 AM** (well after previous day's reset)
- Leaves **90-95%** of your allowance for the rest of the day

### If You Hit Limits

If you're doing heavy Claude usage:
1. Check the log: `cat C:\Haripriya\News\research-log.txt`
2. You'll see: "You've hit your limit · resets 11am"
3. The digest will generate the next day when limit resets
4. Or temporarily disable the task on busy days

---

## Customization

### Change Run Time

```powershell
# Change to 6 AM instead of 5 AM
schtasks /change /tn "DailyDatabaseResearch" /st 06:00
```

### Add/Remove Sources

Edit the skill file: `C:\Users\harip\.claude\skills\research.txt`

```markdown
SQL Server:
- brentozar.com/archive
- sqlserver.blog
- SQLServerCentral.com
- YOUR_NEW_SOURCE_HERE  # Add new sources
```

### Change Output Location

Edit `auto-research.py` line with output path:

```python
# Change this line:
5. Save to: C:\\Haripriya\\News\\digest-{date}.md

# To your preferred location:
5. Save to: C:\\Your\\Custom\\Path\\digest-{date}.md
```

### Adjust Blog Post Ideas

Edit `research.txt` to customize the "Content Ideas for Substack" section:

```markdown
- Look for: technical deep-dives, controversies, new techniques, migration stories
- Skip: basic tutorials, vendor announcements
```

Add your own preferences for what makes good blog content.

---

## Troubleshooting

### Digest Not Generated

**Check the log:**
```bash
tail -50 C:\Haripriya\News\research-log.txt
```

**Common issues:**

1. **"You've hit your limit"**
   - Wait until 11 AM Eastern for reset
   - Digest will generate tomorrow

2. **"Unknown skill: research"**
   - Skill not loading in automated mode
   - Run manually: `/research` in Claude Code

3. **"Permission denied"**
   - Check settings.json has permissions configured
   - See: `C:\Users\harip\.claude\settings.json`

4. **No file created**
   - Check task ran: `schtasks /query /tn "DailyDatabaseResearch"`
   - Check log for errors

### Task Didn't Run

```powershell
# Verify task exists
schtasks /query /tn "DailyDatabaseResearch"

# Check if disabled
schtasks /query /tn "DailyDatabaseResearch" /fo LIST | findstr "Status"

# Re-create if needed
python C:\Haripriya\auto-research.py
```

### Source Unavailable

The automation handles this gracefully. Check the digest - you'll see:

```markdown
[Source Name] unavailable
```

The digest generates with remaining sources.

---

## Digest Format

Each daily digest includes:

### 1. SQL Server Updates
- Latest releases and updates
- Security alerts
- Tool updates (SSMS, Azure Data Studio)
- Community insights from Brent Ozar

### 2. PostgreSQL Updates
- Version releases
- Extension updates (pgvector, etc.)
- Community events and conferences
- Performance tips from Crunchy Data

### 3. AI Infrastructure
- Anthropic announcements
- OpenAI updates
- Model releases
- Infrastructure news

### 4. Industry Pulse - Hacker News
- Top 20 stories from last 2 days
- Filtered for: database, PostgreSQL, SQL Server, AI, vector, embeddings
- Includes points and discussion links

### 5. Content Ideas for Substack
- 2-3 blog post ideas based on the day's news
- Focus on: controversies, deep-dives, new techniques, migration stories
- Skips: basic tutorials, simple vendor announcements

---

## Maintenance

### Weekly

✅ Check `research-log.txt` for any recurring errors
✅ Verify digests are generating successfully

### Monthly

✅ Review sources - add/remove as needed
✅ Update skill if news sources change URLs
✅ Check digest quality and adjust prompts if needed

### As Needed

✅ Disable on vacation/heavy usage days
✅ Update Python/Claude Code if prompted
✅ Adjust run time if schedule changes

---

## Quick Reference Commands

```bash
# Run research manually
/research

# Check scheduled task
schtasks /query /tn "DailyDatabaseResearch"

# View recent log
tail -20 C:\Haripriya\News\research-log.txt

# Test automation
schtasks /run /tn "DailyDatabaseResearch"

# Disable automation
schtasks /change /tn "DailyDatabaseResearch" /disable

# Enable automation
schtasks /change /tn "DailyDatabaseResearch" /enable

# View today's digest
cat C:\Haripriya\News\digest-$(date +%Y-%m-%d).md
```

---

## Support & Modifications

### To Modify

1. **Sources:** Edit `C:\Users\harip\.claude\skills\research.txt`
2. **Schedule:** Use `schtasks /change`
3. **Output format:** Edit skill prompts
4. **Permissions:** Edit `C:\Users\harip\.claude\settings.json`

### Backup Your Configuration

```bash
# Backup skill
cp C:\Users\harip\.claude\skills\research.txt C:\Haripriya\research-skill-backup.txt

# Backup settings
cp C:\Users\harip\.claude\settings.json C:\Haripriya\settings-backup.json

# Backup automation script
cp C:\Haripriya\auto-research.py C:\Haripriya\auto-research-backup.py
```

---

## What's Configured

✅ **Windows Scheduled Task** - Runs at 5 AM daily
✅ **Claude Code Skill** - `/research` command available
✅ **Auto-Permissions** - No prompts for WebSearch/WebFetch
✅ **Python Automation** - Handles execution and logging
✅ **Error Handling** - Continues even if sources fail
✅ **Deduplication** - Same story shown once with all URLs
✅ **Content Curation** - Blog post ideas generated
✅ **Obsidian Integration** - Saves to your vault

---

**Status:** 🟢 Active and Running
**Next Digest:** Tomorrow at 5:00 AM
**Location:** `C:\Haripriya\News\`

*Automation created: February 12, 2026*
*Using Claude Code + Windows Task Scheduler*
