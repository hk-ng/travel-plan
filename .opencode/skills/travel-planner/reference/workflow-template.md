# LLM Wiki Workflow Template

This template defines the step-by-step workflow for the travel planner agent.

## Phase 0: Preference Collection

**Action**: Collect structured preferences from user.

**Output**: TRAVEL_WIKI/00_user_preferences.md

**Prompt to user**: *"Here are your parsed preferences. Any adjustments before I begin research?"*

**Wait for**: User confirmation or corrections.

---

## Phase 1: Raw Data Gathering

**Action**: Run parallel websearch queries for each category.

**Queries to run in parallel**:
- Attractions: "best [interest] in [destination] 2025 2026"
- Accommodation: "best areas to stay in [destination] for [traveler type]"
- Food: "best restaurants [destination] locals recommend 2025 2026"
- Transport: "[destination] public transport guide 2025 2026"
- Social validation: "site:reddit.com [destination] travel tips 2025 2026"

**Output**: TRAVEL_WIKI/01_raw_data/ (5 files)

**Present to user**: Raw findings tables with source citations.

**Prompt to user**: *"Review these raw findings. Add any biases, remove anything you dislike, or add must-include items. Reply with your adjustments or 'proceed' to continue."*

**Wait for**: User bias injection.

---

## Phase 2: User Bias Injection

**Action**: Apply all user adjustments to create validated candidate set.

**Output**: TRAVEL_WIKI/02_user_bias.md

**Rules**:
- Preserve user input verbatim
- Apply corrections to raw data
- Add/remove items as specified
- Adjust weights if requested

---

## Phase 3: Validation

**Action**: Cross-reference all claims against multiple sources.

**For each top-3 candidate**:
1. Verify opening hours against official site or recent source
2. Verify prices against 2+ sources
3. Check Reddit/forum for recent experiences
4. Flag any scam reports or warnings
5. Distinguish tourist vs local opinions

**Output**: TRAVEL_WIKI/03_validation/ (3 files)

**Present to user**: Validation report with confidence scores.

**Prompt to user**: *"Here is what I verified. Any concerns about the confidence levels or flagged items?"*

**Wait for**: User confirmation or concerns.

---

## Phase 4: Reasoning

**Action**: Run optimization algorithm with validated candidates.

**Steps**:
1. Score all candidates using weighted formula
2. Apply geographic clustering (nearest-neighbor + 2-opt)
3. Allocate budget by user weights
4. Generate day-by-day schedule respecting constraints
5. Document rejected options with reasons

**Output**: TRAVEL_WIKI/04_reasoning/ (3 files)

**Present to user**: Optimization reasoning with scoring breakdown.

**Prompt to user**: *"Review the optimization reasoning. Any adjustments to weights or constraints before I generate the final itinerary?"*

**Wait for**: User confirmation or weight adjustments.

---

## Phase 5: Final Output

**Action**: Generate polished itinerary from validated, optimized data.

**Output**: TRAVEL_WIKI/05_final_output/itinerary.md

**Rules**:
- Every item links back to raw source
- Confidence level shown per item
- Budget shown per day
- Transport shown explicitly
- Source checklist completed

**Present to user**: Final itinerary.

**Prompt to user**: *"Would you like to adjust anything? You can swap items, change pacing, adjust budget, or request alternatives."*

**Wait for**: User confirmation or iteration requests.

---

## Iteration Loop

**If user requests changes**:
1. Identify affected sections only
2. Re-run only those sections (not full research)
3. Show what changed and why
4. Present updated itinerary
5. Loop until user confirms: "Looks good, finalize it."

---

## Tool Calling Decision Tree

`
User request received
    |
    v
Is destination known? --No--> Ask for destination + dates
    |
   Yes
    |
    v
Use websearch for discovery (parallel queries)
    |
    v
Results found? --No--> Broaden search terms, try alternative queries
    |
   Yes
    |
    v
Can I verify on official site? --Yes--> webfetch official URL
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
Confidence HIGH? --No--> Flag as LOW confidence, seek alternatives
    |
   Yes
    |
    v
Include in candidate set with source citations
`

## Cost Management Rules

1. Always prefer websearch over webfetch
2. Never scrape booking sites directly
3. Use official .gov/.org sites for webfetch
4. Cache results mentally - reuse prior results
5. Flag uncertain data with staleness warning
6. Provide ranges not exact figures
