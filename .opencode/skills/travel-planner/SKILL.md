---
name: travel-planner
description: Use ONLY when the user asks for travel planning, itinerary creation, trip optimization, or vacation planning with specific destinations. Generates cost-time optimized travel plans with validated attraction hours, hotel details, traffic conditions, and social media cross-referenced reviews. Triggers on keywords: travel plan, itinerary, trip planner, vacation plan, destination guide.
---

# Travel Planner Agent Skill

A transparent, LLM Wiki-style travel planning system that optimizes for cost and time while validating all recommendations against real-world social proof.

## Core Philosophy

This skill follows the **LLM Wiki pattern**: every claim is traceable to raw sources, the user can inject bias at validation checkpoints, and the LLM handles optimization with full transparency into its reasoning.

### LLM Wiki Structure

All travel plans follow a 5-section wiki structure defined in `reference/llm-wiki-structure.md`:

```
TRAVEL_WIKI/
+-- 00_user_preferences.md          # Structured input from user
+-- 01_raw_data/                    # Unfiltered research findings
|   +-- attractions.md
|   +-- accommodation.md
|   +-- food.md
|   +-- transport.md
|   +-- social_validation.md
+-- 02_user_bias.md                 # User corrections and preferences
+-- 03_validation/                  # Cross-referenced claims
|   +-- claims_verified.md
|   +-- claims_rejected.md
|   +-- confidence_scores.md
+-- 04_reasoning/                   # Decision-making trace
|   +-- scoring_breakdown.md
|   +-- rejected_options.md
|   +-- optimization_trace.md
+-- 05_final_output/                # Polished itinerary
    +-- itinerary.md
```

### Workflow

The step-by-step workflow is defined in `reference/workflow-template.md`:

```
[00] Parse preferences -> Present back for confirmation
    |
[01] Raw data gathering (parallel websearch) -> WAIT FOR USER
    |
[02] User bias injection (corrections, additions, removals)
    |
[03] Validation (cross-reference, social media check) -> WAIT FOR USER
    |
[04] Reasoning (scoring, clustering, budget allocation) -> WAIT FOR USER
    |
[05] Final output (polished itinerary) -> ITERATION LOOP
```

**Key principle**: Present raw findings, wait for user bias injection, validate, reason, then output. Never skip validation checkpoints.

---

## Phase 0: Preference Collection

Before any research begins, collect structured preferences. If the user has not provided these, ask for them in a single batch:

```yaml
destination: "city, country"
dates:
  arrival: "YYYY-MM-DD"
  departure: "YYYY-MM-DD"
travelers:
  count: 1
  composition: "solo|couple|family|friends"
budget:
  total: 0  # optional, in USD or local currency
  daily_limit: 0  # optional
  breakdown:  # optional weights 0-1, default equal
    accommodation: 0.3
    food: 0.25
    transport: 0.15
    activities: 0.3
preferences:
  pace: "relaxed|moderate|packed"  # affects daily activity count
  interests: ["history", "food", "nature", "nightlife", "shopping", "art", "adventure"]
  must_see: ["attraction names if any"]
  avoid: ["crowds", "tourist traps", "walking heavy", etc]
  dietary: []  # restrictions
  accommodation_style: "budget|mid-range|luxury|boutique|hostel|apartment"
  transport_preference: "public|walk|rideshare|rental|mixed"
constraints:
  mobility: "none|limited|wheelchair"
  language: "english|local|mixed"
  safety_priority: "low|medium|high"
```

**Output**: Present the parsed preferences back to the user and ask: *"Any adjustments before I begin research?"*

---

## Phase 1: Raw Data Gathering

### Tool Calling Strategy (Cost-Optimized)

**Priority order for data sources** (cheapest/most reliable first):

1. **websearch** (free, no CAPTCHA) - Use for all initial discovery
2. **webfetch** (free, but may fail on protected sites) - Use only on known scraper-friendly URLs
3. **Direct knowledge** (free, but may be stale) - Use for well-known facts, always flag as "unverified"

**NEVER use** direct scraping of:
- Google Maps (CAPTCHA)
- TripAdvisor (anti-bot)
- Booking.com (anti-bot)
- Any site with Cloudflare protection

**Safe webfetch targets** (typically allow fetching):
- Official tourism board websites (`*.tourism.*`, `visit*.com`)
- Wikipedia pages for attraction details
- Official attraction websites (`.gov`, `.org` domains)
- OpenStreetMap for geographic data
- Government transit websites

### 1A. Attraction Research

For each interest category, run **parallel websearch** queries:

```
"best [interest] in [destination] 2025 2026"
"[destination] top attractions opening hours current"
"[destination] hidden gems locals recommend"
"[destination] tourist traps to avoid"
```

For each attraction found, gather:
- Name, category, approximate location
- Opening hours (search specifically for current year)
- Entry fee (free/paid range)
- Recommended visit duration
- Peak vs off-peak times

### 1B. Hotel/Accommodation Research

Run **parallel websearch** queries:

```
"best areas to stay in [destination] for [traveler type]"
"[destination] hotels [budget range] 2025 2026 reviews"
"[destination] accommodation neighborhoods safety"
"[destination] hotel prices [month] [year]"
```

For each area/hotel found, gather:
- Neighborhood name and characteristics
- Price range per night
- Proximity to attractions (walking/public transit)
- Safety reputation
- Notable amenities

### 1C. Restaurant Research

Run **parallel websearch** queries:

```
"best restaurants [destination] locals recommend 2025 2026"
"[destination] food guide must try dishes"
"[destination] restaurants near [attraction/area]"
"[destination] street food safe areas"
```

For each restaurant found, gather:
- Name, cuisine type, price range
- Location/neighborhood
- Signature dishes
- Reservation requirements

### 1D. Transport & Traffic Research

Run **parallel websearch** queries:

```
"[destination] public transport guide 2025 2026"
"[destination] airport to city center transport options cost"
"[destination] traffic conditions [month] [year]"
"[destination] best transport app for tourists"
"[destination] walkability score neighborhoods"
```

Gather:
- Transport modes available and costs
- Average transit times between key areas
- Traffic patterns by time of day
- Recommended transport apps

### 1E. Social Media Validation (Critical Step)

For EVERY top candidate (attractions, hotels, restaurants), run validation searches:

```
site:reddit.com "[attraction/hotel/restaurant name] review experience"
site:reddit.com "[destination] travel tips 2025 2026"
site:reddit.com "[destination] what I wish I knew before visiting"
"[attraction/hotel name] real review not sponsored"
"[destination] overrated attractions"
"[destination] underrated places locals go"
```

**Extract from social media**:
- Recent visitor experiences (last 6 months preferred)
- Complaints or warnings (safety, scams, closures)
- Local resident recommendations vs tourist opinions
- Seasonal considerations (weather, crowds, closures)
- Price accuracy verification

### Phase 1 Output Format

Present findings as **RAW SOURCE TABLES** with citations:

```markdown
## Attractions Found (Raw)

| # | Name | Source | Opening Hours | Fee | Duration | Social Validation | Confidence |
|---|------|--------|---------------|-----|----------|-------------------|------------|
| 1 | ...  | [link] | ...           | ... | ...      | [reddit link]     | HIGH/MED/LOW |

## Hotels Found (Raw)

| # | Name/Area | Source | Price/Night | Location | Social Validation | Confidence |
|---|-----------|--------|-------------|----------|-------------------|------------|

## Restaurants Found (Raw)

| # | Name | Source | Cuisine | Price | Social Validation | Confidence |
|---|------|--------|---------|-------|-------------------|------------|

## Transport Notes (Raw)

| Route | Mode | Time | Cost | Source | Notes |
|-------|------|------|------|--------|-------|
```

**Then ask the user**: *"Review these raw findings. Add any biases, remove anything you dislike, or add must-include items. Reply with your adjustments or 'proceed' to continue."*

---

## Phase 2: User Validation & Bias Injection

Wait for user input. The user may:
- Remove items they don't want
- Add items from their own research
- Adjust priorities ("I care more about food than museums")
- Add constraints ("no walking more than 2km per day")
- Inject personal bias ("I prefer local neighborhoods over tourist areas")

Apply all user adjustments to create the **VALIDATED CANDIDATE SET**.

---

## Phase 3: Optimization Algorithm

### Multi-Objective Scoring Function

Each candidate (attraction, hotel, restaurant) receives a composite score:

```
Score(w, c) = Σ [w_i * normalize(metric_i(c))] - penalty(c)

Where:
  w = weight vector from user preferences
  c = candidate item
  metric_i = individual scoring dimension
  penalty = constraint violations
```

### Attraction Scoring

```
AttractionScore = 
  w_interest * interest_match(c) +           # 0-1: how well it matches stated interests
  w_social * social_proof(c) +               # 0-1: normalized from Reddit/review sentiment
  w_cost * cost_efficiency(c) +              # 0-1: value for money (quality/price)
  w_time * time_efficiency(c) +              # 0-1: experience quality per hour spent
  w_location * proximity_score(c) +          # 0-1: closeness to other planned items
  - penalty_closed(c) * 10 -                  # hard penalty if closed on visit day
  - penalty_crowded(c) * 2 -                  # penalty if peak crowd time
  - penalty_mismatch(c) * 3                   # penalty if conflicts with avoid list
```

### Hotel Scoring

```
HotelScore =
  w_budget * budget_fit(c) +                 # 0-1: how well price fits budget
  w_social * social_proof(c) +               # 0-1: from review validation
  w_location * location_score(c) +           # 0-1: proximity to attractions + safety
  w_amenity * amenity_match(c) +             # 0-1: matches desired amenities
  w_transport * transport_access(c) +        # 0-1: public transport accessibility
  - penalty_overbudget(c) * 5 -              # hard penalty if exceeds budget
  - penalty_unsafe(c) * 10                   # hard penalty for unsafe areas
```

### Restaurant Scoring

```
RestaurantScore =
  w_food * cuisine_match(c) +                # 0-1: matches dietary/cuisine preferences
  w_social * social_proof(c) +               # 0-1: from review validation
  w_cost * cost_fit(c) +                     # 0-1: fits food budget allocation
  w_location * proximity_score(c) +          # 0-1: near planned activities
  w_authentic * authenticity(c) +            # 0-1: local vs tourist trap indicator
  - penalty_closed(c) * 10 -                 # hard penalty if closed
  - penalty_tourist_trap(c) * 3              # penalty if flagged as tourist trap
```

### Geographic Clustering Algorithm

To minimize travel time, group activities by proximity:

```
1. Map all candidates to coordinates (from search results)
2. Apply greedy nearest-neighbor clustering:
   a. Start from hotel location
   b. Find nearest unvisited candidate
   c. Add to current cluster if within threshold distance
   d. If threshold exceeded, start new cluster
3. Optimize cluster order using 2-opt swap:
   For each pair of consecutive items (i, i+1) and (j, j+1):
     If dist(i,j) + dist(i+1,j+1) < dist(i,i+1) + dist(j,j+1):
       Reverse segment between i+1 and j
4. Assign clusters to time blocks (morning/afternoon/evening)
```

### Daily Schedule Optimization

```
For each day d in trip:
  available_hours = wake_time to sleep_time - meal_breaks
  pace_limit = {relaxed: 3, moderate: 5, packed: 7}  # max activities
  
  Select activities for day d:
    1. Must-see items get priority slots
    2. Fill remaining slots by highest AttractionScore
    3. Respect opening hours constraints
    4. Minimize travel time between consecutive items (use clustered order)
    5. Insert meals at restaurants near activity locations
    6. Ensure total walking/transit time < threshold per day
    
  Optimize timing:
    For each activity a:
      If a has peak/off-peak times:
        Schedule for off-peak when possible
      If a has limited hours:
        Schedule within operating window
      If a is outdoor:
        Check weather patterns for optimal time
```

### Budget Allocation Algorithm

```
total_budget = user.total or estimated from daily_limit * days

Allocate by weights:
  accommodation_budget = total_budget * w_accommodation
  food_budget = total_budget * w_food
  transport_budget = total_budget * w_transport
  activities_budget = total_budget * w_activities

For each category:
  Select items greedily by Score/Budget ratio until budget exhausted
  If budget exceeded:
    1. Replace most expensive item with next best cheaper alternative
    2. If still over, reduce item count and increase per-item quality

Output budget breakdown with actual vs allocated comparison
```

### Phase 3 Output

Present the **OPTIMIZATION REASONING**:

```markdown
## Optimization Summary

### Scoring Weights Applied
- Interest match: X%
- Social proof: X%
- Cost efficiency: X%
- ...

### Items Rejected and Why
- [Item]: rejected because [reason with score breakdown]

### Geographic Clusters
- Cluster 1 (Area X): [items] - avg travel time: X min
- Cluster 2 (Area Y): [items] - avg travel time: X min

### Budget Allocation
| Category | Allocated | Used | Remaining |
|----------|-----------|------|-----------|
```

**Then ask**: *"Review the optimization reasoning. Any adjustments to weights or constraints before I generate the final itinerary?"*

---

## Phase 4: Final Itinerary Generation

Generate the day-by-day itinerary with **detailed transport timing, queue estimates, and luggage handling notes**:

```markdown
# Travel Itinerary: [Destination]
**Dates**: [arrival] to [departure] | **Travelers**: [count] [composition]
**Total Budget**: [amount] | **Estimated Spend**: [amount]
**Confidence Level**: HIGH/MEDIUM/LOW

---

## Day 1: [Date] - [Theme, e.g., "Historic Center"]

**Theme**: [brief description]
**Weather Note**: [relevant weather/holiday considerations]

| Time | Activity | Details | Source | Confidence |
|------|----------|---------|--------|------------|
| HH:MM | **Activity Name** | Description with **start/end times**, **walking times with luggage**, **queue times**, **transport frequency**. | Source name | HIGH/MED/LOW |

### Transport Verification - Day X
| Transport | Scheduled Time | Available? | Wait Time | Notes |
|-----------|---------------|------------|-----------|-------|
| [Mode] | HH:MM | ✅/❌ | X min | [luggage/accessibility notes] |

### Day X Budget
| Category | Item | Cost (USD) |
|----------|------|------------|
| Transport | ... | $X |
| Food | ... | $X |
| Activities | ... | $X |
| **Day Total** | | **$X** |

---

[Repeat for each day]

---

## Accommodation Recommendation

### [Area] Hotels
| Hotel | Price/Night | Rating | Why | Source |
|-------|-------------|--------|-----|--------|

---

## Practical Information

### Transport
- Airport transfer: [options with cost/time]
- Daily transport: [recommended mode + pass options]
- Transport apps to download: [list]
- **Luggage handling**: [tips for navigating with bags]

### Queue Time Summary
| Attraction | Best Time | Queue Time | Visit Duration |
|------------|-----------|------------|----------------|

### Money-Saving Tips
- [Specific tips from research]

### Warnings from Recent Visitors
- [Scams, closures, seasonal issues from social media]

### Dress Code
- [Temple/venue requirements]

### Emergency Contacts
- Local emergency number
- Embassy info if applicable

---

## Source Transparency

All recommendations validated against:
- [ ] Official websites (opening hours, prices)
- [ ] Reddit discussions (real experiences)
- [ ] Local blogs/guides (insider knowledge)
- [ ] Multiple independent sources per item

Confidence level: HIGH/MEDIUM/LOW based on source recency and agreement
```

### Required Details Per Activity

Every activity entry must include:
- **Start time** and **end time** (explicit)
- **Transport mode** with frequency (e.g., "Every 5-8 min")
- **Walking time** (separate values: without luggage / with luggage)
- **Queue time** (expected wait at attraction/restaurant)
- **Accessibility notes** (elevators, stairs, covered walkways)
- **Weather contingency** (indoor backup if outdoor)

### Transport Verification Table

After each day's itinerary, include a verification table:
- Check if scheduled transport is available at that time
- Include wait time estimates based on frequency
- Note luggage accessibility (elevators, ramps, storage)
- Flag any service gaps (e.g., "BTS stops at midnight")

### Queue Time Summary

At the end of the itinerary, provide a consolidated queue table:
- Best arrival time to minimize queues
- Expected wait time at that time
- Alternative times and their queue penalties

---

## Phase 5: Iteration Loop

After presenting the itinerary:

1. Ask: *"Would you like to adjust anything? You can:"*
   - Swap specific items
   - Change pacing
   - Adjust budget allocation
   - Request alternatives for any category
   - Add/remove days

2. On any adjustment:
   - Re-run only the affected optimization (not full research)
   - Show what changed and why
   - Present updated itinerary

3. Loop until user confirms: *"Looks good, finalize it."*

---

## Tool Calling Decision Tree

```
User request received
    |
    v
Is destination known? ──No──> Ask for destination + dates
    |
   Yes
    |
    v
Use websearch for discovery (parallel queries)
    |
    v
Results found? ──No──> Broaden search terms, try alternative queries
    |
   Yes
    |
    v
Can I verify on official site? ──Yes──> webfetch official URL
    |
   No (protected site)
    |
    v
Search for cached/mirror info via websearch
    |
    v
Cross-validate with social media (site:reddit.com queries)
    |
    v
Confidence HIGH? ──No──> Flag as LOW confidence, seek alternatives
    |
   Yes
    |
    v
Include in candidate set with source citations
```

---

## Cost Management Rules

1. **Always prefer websearch over webfetch** - websearch is more reliable and avoids CAPTCHAs
2. **Never scrape booking sites directly** - use search results that aggregate pricing
3. **Use official .gov/.org sites for webfetch** - these rarely block automated access
4. **Cache results mentally** - if searching for similar info, reuse prior results
5. **Flag uncertain data** - if a price or hour is from >6 months ago, mark as "verify before visit"
6. **Provide ranges not exact figures** - "approximately $15-20" not "$17.50"

---

## Social Media Validation Checklist

For each top-3 recommendation in every category, verify:

- [ ] At least one Reddit thread mentions it positively
- [ ] No recent warnings/complaints in last 3 months
- [ ] Local resident endorsement (not just tourist reviews)
- [ ] Price mentioned aligns with official sources
- [ ] Opening hours confirmed by multiple sources
- [ ] No reports of permanent closure or major renovation

If any item fails validation:
1. Demote it in scoring
2. Find alternative
3. If no alternative exists, flag prominently to user

---

## Export Formats

When the user requests exports, generate the following:

### Printable Diagram (Mermaid + HTML)

Create a self-contained HTML file with:
- Mermaid.js timeline diagram showing day-by-day schedule
- Transport connections between activities
- Color-coded activity types (food=green, transport=blue, activity=orange, hotel=purple)
- Print-friendly CSS (A4 landscape)
- QR code linking to full itinerary (optional)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Travel Itinerary - [Destination]</title>
  <script src="https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"></script>
  <style>
    @media print {
      body { margin: 0; }
      .no-print { display: none; }
    }
  </style>
</head>
<body>
  <div class="mermaid">
    gantt
      title [Destination] Itinerary
      dateFormat HH:mm
      axisFormat %H:%M
      
      section Day 1
      Activity 1 :a1, 14:00, 30min
      Transport  :after a1, 15min
      Activity 2 :after Transport, 2h
  </div>
</body>
</html>
```

### Slide Deck (HTML + Reveal.js)

Create a self-contained HTML presentation with:
- Cover slide: Destination, dates, travelers
- Day overview slides: Theme, key activities, budget
- Transport summary slide: Modes, costs, tips
- Packing checklist slide
- Emergency contacts slide
- Print-friendly (export to PDF via browser)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Travel Itinerary - [Destination]</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@4/dist/reveal.css">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/reveal.js@4/dist/theme/white.css">
</head>
<body>
  <div class="reveal">
    <div class="slides">
      <section>
        <h1>[Destination]</h1>
        <p>[Dates] | [Travelers]</p>
      </section>
      <section>
        <h2>Day 1: [Theme]</h2>
        <ul>
          <li>Activity 1</li>
          <li>Activity 2</li>
        </ul>
        <p>Budget: $X</p>
      </section>
    </div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/reveal.js@4/dist/reveal.js"></script>
  <script>Reveal.initialize();</script>
</body>
</html>
```

---

## Edge Cases

### When websearch returns no recent results:
- Flag as "limited data available"
- Use older information with explicit staleness warning
- Suggest user verify independently before booking

### When social media consensus is negative:
- Do NOT recommend the item regardless of official ratings
- Explain why in the rejection reasoning
- Offer alternatives

### When budget is very tight:
- Prioritize free attractions
- Suggest food markets over restaurants
- Recommend walking over paid transport
- Highlight discount passes and free days

### When destination is obscure:
- Expand search to regional/nearby areas
- Use broader travel forums beyond Reddit
- Acknowledge data limitations prominently

### When dates are flexible:
- Research seasonal price variations
- Identify shoulder season advantages
- Flag major events that affect pricing/availability

---

## Output Quality Standards

Every recommendation must answer:
1. **What** - name, type, location
2. **When** - hours, best time to visit, how long to spend
3. **How much** - cost with source
4. **Why** - matches user preferences + social proof
5. **Source** - link to where info was found
6. **Confidence** - HIGH/MEDIUM/LOW based on source quality and recency

Never present a recommendation without at least 2 independent sources.
