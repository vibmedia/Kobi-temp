# 📊 Data Schema — Extensible Categories & Data Points

> This document defines the flexible data model for influencers and brands. All fields, categories, and subcategories can be **added or removed** without code changes — they are configuration-driven.

---

## 🎯 Design Principle: Everything is Configurable

```
┌──────────────────────────────────────────────────┐
│              Configuration Layer                  │
│  (categories.json / Google Sheets "Config" tab)   │
├──────────────────────────────────────────────────┤
│              Data Layer                           │
│  (Influencer rows / Brand rows in Sheets/DB)      │
├──────────────────────────────────────────────────┤
│              Agent Layer                          │
│  (Reads config dynamically, no hardcoded values)  │
└──────────────────────────────────────────────────┘
```

**To add/remove a data point:** Edit the config file or Config tab. No code changes needed.
**To add/remove a category:** Edit `categories.json`. Agents read this at runtime.

---

## 1. Influencer Data Points

### Core Fields (Required)

| Field | Type | Example | Removable? |
|-------|------|---------|-----------|
| `instagram_handle` | string | @foodie.delhi | ❌ Primary key |
| `full_name` | string | Priya Sharma | ❌ Required |
| `phone` | string | +91 98xxx xxxxx | ❌ Required for payouts |
| `city` | string | Delhi | ❌ Required for matching |
| `pipeline_status` | enum | Available | ❌ System-managed |

### Enrichment Fields (Auto-populated, Configurable)

| Field | Type | Source | Removable? |
|-------|------|--------|-----------|
| `follower_count` | number | instagrapi | ✅ |
| `following_count` | number | instagrapi | ✅ |
| `post_count` | number | instagrapi | ✅ |
| `engagement_rate` | decimal | Calculated | ✅ |
| `avg_likes` | number | Calculated | ✅ |
| `avg_comments` | number | Calculated | ✅ |
| `bio` | text | instagrapi | ✅ |
| `is_verified` | boolean | instagrapi | ✅ |
| `is_private` | boolean | instagrapi | ✅ |
| `profile_pic_url` | URL | instagrapi | ✅ |
| `quality_score` | 0-100 | AI Calculated | ✅ |
| `email` | email | Collected during onboarding | ✅ |

### Onboarding Fields (Collected via Chat)

| Field | Type | Example | Removable? |
|-------|------|---------|-----------|
| `area` | string | South Delhi, Hauz Khas | ✅ |
| `content_rate_reel` | number | ₹5,000 | ✅ |
| `content_rate_story` | number | ₹2,000 | ✅ |
| `deal_preference` | enum[] | [Paid, Barter, Both] | ✅ |
| `content_types` | enum[] | [Reel, Story, Post, Carousel] | ✅ |
| `upi_id` | string | priya@upi | ✅ |
| `bank_details` | JSON | {bank, account, ifsc} | ✅ |

### Points & Reputation Fields

| Field | Type | System-managed? |
|-------|------|----------------|
| `total_points` | number | ✅ Auto |
| `tier` | enum | ✅ Auto (Bronze/Silver/Gold/Platinum) |
| `jobs_completed` | number | ✅ Auto |
| `avg_rating` | decimal | ✅ Auto |
| `no_show_count` | number | ✅ Auto |
| `ban_status` | enum | ✅ Auto (Active/Warning/Banned) |
| `points_last_earned` | date | ✅ Auto (for 12-month decay) |

### Custom Fields (User-Defined)

| Field | Type | Purpose |
|-------|------|---------|
| `custom_1` through `custom_10` | any | Reserved for future expansion |
| `tags` | string[] | Free-form tags like "vegetarian", "reels specialist" |
| `notes` | text | Internal ops notes |

> **To add a new field:** Add a column header to the Influencer Pipeline sheet. The agent reads column headers dynamically.

---

## 2. Influencer Categories & Subcategories

```json
{
  "influencer_categories": {
    "niche": {
      "name": "Content Niche",
      "type": "multi-select",
      "values": [
        {
          "id": "food",
          "label": "Food & Restaurant",
          "subcategories": ["Fine Dining", "Street Food", "Cafe", "Home Cooking", "Food Review", "Recipe"]
        },
        {
          "id": "lifestyle",
          "label": "Lifestyle",
          "subcategories": ["Travel", "Fashion", "Fitness", "Wellness", "Home Decor"]
        },
        {
          "id": "tech",
          "label": "Technology",
          "subcategories": ["Gadgets", "Apps", "Gaming", "AI/SaaS"]
        }
      ],
      "allow_custom": true
    },

    "tier": {
      "name": "Follower Tier",
      "type": "single-select",
      "values": [
        {"id": "nano", "label": "Nano", "range": "1K-10K"},
        {"id": "micro", "label": "Micro", "range": "10K-50K"},
        {"id": "mid", "label": "Mid", "range": "50K-200K"},
        {"id": "macro", "label": "Macro", "range": "200K-1M"},
        {"id": "mega", "label": "Mega", "range": "1M+"}
      ],
      "allow_custom": false
    },

    "content_style": {
      "name": "Content Style",
      "type": "multi-select",
      "values": ["Cinematic", "Casual/Vlog", "Review", "Tutorial", "Comedy/Skit", "Aesthetic"],
      "allow_custom": true
    },

    "language": {
      "name": "Content Language",
      "type": "multi-select",
      "values": ["Hindi", "English", "Hinglish", "Punjabi", "Tamil", "Telugu", "Bengali"],
      "allow_custom": true
    },

    "availability": {
      "name": "Availability",
      "type": "single-select",
      "values": ["Full-time Creator", "Part-time / Weekends", "Student", "Working Professional"],
      "allow_custom": true
    }
  }
}
```

> **To add a new category:** Add a key to `influencer_categories`. The system reads this at startup.
> **To add a subcategory:** Add to the `subcategories` array. No code changes needed.
> **To remove:** Delete the entry. Existing data retains the old value but won't appear in dropdowns.

---

## 3. Brand Data Points

### Core Fields (Required)

| Field | Type | Example | Removable? |
|-------|------|---------|-----------|
| `restaurant_name` | string | The Delhi Kitchen | ❌ Primary key |
| `phone` | string | +91 98xxx xxxxx | ❌ Required for WA outreach |
| `locality` | string | Connaught Place | ❌ Required for matching |
| `pipeline_status` | enum | Raw Lead | ❌ System-managed |

### Enrichment Fields (Configurable)

| Field | Type | Source | Removable? |
|-------|------|--------|-----------|
| `contact_name` | string | Manual / WA conversation | ✅ |
| `full_address` | text | Manual | ✅ |
| `cuisine_type` | enum[] | Manual / AI classified | ✅ |
| `popular_dishes` | string[] | Manual | ✅ |
| `zomato_rating` | decimal | Manual lookup | ✅ |
| `google_rating` | decimal | Manual lookup | ✅ |
| `instagram_handle` | string | Manual | ✅ |
| `ig_follower_count` | number | instagrapi if handle exists | ✅ |
| `zomato_url` | URL | Manual | ✅ |
| `google_maps_link` | URL | Manual | ✅ |
| `price_range` | enum | Budget/Mid/Premium | ✅ |
| `seating_capacity` | number | Manual | ✅ |
| `delivery_only` | boolean | Manual | ✅ |

### Sales Fields (Tracked)

| Field | Type | Example |
|-------|------|---------|
| `lead_score` | 0-100 | AI calculated from reply patterns |
| `assigned_sales` | string | Rahul |
| `last_contacted` | datetime | 2026-03-28 |
| `total_spend` | number | ₹25,000 |
| `campaigns_completed` | number | 3 |
| `client_since` | date | 2026-03-01 |
| `preferred_deal_type` | enum | Barter / Paid / Both |

### Custom Fields

| Field | Type | Purpose |
|-------|------|---------|
| `custom_1` through `custom_10` | any | Reserved for future expansion |
| `tags` | string[] | Free-form, e.g., "high-value", "chain", "premium" |
| `notes` | text | Internal sales notes |

> **To add a new field:** Add a column header to the Brand Pipeline sheet.

---

## 4. Brand Categories & Subcategories

```json
{
  "brand_categories": {
    "cuisine": {
      "name": "Cuisine Type",
      "type": "multi-select",
      "values": [
        {
          "id": "north_indian",
          "label": "North Indian",
          "subcategories": ["Mughlai", "Punjabi", "Rajasthani", "Lucknowi", "Street Food"]
        },
        {
          "id": "south_indian",
          "label": "South Indian",
          "subcategories": ["Kerala", "Tamil", "Hyderabadi", "Andhra"]
        },
        {
          "id": "continental",
          "label": "Continental / Western",
          "subcategories": ["Italian", "French", "American", "Mediterranean"]
        },
        {
          "id": "asian",
          "label": "Asian",
          "subcategories": ["Chinese", "Japanese", "Thai", "Korean", "Vietnamese"]
        },
        {
          "id": "cafe",
          "label": "Cafe / Bakery",
          "subcategories": ["Coffee Shop", "Dessert Bar", "Bakery", "Tea House"]
        },
        {
          "id": "fast_food",
          "label": "Fast Food / QSR",
          "subcategories": ["Burgers", "Pizza", "Wraps", "Rolls"]
        }
      ],
      "allow_custom": true
    },

    "venue_type": {
      "name": "Venue Type",
      "type": "single-select",
      "values": ["Fine Dining", "Casual Dining", "Cafe", "Bar/Lounge", "QSR", "Cloud Kitchen", "Food Truck", "Dessert Parlor"],
      "allow_custom": true
    },

    "area": {
      "name": "Area (Delhi NCR)",
      "type": "single-select",
      "values": [
        {
          "id": "south_delhi",
          "label": "South Delhi",
          "subcategories": ["Hauz Khas", "GK", "Saket", "Mehrauli", "Lajpat Nagar", "Defence Colony"]
        },
        {
          "id": "central_delhi",
          "label": "Central Delhi",
          "subcategories": ["Connaught Place", "Khan Market", "Janpath", "Barakhamba"]
        },
        {
          "id": "west_delhi",
          "label": "West Delhi",
          "subcategories": ["Rajouri Garden", "Punjabi Bagh", "Janakpuri"]
        },
        {
          "id": "north_delhi",
          "label": "North Delhi",
          "subcategories": ["Kamla Nagar", "GTB Nagar", "Model Town"]
        },
        {
          "id": "east_delhi",
          "label": "East Delhi",
          "subcategories": ["Preet Vihar", "Laxmi Nagar", "Mayur Vihar"]
        },
        {
          "id": "gurgaon",
          "label": "Gurgaon",
          "subcategories": ["Cyber Hub", "Golf Course Road", "MG Road", "Sohna Road"]
        },
        {
          "id": "noida",
          "label": "Noida",
          "subcategories": ["Sector 18", "Sector 29", "Greater Noida"]
        }
      ],
      "allow_custom": true
    },

    "budget_tier": {
      "name": "Marketing Budget Tier",
      "type": "single-select",
      "values": [
        {"id": "starter", "label": "Starter", "range": "₹5K-₹15K"},
        {"id": "growth", "label": "Growth", "range": "₹15K-₹35K"},
        {"id": "premium", "label": "Premium", "range": "₹35K+"}
      ],
      "allow_custom": false
    }
  }
}
```

> **To add a new cuisine/area/venue:** Edit the `brand_categories` config. All agents, templates, and matching logic read from this config.

---

## 5. How to Add / Remove Data Points

### Adding a New Field

1. **Google Sheets:** Add a new column header to the relevant tab
2. **Config file:** If it's a category, add to `categories.json`
3. **If collected via onboarding:** Add the question to the onboarding agent prompt
4. **If auto-enriched:** Add the scraping logic to the enrichment agent

### Removing a Field

1. **Google Sheets:** Remove the column (data is lost) or hide it (data preserved)
2. **Config file:** Remove the entry from `categories.json`
3. Agents skip unknown/empty fields automatically

### Adding a New Category

1. Add the new key to `influencer_categories` or `brand_categories` in `categories.json`
2. Define `name`, `type`, `values`, and optional `subcategories`
3. Set `allow_custom: true` if users should be able to add ad-hoc values

### Adding a Subcategory

1. Find the parent category in `categories.json`
2. Add the new value to the `subcategories` array
3. No code changes needed — agents read config dynamically

---

## 6. Google Sheets Tab Structure (Phase 1)

| Tab Name | Purpose | Key Columns |
|----------|---------|-------------|
| **Influencer Pipeline** | All influencer data + pipeline status | handle, name, phone, city, follower_count, engagement_rate, niche, tier, quality_score, pipeline_status, points, reputation_tier |
| **Brand Pipeline** | All brand data + outreach status | restaurant_name, phone, locality, cuisine, pipeline_status, lead_score, assigned_sales, last_contacted |
| **Job Board** | Active job postings | job_id, brand_name, niche, tier, deal_type, budget, points, deliverables, status, expires |
| **Campaign Tracker** | Active campaigns | campaign_id, brand, influencer, deal_type, amount, commission, status, visit_date, content_links |
| **Account Manager** | IG accounts + proxy health | account, persona, proxy_ip, status, dms_today, session_status, warmup_stage |
| **Dashboard** | Aggregated KPIs | Auto-calculated from other tabs |
| **Config** | Categories, subcategories, settings | All configurable values live here |
