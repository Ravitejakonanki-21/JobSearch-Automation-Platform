# 🚀 JobSearch Automation Platform

An automated **fresher tech job discovery and notification system** built with **n8n** and **Telegram**.

The workflow continuously monitors multiple public job-board APIs, filters opportunities based on role, location, freshness, and experience requirements, removes duplicate postings, and sends relevant jobs directly to subscribed Telegram users.

---

## ✨ Features

- 🔎 Automated job discovery from multiple job sources
- ⚡ Scheduled polling using n8n
- 🏢 Supports company career boards using:
  - Greenhouse
  - Lever
  - Ashby
  - Adzuna
- 🎯 Role-based filtering for software, backend, Python, data, AI/ML and analyst roles
- 📍 Location filtering for:
  - Hyderabad
  - Bangalore / Bengaluru
  - Chennai
  - Remote India
- 🎓 Fresher / entry-level filtering
- ⏱️ Filters jobs based on experience requirements
- ♻️ Duplicate job detection
- 💾 Persistent job tracking using n8n Data Tables
- 📲 Real-time Telegram notifications
- 👥 Telegram subscription management
- `/start` to subscribe
- `/stop` to unsubscribe
- 🔗 Direct application links
- 📝 Automatic job-description summarization
- 🛡️ Uses official public job APIs instead of web scraping

---

## 🏗️ Architecture

```text
                    ┌─────────────────────┐
                    │   n8n Scheduler     │
                    │   Scheduled Trigger  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │  Job Sources Config │
                    │ Greenhouse / Lever  │
                    │ Ashby / Adzuna      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Public Job APIs     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Normalize & Filter  │
                    │ Role + Location     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Duplicate Check     │
                    │ n8n Data Table      │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Fetch Description   │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Experience Filter   │
                    │ Fresher / 0-1 Years │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Telegram Message    │
                    │ Builder             │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ Telegram Bot        │
                    │ Job Notification    │
                    └─────────────────────┘
```

---

## 🔄 How It Works

### 1. Scheduled Job Search

The n8n workflow runs automatically using a scheduled trigger.

Each configured source has its own polling interval to avoid unnecessary API requests.

### 2. Job Source Configuration

Job sources are maintained in a configurable list.

Example:

```javascript
{
  company: 'InMobi',
  type: 'greenhouse',
  slug: 'inmobi',
  enabled: true,
  pollEveryMinutes: 1
}
```

New companies can be added by providing their supported ATS type and board slug.

### 3. Public Job APIs

The workflow retrieves job postings from:

| Source | API |
|---|---|
| Greenhouse | Greenhouse Job Board API |
| Lever | Lever Postings API |
| Ashby | Ashby Job Board API |
| Adzuna | Adzuna Jobs API |

The workflow is designed around **official public APIs and does not require website login, CAPTCHA bypass, or scraping**.

### 4. Job Normalization

Different job boards return different JSON structures.

The workflow converts these responses into a common format:

```text
job_url
title
company
location
posted_at
description
source
```

This allows the same filtering logic to work across different job sources.

### 5. Role Filtering

The workflow identifies relevant entry-level technology roles such as:

- Software Engineer
- Software Developer
- Software Development Engineer
- Backend Developer
- Python Developer
- Data Analyst
- Data Scientist
- Machine Learning
- AI / ML
- Associate Software Engineer
- Graduate Engineer Trainee
- Graduate Trainee
- Analyst
- Fresher
- New Grad
- Entry Level

Senior roles are excluded using configurable title patterns.

### 6. Location Filtering

The current workflow targets:

```text
Hyderabad
Bangalore / Bengaluru
Chennai
Remote India
```

These filters can be modified directly inside the workflow.

### 7. Experience Filtering

Job descriptions are analyzed for experience requirements.

The current configuration targets:

```text
Fresher
Entry Level
New Grad
Graduate Engineer Trainee
2026 graduates
0-1 years
No prior experience
```

Jobs requiring more than the configured experience threshold are rejected.

### 8. Duplicate Detection

Each job's application URL is used as its unique identifier.

The workflow checks the `job_finder_seen_jobs` Data Table before processing a job.

This prevents the same opportunity from being repeatedly sent to Telegram.

### 9. Telegram Notifications

Matching jobs are formatted into a concise Telegram message containing:

```text
🔥 NEW JOB

💼 Job Title
🏢 Company
📍 Location
🎓 Experience

📝 Job Summary

🔗 Apply:
Job URL

Source
```

---

## 🤖 Telegram Bot

The workflow also provides subscription management through Telegram.

### Subscribe

Send:

```text
/start
```

The user is added to the active subscriber list.

### Unsubscribe

Send:

```text
/stop
```

The user's subscription is deactivated.

Only active subscribers receive new job notifications.

---

## 🛠️ Tech Stack

- **n8n** — Workflow automation
- **JavaScript** — Job normalization and filtering logic
- **Telegram Bot API** — Notifications and subscriptions
- **Greenhouse API** — Company job listings
- **Lever API** — Company job listings
- **Ashby API** — Company job listings
- **Adzuna API** — Job search
- **n8n Data Tables** — Job deduplication and subscriber management
- **REST APIs** — Data retrieval and integration

---

## 📁 Repository Structure

```text
JobSearch-Automation-Platform/
│
├── Automated Job Finder - Fresher Tech Jobs to Telegram.json
│
└── README.md
```

The JSON file contains the complete n8n workflow and can be imported directly into an n8n instance.

---

## ⚙️ Setup

### Prerequisites

You need:

- A running n8n instance
- A Telegram Bot
- Telegram Bot API credentials
- Adzuna API credentials if using Adzuna searches
- Access to the public job-board APIs

---

### 1. Clone the Repository

```bash
git clone https://github.com/Ravitejakonanki-21/JobSearch-Automation-Platform.git

cd JobSearch-Automation-Platform
```

---

### 2. Import the n8n Workflow

Open your n8n instance.

Go to:

```text
Workflows → Import from File
```

Select:

```text
Automated Job Finder - Fresher Tech Jobs to Telegram.json
```

---

### 3. Configure Telegram

Create a Telegram bot using **BotFather** and obtain the bot token.

Configure the Telegram credential in n8n and attach it to the Telegram nodes.

---

### 4. Configure Adzuna

If you want to use Adzuna searches, configure your Adzuna API credentials in n8n.

The workflow uses Adzuna searches for:

```text
Hyderabad
Bangalore
Chennai
Remote India
```

---

### 5. Configure Job Sources

The `Job Sources Config` node contains the company/job-board configuration.

You can enable or disable sources:

```javascript
enabled: true
```

or:

```javascript
enabled: false
```

You can also change polling frequency:

```javascript
pollEveryMinutes: 5
```

---

### 6. Customize Job Filters

The workflow's JavaScript filtering logic can be modified to change:

- Target roles
- Excluded seniority levels
- Target cities
- Maximum experience
- Maximum job age
- Fresher keywords

---

## 🔐 Security

**Never commit credentials or API keys to GitHub.**

Keep the following inside n8n credentials/environment configuration:

- Telegram Bot Token
- Adzuna API Key
- Adzuna App ID
- Other private credentials

The workflow is designed to keep API credentials outside the job-source configuration.

---

## 🚫 What This Project Does Not Do

This project does **not**:

- Automatically apply to jobs
- Bypass CAPTCHAs
- Log into job portals
- Scrape authenticated websites
- Submit applications without user interaction

It focuses on **job discovery, filtering, deduplication, and notification**.

---

## 🎯 Use Cases

This automation can be useful for:

- Fresh graduates
- Entry-level software developers
- New-grad candidates
- Students looking for internships and graduate roles
- Job seekers who want instant Telegram notifications
- Developers building automated career-search systems

---

## 🚀 Future Improvements

Potential improvements include:

- [ ] AI-powered resume-to-job matching
- [ ] Job relevance scoring
- [ ] Resume-specific keyword matching
- [ ] Gmail application-status tracking
- [ ] Google Sheets application tracker
- [ ] Application deadline detection
- [ ] Company-specific filters
- [ ] User-configurable Telegram preferences
- [ ] Multiple role profiles
- [ ] Dashboard for job analytics
- [ ] Application tracking
- [ ] Daily/weekly job summaries

---

## 📊 Example Workflow

```text
New Job Posted
      ↓
Fetch from Public API
      ↓
Normalize Job Data
      ↓
Role Match?
      ↓
Location Match?
      ↓
Already Seen?
      ↓
Fetch Description
      ↓
Experience Check
      ↓
Fresher / 0-1 Years?
      ↓
Build Telegram Message
      ↓
Send Notification
      ↓
Store Job URL
```

---

## 👨‍💻 Author

**Ravi Teja Konanki**

B.Tech — Artificial Intelligence

GitHub:  
https://github.com/Ravitejakonanki-21

LinkedIn:  
https://linkedin.com/in/ravi-teja-konanki-432391264

Portfolio:  
https://ravitejakonanki.vercel.app

---

## ⭐ If You Find This Useful

Give the repository a ⭐ and feel free to adapt the workflow for your own job-search requirements.

---

## 📄 License

This project is provided for educational and personal automation purposes.

Check the terms and usage limits of each external job API before deploying the workflow at scale.
