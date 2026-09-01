# AI4Bio ArXiv Daily - Usage Instructions

## Overview

AI4Bio ArXiv Daily fetches recent AI for Biology papers from arXiv, organizes them by category, and publishes the accumulated collection to the repository README and GitHub Pages.

## Usage

### Manual Trigger

1. Open the repository's **Actions** tab.
2. Select **Run Arxiv Papers Daily** to fetch papers, or **Run Update Paper Links Weekly** to run link maintenance.
3. Select **Run workflow**.

### Automatic Scheduling

- **Daily Papers:** Runs every day at 22:37 UTC, as configured in `.github/workflows/ai4bio-arxiv-daily.yml`.
- **Link Maintenance:** Runs every Monday at 08:00 UTC, as configured in `.github/workflows/update-paper-links.yml`.

The former Papers with Code integration is deprecated, so link maintenance currently preserves existing code-link values instead of discovering new repositories.

## Daily GitHub Issue Notifications

When the daily run finds papers not already present in the main JSON database, it posts a Markdown summary to a fixed GitHub Issue. A paper matching multiple categories appears once in the notification with all matched categories listed. No comment is posted when there are no new papers.

To enable notifications:

1. Create a persistent Issue such as **AI4Bio ArXiv Daily Paper Notifications** and leave it open.
2. Subscribe to that Issue.
3. Open **Settings → Secrets and variables → Actions → Variables** in the repository.
4. Create a repository variable named `DAILY_PAPER_ISSUE_NUMBER` whose value is the Issue number, without the `#` prefix.
5. In your personal GitHub notification settings, enable email delivery for subscribed Issue activity.

The workflow uses its built-in `GITHUB_TOKEN`; no SMTP password, email credential, or third-party webhook is required. Workflow failures continue to use GitHub Actions notifications.

## Output Files

- `README.md` - Main paper collection.
- `docs/ai4bio-arxiv-daily.json` - Data source for the main README.
- `docs/ai4bio-arxiv-daily-web.json` - Data source for GitHub Pages.
- `docs/index.md` - GitHub Pages version.
- `docs/ai4bio-arxiv-daily-wechat.json` - WeChat data, currently disabled.
- `docs/wechat.md` - WeChat Markdown, currently disabled.

## Configuration

Edit `config.yaml` to customize:

- Search categories and filters.
- Category display order.
- Maximum results fetched per category.
- arXiv request delay and retry count.
- README, GitHub Pages, and WeChat publication options.
- Daily Issue notification behavior.
- Output paths.

## Paper Categories

The default display order is:

1. Protein Structure & Engineering
2. Enzyme Design & Prediction
3. Antibody, Antigen & Vaccine
4. Genomics & Regulatory Sequence
5. Single-cell & Spatial Multi-omics
6. Drug Discovery & Interaction
7. Biological Generative & Foundation Models
8. Systems Biology & Knowledge Graphs
9. Medical Imaging & Evolution

## Requirements

- Python 3.10+
- `arxiv`
- `requests`
- `pyyaml`

## Troubleshooting

### GitHub Actions Failing

1. Check the workflow logs.
2. Confirm that Actions has `contents: write` and `issues: write` permissions.
3. Confirm that `DAILY_PAPER_ISSUE_NUMBER` exists and points to an open Issue.
4. Check `config.yaml` for syntax errors.

### No Notification Email

1. Confirm that the Issue received a comment.
2. Confirm that you subscribed to the Issue.
3. Check your personal GitHub email notification settings.
4. Check your email spam and filtering rules.

### No Papers Found

- Verify the filters in `config.yaml`.
- Check whether the arXiv API is accessible.
- Review the workflow logs for the generated search queries.

### arXiv HTTP 429

HTTP 429 means that arXiv is rate-limiting requests. The script reuses one API client across all categories, requests only the configured number of results per page, adds a random initial delay on GitHub Actions, and uses minute-level backoff for persistent 429 responses. The scheduled run starts away from the top of the hour to reduce contention. These controls can be adjusted with `arxiv_initial_jitter_seconds`, `arxiv_delay_seconds`, `arxiv_num_retries`, and `arxiv_retry_backoff_seconds` in `config.yaml`.
