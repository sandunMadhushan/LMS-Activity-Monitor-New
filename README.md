# LMS Activity Monitor

A web-based system to monitor multiple Moodle LMS instances for new activities, assignments, and course content. Get notifications twice daily about any new additions to your courses. **Now deployed on AWS EC2 with automated GitHub Actions scanning and deployment.**

---

## 🌐 Live Deployments

| Platform    | Status     | Type                  | URL                                     |
| ----------- | ---------- | --------------------- | --------------------------------------- |
| **AWS EC2** | 🟢 Primary | Always-on (no sleep) | [lms.madhushan.me](https://lms.madhushan.me) |

_Deployment is automatically updated via GitHub Actions!_

---

## 🌟 Features

- 🔍 **Multi-LMS Support**: Monitor OUSL and Rajarata University Moodle instances
- 📧 **Email Notifications**: Get detailed notifications about new content
- 📱 **Mobile Push Notifications**: Get instant alerts on your phone via [Ntfy.sh](https://ntfy.sh) (free, no registration!)
- 📄 **PDF Reports**: Beautiful PDF reports with new activities emailed as attachments
- 🌐 **Web Dashboard**: View all changes in a user-friendly interface (deployed on AWS EC2)
- 🔄 **Automated Checks**: Runs twice daily (9 AM and 9 PM Sri Lanka Time) via GitHub Actions
- 📊 **Activity Tracking**: Monitors assignments, resources, forums, quizzes, and more
- 📅 **Calendar Integration**: Syncs deadlines to your calendar automatically
- ⏰ **Deadline Reminders**: Get notified about upcoming deadlines via email and mobile
- 🔐 **Secure**: Credentials stored as GitHub Secrets

## 📝 Article

- [Never miss a deadline again: How I automated my university LMS with Python + GitHub Actions](https://medium.com/@sandunmadhushan/never-miss-a-deadline-again-how-i-automated-my-university-lms-with-python-github-actions-d729fe81e80c)
- [From Render to AWS EC2: How I made my LMS automation fully always-on](https://medium.com/@sandunmadhushan/from-render-to-aws-ec2-how-i-made-my-lms-automation-fully-always-on-0a2b7ae8c3c1)

## 🚀 Quick Start

### Live Dashboard

**Live Deployment (AWS):**

- **AWS EC2**: https://lms.madhushan.me ✅

### Deployment Options

1. **Production (Recommended)**: Deployed on AWS EC2 + GitHub Actions
2. **Local Development**: Run locally for testing

## 📦 Setup Instructions

### 1. Local Development Setup

1. Clone this repository
2. Install Python 3.9 or higher
3. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

4. Copy `.env.example` to `.env` and fill in your credentials:

   ```bash
   cp .env.example .env
   ```

5. Edit `.env` with your actual credentials:
   - **OUSL credentials**: Your Open University username and password
   - **RUSL credentials**: Your Rajarata University username and password
   - **Email settings**: Gmail address and app password (see below)

### 2. Gmail Setup for Notifications

To send email notifications:

1. Go to your Google Account settings
2. Enable 2-Factor Authentication
3. Generate an App Password:
   - Go to Security → 2-Step Verification → App passwords
   - Generate a password for "Mail"
   - Use this password in `.env` as `EMAIL_PASSWORD`

### 3. Mobile Push Notifications Setup (Optional but Recommended!)

Get **instant notifications on your phone** in under 5 minutes:

#### Quick Setup:

1. **Install Ntfy app**: [Android (Google Play)](https://play.google.com/store/apps/details?id=io.heckel.ntfy) / [iOS (App Store)](https://apps.apple.com/us/app/ntfy/id1625396347)
2. **Subscribe to a unique topic** in the app (e.g., `lms-monitor-xyz789-secret`)
3. **Add to your `.env` file**:
   ```bash
   NTFY_TOPIC=lms-monitor-xyz789-secret
   NTFY_SERVER=https://ntfy.sh
   ```
4. **Done!** You'll now get instant push notifications on your phone 📱

**📖 See [`docs/NTFY_SETUP_GUIDE.md`](docs/NTFY_SETUP_GUIDE.md) for detailed setup instructions with screenshots.**

**🔒 Security Tip**: Use a long, random topic name (e.g., `lms-monitor-abc123def456xyz789-secret`) to keep your notifications private!

**✅ Test it**: After adding to `.env`, use the web dashboard's "Test Mobile Notification" button or run:

```bash
curl -d "Hello from LMS Monitor!" ntfy.sh/your-topic-name
```

### 4. Running Locally

Run the scraper manually:

```bash
python scraper.py
```

Run the web dashboard:

```bash
python app.py
```

Then visit: `http://localhost:5000`

### 5. GitHub Actions Setup (Automated Scanning)

The system uses GitHub Actions to automatically scan both Moodle instances twice daily:

1. **Fork this repository**
2. **Go to Settings → Secrets and variables → Actions**
3. **Add the following secrets:**
   - `OUSL_USERNAME` - Your OUSL Moodle username
   - `OUSL_PASSWORD` - Your OUSL Moodle password
   - `RUSL_USERNAME` - Your RUSL Moodle username
   - `RUSL_PASSWORD` - Your RUSL Moodle password
   - `EMAIL_SENDER` - Your Gmail address
   - `EMAIL_PASSWORD` - Gmail app password
   - `EMAIL_RECIPIENT` - Email to receive notifications
   - `NTFY_TOPIC` - Your unique Ntfy topic name (optional - for mobile notifications)

4. **Automated Schedule**:
   - Runs at **9:00 AM Sri Lanka Time** (3:30 AM UTC)
   - Runs at **9:00 PM Sri Lanka Time** (3:30 PM UTC)
5. **What it does automatically:**
   - ✅ Scans both OUSL and RUSL Moodle sites
   - ✅ Detects new activities and assignments
   - ✅ Sends email notifications for new content
   - ✅ Generates and emails PDF reports with new activities
   - ✅ Sends mobile push notifications (if configured)
   - ✅ Sends deadline reminders (7 days in advance) via email and mobile
   - ✅ Updates the database in the repository (with Sri Lanka time timestamps)
   - ✅ Uploads database as workflow artifact

### 6. Cloud Deployment (Web Dashboard)

This project was migrated from Render to AWS because always-on hosting was needed without free-tier sleeping behavior.

#### AWS EC2 (Current Production) ☁️

1. **Server stack:**
   - Ubuntu EC2 instance
   - Nginx reverse proxy (`lms.madhushan.me` → `127.0.0.1:5000`)
   - PM2 process manager for always-on service
   - Gunicorn running Flask app

2. **Automated deployment on every push:**
   - Workflow: `.github/workflows/deploy-ec2.yml`
   - Trigger: push to `master` / `main` (also supports manual dispatch)
   - Action: SSH into EC2 → pull latest code → install requirements → restart PM2

3. **Automated scanning (unchanged):**
   - Workflow: `.github/workflows/monitor.yml`
   - Runs at 9:00 AM and 9:00 PM Sri Lanka Time
   - Updates `lms_data.db` in the repository

4. **Live URL:** https://lms.madhushan.me

**Important Notes:**

- ✅ Daily scan jobs run via GitHub Actions
- ✅ EC2 serves dashboard 24/7 without sleeping
- ✅ Pushes to repository can deploy immediately via `deploy-ec2.yml`
- ✅ Optional cron pull script can be kept as fallback

### 7. Manual GitHub Actions Run

Go to Actions → LMS Monitor → Run workflow

## 🔄 How It Works

### Automated Workflow (GitHub Actions)

1. **Scheduled Trigger**: Runs at 9 AM & 9 PM Sri Lanka Time
2. **Authentication**: Logs into both OUSL and RUSL Moodle instances
3. **Course Discovery**: Finds all your enrolled courses
4. **Content Scraping**: Extracts all activities, assignments, resources using Selenium + Chrome
5. **Change Detection**: Compares with previous scan to find new items
6. **PDF Report Generation**: Creates a professional PDF report with all new activities and upcoming deadlines
7. **Notifications**: Sends email with details of new content and PDF report attachment
8. **Deadline Check**: Identifies upcoming deadlines (next 7 days) and sends reminders
9. **Database Update**: Commits updated database back to GitHub repository with Sri Lanka time (UTC+5:30)
10. **Artifact Upload**: Stores database as workflow artifact (90-day retention)

### Web Dashboard (AWS EC2)

1. **Always Online**: Hosted on AWS EC2 for 24/7 access
2. **Real-time Data**: Uses the latest database from GitHub
3. **Calendar Sync**: Syncs deadlines to calendar events (Google Calendar compatible)
4. **Manual Scan Support**: Optional manual scan from web UI when Chrome/ChromeDriver and credentials are set on EC2
5. **Auto Deployment**: Pushes deploy via GitHub Actions SSH workflow

## 📊 What Gets Monitored

- ✅ New assignments (with deadlines)
- ✅ New resources (PDFs, files, links)
- ✅ New forum posts
- ✅ New quizzes and exams
- ✅ New pages and labels
- ✅ Course updates and announcements
- ✅ Assignment deadlines
- ✅ Calendar events from Moodle

## 🎨 Dashboard Features

- 📈 Statistics overview (courses, activities, deadlines)
- 📚 View all courses from both universities
- 🆕 See recent changes and new activities
- 📅 Upcoming deadlines (7-day view)
- 🔍 Search functionality
- 📧 Test email notifications
- � Test mobile push notifications
- �🔄 Manual calendar sync
- 🎯 Filter by LMS (OUSL/RUSL)

## Project Structure

```
lms-scraper/
├── .github/
│   └── workflows/
│       ├── monitor.yml          # GitHub Actions workflow for automated scanning
│       └── deploy-ec2.yml       # GitHub Actions workflow for EC2 auto-deploy
├── docs/                        # 📚 All documentation
│   ├── README.md               # Documentation index
│   ├── GETTING_STARTED.md      # Complete getting started guide
│   ├── SETUP_GUIDE.md          # Detailed setup instructions
│   ├── QUICK_REFERENCE.md      # Quick reference guide
│   ├── SCHEDULING.md           # Automatic scheduling documentation
│   ├── SCHEDULER_QUICKSTART.md # Scheduler quick start
│   ├── PROJECT_OVERVIEW.md     # Architecture and design
│   ├── IMPLEMENTATION_SUMMARY.md # Implementation details
│   └── SYSTEM_SUMMARY.md       # System features summary
├── static/
│   └── style.css               # Web dashboard CSS styles
├── templates/                   # Flask HTML templates
│   ├── base.html               # Base template
│   ├── index.html              # Main dashboard
│   ├── courses.html            # Courses page
│   ├── course_detail.html      # Course detail page
│   └── activities.html         # Activities page
├── tests/                       # 🧪 Test scripts
│   ├── README.md               # Test documentation
│   ├── test_setup.py           # System setup tests
│   └── test_course_names.py    # Course scraping tests
│
├── .env                         # Environment variables (create from .env.example)
├── .env.example                # Environment variables template
├── .gitignore                  # Git ignore rules
├── LICENSE                     # MIT License
├── README.md                   # This file - main documentation
├── requirements.txt            # Python dependencies
├── lms_data.db                 # SQLite database (created automatically)
│
├── app.py                      # 🌐 Flask web application
├── scraper.py                  # 🔍 Main scraping logic (OUSL & RUSL)
├── database.py                 # 💾 Database operations (SQLite)
├── calendar_scraper.py         # 📅 Calendar event scraper & deadline extractor
├── scheduler.py                # ⏰ Background task scheduler (APScheduler)
├── notifier.py                 # 📧 Email + mobile push notification system
├── pdf_report.py               # 📄 PDF report generator with activities & deadlines
│
├── start.sh                    # Linux/Mac startup script
└── start.bat                   # Windows startup script
```

### Core Components

- **`app.py`** - Flask web server with dashboard, routes, and API endpoints
- **`scraper.py`** - Selenium-based web scraper for OUSL and RUSL Moodle sites (~900 lines)
- **`database.py`** - SQLite database manager with all CRUD operations (~650 lines)
- **`calendar_scraper.py`** - iCalendar event fetcher and deadline extractor from text
- **`scheduler.py`** - APScheduler integration for automated twice-daily scanning
- **`notifier.py`** - Email (SMTP) + mobile push notification system (Ntfy.sh integration)
- **`pdf_report.py`** - Professional PDF report generator with ReportLab (~400 lines)

### Documentation

All detailed documentation is in the `docs/` folder:

- **Getting Started**: `docs/GETTING_STARTED.md` - New user guide
- **Setup Guide**: `docs/SETUP_GUIDE.md` - Detailed setup walkthrough
- **Quick Reference**: `docs/QUICK_REFERENCE.md` - Commands and common tasks
- **Scheduling**: `docs/SCHEDULING.md` - Auto-scan configuration
- **Architecture**: `docs/PROJECT_OVERVIEW.md` - System design and architecture

### Testing

Test scripts are in the `tests/` folder:

- **`test_setup.py`** - Validates environment setup and configuration
- **`test_course_names.py`** - Tests scraping functionality for both universities

Run tests:

```bash
python tests/test_setup.py
python tests/test_course_names.py
```

## 🛠️ Troubleshooting

### Scraper Issues (GitHub Actions)

- Check the Actions tab for detailed logs
- Make sure credentials are correct in GitHub Secrets
- Verify Moodle sites are accessible
- Chrome/ChromeDriver are automatically installed by the workflow

### Email Issues

- Verify Gmail app password is correct
- Check if 2FA is enabled on your Google Account
- Use App Passwords (not your regular password)
- Check spam folder for notifications

### Mobile Notification Issues

- **Check Ntfy App**: Ensure you're subscribed to the correct topic in the app
- **Test Topic**: Run `curl -d "Test message" ntfy.sh/your-topic-name` - if you receive this on your phone, the topic works!
- **Verify `.env`**: Ensure `NTFY_TOPIC` matches your app subscription exactly
- **Check Server**: Default is `https://ntfy.sh` (no trailing slash)
- **GitHub Actions**: Add `NTFY_TOPIC` to repository secrets for automated notifications

**📖 See [`docs/NTFY_SETUP_GUIDE.md`](docs/NTFY_SETUP_GUIDE.md) for detailed troubleshooting.**

### GitHub Actions Issues

- Check Actions tab for error logs
- Verify all 8 secrets are set correctly (including `NTFY_TOPIC`)
- Ensure repository has write permissions enabled
- Database commits require `permissions: contents: write` (already configured)

### Cloud Deployment Issues

**AWS EC2 + Nginx + PM2:**

- Check process status: `pm2 status`
- Check app logs: `pm2 logs lms-monitor --lines 100`
- Check Nginx config test: `sudo nginx -t`
- Reload Nginx after config changes: `sudo systemctl reload nginx`
- Verify DNS points to EC2 public IP
- Verify SSL cert status: `sudo certbot certificates`
- If deploy workflow fails, verify GitHub secrets: `EC2_HOST`, `EC2_USER`, `EC2_SSH_KEY`

### Calendar Sync Issues

- Check that deadlines exist in the database
- Calendar events are stored in the `deadlines` table with `source='calendar'`
- Duplicate events are automatically prevented
- Delete old calendar events manually if needed: `DELETE FROM deadlines WHERE source='calendar'`

## 🔒 Security Notes

- ✅ Never commit `.env` file (already in .gitignore)
- ✅ Use GitHub Secrets for all sensitive data
- ✅ Credentials are encrypted in transit (HTTPS)
- ✅ Database is public but contains no sensitive info (only course metadata)
- ✅ Use Gmail App Passwords (not your main password)
- ✅ Repository can be private if needed (GitHub Actions work on private repos)

## 📞 Support

If you encounter issues:

1. Check the GitHub Actions logs (Actions tab)
2. Review EC2 deployment and PM2 logs
3. Check error messages in email notifications
4. Ensure all dependencies are installed (`requirements.txt`)
5. Verify Moodle sites are accessible from your network
6. See detailed docs in `docs/` folder

## 🚀 Recent Updates

- **Recent Updates**
- ✅ **PDF Reports** - Professional PDF reports with new activities emailed as attachments (ReportLab)
- ✅ **Sri Lanka Time Commits** - Database commits now show correct Sri Lanka time (UTC+5:30)
- ✅ **Python 3.13 Compatible** - Updated dependencies for latest Python version
- ✅ **Mobile Push Notifications** - Get instant alerts on your phone via Ntfy.sh!
- ✅ **Dual Notification System** - Both email and mobile notifications
- ✅ GitHub Actions scheduled for 9 AM & 9 PM Sri Lanka Time
- ✅ Added calendar sync functionality
- ✅ Automated deadline reminders (7 days in advance) via email and mobile
- ✅ Fixed database tracking in git
- ✅ Migrated web dashboard hosting from Render to AWS EC2
- ✅ Added auto-deploy workflow (`deploy-ec2.yml`) for push-to-EC2 updates
- ✅ Added write permissions for workflow commits
- ✅ Removed artifact download step (using git-tracked database)

## 📚 Documentation

For more detailed information, see the `docs/` folder:

### Deployment Guides:

- **`DEPLOYMENT_GUIDE.md`** - Cloud deployment guide
- **`.github/workflows/deploy-ec2.yml`** - Push-to-EC2 deployment workflow
- **`.github/workflows/monitor.yml`** - Scheduled scanning workflow

### Feature Guides:

- **`NTFY_SETUP_GUIDE.md`** - Mobile push notifications setup (5-minute guide)
- **`GETTING_STARTED.md`** - New user guide
- **`SETUP_GUIDE.md`** - Detailed setup walkthrough
- **`SCHEDULING.md`** - Auto-scan configuration
- **`PROJECT_OVERVIEW.md`** - System architecture

## 📄 License

MIT License - Feel free to modify and use for your needs!

---

**Made with ❤️ for OUSL and Rajarata University students**
