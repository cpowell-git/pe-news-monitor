# RCSI News Digest

Automated daily news monitoring for River Capital. Discovers investment-relevant articles across QSR, franchising, PE/M&A, retail, and Australian markets. Summarises paywalled AFR and The Australian articles via Claude API.

## Outputs

1. **Email digest** — HTML email grouped by topic, sent to your inbox each morning
2. **Excel news log** (`news_log.xlsx`) — Running tracker with Date, Headline, Source, URL, Topic, and Summary columns. Auto-deduplicates by URL.

## Setup

### 1. Create a private GitHub repo

```bash
git init pe-news-monitor
cd pe-news-monitor
# Copy all project files in, then:
git add .
git commit -m "Initial commit"
git remote add origin https://github.com/YOUR_USERNAME/pe-news-monitor.git
git push -u origin main
```

### 2. Add secrets in GitHub

Go to **Settings → Secrets and variables → Actions → New repository secret** and add:

| Secret               | Value                              |
|----------------------|------------------------------------|
| `SMTP_SERVER`        | `smtp.gmail.com`                   |
| `SMTP_PORT`          | `587`                              |
| `SMTP_USER`          | Your Gmail address                 |
| `SMTP_PASS`          | Gmail App Password (not regular)   |
| `EMAIL_TO`           | Your River Capital email           |
| `EMAIL_FROM`         | Your Gmail address                 |
| `AFR_EMAIL`          | AFR login email                    |
| `AFR_PASSWORD`       | AFR login password                 |
| `AUSTRALIAN_EMAIL`   | The Australian login email         |
| `AUSTRALIAN_PASSWORD`| The Australian login password      |
| `ANTHROPIC_API_KEY`  | Your Anthropic API key             |

### 3. Verify the workflow

- Go to **Actions** tab in your repo
- Click **RCSI News Digest** → **Run workflow** to trigger manually
- Check the run log for any errors

### 4. Schedule

The workflow runs daily at **9pm UTC** (7am AEST / 8am AEDT). Edit the cron in `.github/workflows/news_digest.yml` to adjust.

## Local Testing

```bash
cp .env.example .env
# Fill in your real credentials in .env
pip install -r requirements.txt
python pe_news_monitor.py
```

## Customisation

- **Topics & keywords**: Edit the `TOPICS` dict in `pe_news_monitor.py`
- **Approved sources**: Edit `REPUTABLE_SOURCES`
- **RSS feeds**: Edit `RSS_FEEDS`
- **Google News queries**: Edit `GOOGLE_NEWS_QUERIES`
- **Summary length**: Adjust the prompt in `summarise_article()`

## Notes

- The AFR and Australian login endpoints may change over time — if logins start failing, the auth functions in the script will need updating. The script will still run and capture articles; it just won't produce summaries for paywalled content.
- Monday runs look back 72 hours to capture weekend coverage; all other days look back 24 hours.
- The Excel file is committed back to the repo after each run, so you can always pull the latest version.
