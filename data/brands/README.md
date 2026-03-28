# Brand Data

Place manually sourced restaurant/brand CSV data in this directory.

> ⚠️ **No scraping.** All data is provided manually by the ops team (per PRD guardrails).

## Expected Sources
- Manually sourced from Google My Business listings
- Manually sourced from Zomato listings
- Direct collection from restaurant owners

## Expected Format

| Column | Type | Required |
|---|---|---|
| restaurant_name | text | ✅ |
| phone | +91... | ✅ |
| locality | text | ✅ |
| full_address | text | ❌ |
| cuisine_type | text | ❌ |
| zomato_url | URL | ❌ |
| google_maps_link | URL | ❌ |
| zomato_rating | number | ❌ |
| popular_dishes | text | ❌ |
| instagram_handle | @handle | ❌ |
