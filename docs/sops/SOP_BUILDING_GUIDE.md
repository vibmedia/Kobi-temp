# 📋 SOP Building Guide — How to Create, Update & Manage SOPs

> This is the **meta-SOP** — a guide for writing new Standard Operating Procedures whenever VibMedia adds features, processes, expands into new areas, or updates existing workflows.

---

## 1. When Do You Need a New SOP?

| Trigger | Example | Action |
|---------|---------|--------|
| **New feature added** | Job Board launched | Write SOP for job creation, matching, review |
| **New channel added** | Email outreach | Write SOP for email drip, reply handling |
| **Process change** | Manual → AI automation | Update existing SOP, mark old as deprecated |
| **New team member** | Sales hire | Ensure all relevant SOPs are shared + understood |
| **Repeated mistake** | Influencer paid twice | Write SOP for payout verification |
| **New geography** | Expanding to Mumbai | Write SOP for area-specific data sourcing, matching |
| **New industry** | Fashion brands | Write SOP for fashion-specific onboarding, QC standards |
| **Customer complaint** | Content not matching brief | Write/improve SOP for QC review checklist |
| **Compliance requirement** | DPDPA audit | Write SOP for data handling and opt-out |
| **Scaling threshold** | >50 campaigns/month | Write SOP for batch operations, delegation |

---

## 2. SOP Template

Every SOP **MUST** follow this format:

```markdown
# SOP-[XXX]: [Title]

| Field | Value |
|-------|-------|
| **SOP ID** | SOP-XXX |
| **Version** | 1.0 |
| **Domain** | [Influencer / Brand / Campaign / Operations / Finance / Compliance / Growth] |
| **Owner** | [Role responsible, e.g., Ops Manager] |
| **Executor** | [Who performs: AI Agent / Ops Team / Sales / Both] |
| **Frequency** | [Per event / Daily / Weekly / Monthly / As needed] |
| **Estimated Time** | [X minutes per execution] |
| **Prerequisites** | [What must be true before starting] |
| **Related SOPs** | [SOP-XXX, SOP-XXX] |
| **Last Updated** | [Date] |

## Purpose
[1-2 sentences explaining WHY this SOP exists]

## Scope
[What this SOP covers and what it does NOT cover]

## Steps

### Step 1: [Action Title]
**Who:** [Executor]
**Tool:** [Tool/system used]
**Action:**
1. Do X
2. Do Y
3. Do Z

**✅ Done when:** [Clear completion criteria]

### Step 2: [Action Title]
...

## Decision Points

| Condition | Action |
|-----------|--------|
| If X happens | Do A |
| If Y happens | Do B |
| If unsure | Escalate to [role] |

## Failure Recovery
- **If [tool] fails:** [Alternative steps]
- **If [person] unavailable:** [Backup person/process]

## Quality Checklist
- [ ] [Item 1]
- [ ] [Item 2]
- [ ] [Item 3]

## Change Log
| Version | Date | Change | Author |
|---------|------|--------|--------|
| 1.0 | YYYY-MM-DD | Initial version | [Name] |
```

---

## 3. SOP Numbering System

| Range | Domain | Example |
|-------|--------|---------|
| SOP-001 to SOP-099 | **Influencer Pipeline** | SOP-001: Data Import |
| SOP-100 to SOP-199 | **Brand Pipeline** | SOP-101: WhatsApp First Touch |
| SOP-200 to SOP-299 | **Job Board & Campaigns** | SOP-201: Job Creation |
| SOP-300 to SOP-399 | **Operations & Infrastructure** | SOP-301: Daily Routine |
| SOP-400 to SOP-499 | **Finance & Payments** | SOP-401: Invoice Generation |
| SOP-500 to SOP-599 | **QC & Compliance** | SOP-501: Content QC |
| SOP-600 to SOP-699 | **Growth & Expansion** | SOP-601: New Area Launch |

> **To add a new SOP:** Use the next available number in the relevant domain range.

---

## 4. SOP Lifecycle

```
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  DRAFT   │───▶│  REVIEW  │───▶│  ACTIVE  │───▶│ DEPRECATED│
│          │    │          │    │          │    │          │
│ Author   │    │ Team lead│    │ Published│    │ Replaced │
│ writes   │    │ reviews  │    │ + trained│    │ by newer │
└──────────┘    └──────────┘    └──────────┘    └──────────┘
                     │                │
                     │                ▼
                     │          ┌──────────┐
                     └──────────│  UPDATED │
                               │ New vers │
                               └──────────┘
```

### Review Triggers
- **Quarterly review:** All active SOPs reviewed every 3 months
- **Incident-triggered:** SOP updated after any failure or near-miss
- **Feature change:** SOP updated when underlying tools or processes change
- **Team feedback:** SOP updated when executor reports confusion or inefficiency

---

## 5. How to Write a Good SOP

### ✅ DO
- Use **specific, numbered steps** (not vague paragraphs)
- Include **screenshots or tool references** for every step
- Define **clear completion criteria** ("✅ Done when")
- Add **decision trees** for branching logic
- Include **failure recovery** steps
- Write for the **dumbest possible executor** — assume zero context
- Include **time estimates** per step

### ❌ DON'T
- Don't write walls of text — use tables and lists
- Don't assume the reader knows the tools
- Don't skip edge cases — they're where mistakes happen
- Don't combine two unrelated processes in one SOP
- Don't forget to update when the process changes

---

## 6. SOP Index Template

Maintain a master index in `docs/sops/INDEX.md`:

```markdown
| SOP ID | Title | Domain | Status | Last Updated |
|--------|-------|--------|--------|-------------|
| SOP-001 | Influencer Data Import | Influencer | ✅ Active | 2026-03-28 |
| SOP-002 | Profile Enrichment | Influencer | ✅ Active | 2026-03-28 |
| ... | ... | ... | ... | ... |
```

---

## 7. Expansion Playbook — SOPs Needed When Scaling

### Adding a New City
| New SOPs Needed | Based On |
|----------------|----------|
| `SOP-6XX: [City] Data Sourcing` | Clone SOP-001, update area/sources |
| `SOP-6XX: [City] Brand Outreach` | Clone SOP-101, update templates |
| `SOP-6XX: [City] Proxy Setup` | Clone SOP-305, update IP region |

### Adding a New Industry (e.g., Fashion)
| New SOPs Needed | Based On |
|----------------|----------|
| `SOP-6XX: [Industry] Influencer Criteria` | New — define niche categories, minimum metrics |
| `SOP-6XX: [Industry] Content QC Standards` | Clone SOP-501, update quality criteria |
| `SOP-6XX: [Industry] Brand Outreach Templates` | Clone SOP-101, update messaging |

### Adding a New Feature
| New SOPs Needed | Based On |
|----------------|----------|
| `SOP-XXX: Feature Usage` | New — define how ops team uses the feature |
| `SOP-XXX: Feature QA` | New — define testing/validation steps |
| Update existing related SOPs | Identify via `Related SOPs` field |

### Onboarding a New Team Member
1. Share SOP Index
2. Assign domain-specific SOPs based on role
3. Shadow existing executor for 1 week
4. Execute under supervision for 1 week
5. Independent execution with spot-checks
