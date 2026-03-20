# Brand Data

Place scraped restaurant/brand CSV data in this directory.

## Expected Sources
- Google My Business scrape
- Zomato scrape
- Manual collection

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
