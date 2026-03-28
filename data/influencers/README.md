# Influencer Data

Place raw influencer CSV/spreadsheet data in this directory.

> ⚠️ **No scraping.** All data is manually sourced or enriched via instagrapi API (per PRD guardrails).

## Expected Format

| Column | Type | Required |
|---|---|---|
| instagram_handle | @handle | ✅ |
| full_name | text | ❌ (auto-enriched via instagrapi) |
| email | email | ❌ |
| phone | +91... | ❌ |
| follower_count | number | ❌ (auto-enriched via instagrapi) |
| city | text | ✅ |
| content_niche | food/fashion/tech | ❌ |

