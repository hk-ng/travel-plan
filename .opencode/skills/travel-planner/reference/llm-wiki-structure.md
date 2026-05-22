# LLM Wiki: Travel Planning Structure

This document defines the **LLM Wiki pattern** for travel planning. It separates raw data, user bias, validation, reasoning, and final output so users can trace every recommendation to its source.

## Core Principle

The LLM Wiki pattern separates:
- **Raw Data** (what was found, unfiltered)
- **User Bias** (what the user wants to inject)
- **Validation** (how claims were verified)
- **Reasoning** (how decisions were made)
- **Output** (the final result)

This allows users to see raw source material, add their own knowledge, trace recommendations to sources, and understand why options were rejected.

## Directory Structure

`
TRAVEL_WIKI/
+-- 00_user_preferences.md
+-- 01_raw_data/
|   +-- attractions.md
|   +-- accommodation.md
|   +-- food.md
|   +-- transport.md
|   +-- social_validation.md
+-- 02_user_bias.md
+-- 03_validation/
|   +-- claims_verified.md
|   +-- claims_rejected.md
|   +-- confidence_scores.md
+-- 04_reasoning/
|   +-- scoring_breakdown.md
|   +-- rejected_options.md
|   +-- optimization_trace.md
+-- 05_final_output/
    +-- itinerary.md
`

## Section 00: User Preferences

Structured capture of user input before any research begins.

`markdown
# User Preferences

## Trip Basics
- **Destination**: [user input]
- **Dates**: [arrival] to [departure]
- **Travelers**: [count] [composition]
- **Budget**: [total or daily]

## Preference Weights (0-1, sum to 1.0)
| Dimension | Weight | Rationale |
|-----------|--------|-----------|
| Interest match | 0.25 | |
| Social proof | 0.20 | |
| Cost efficiency | 0.20 | |
| Location convenience | 0.15 | |
| Time efficiency | 0.10 | |
| Authenticity | 0.10 | |

## Constraints
- Must-see: [list]
- Avoid: [list]
- Dietary: [list]
- Mobility: [level]
- Safety priority: [low/medium/high]
`

## Section 01: Raw Data

Unfiltered findings from research, presented as tables with source citations.

**Rules**:
- Include ALL findings, even low-confidence ones
- Never filter or rank at this stage
- Always include source URLs
- Note when information is stale (>6 months old)
- Flag conflicting data for validation

`markdown
# Raw Data: [Category]

| # | Item | Detail 1 | Detail 2 | Detail 3 | Source(s) | Date Found | Confidence |
|---|------|----------|----------|----------|-----------|------------|------------|

## Conflicting Information
- [Claim A] says X, [Claim B] says Y
- Resolution needed in validation phase
`

## Section 02: User Bias

User injects their own knowledge, corrections, and preferences after reviewing raw data.

`markdown
# User Bias Injection

## Corrections to Raw Data
| # | Item | Correction | Reason |
|---|------|------------|--------|

## Additions (user own research)
| Item | Category | Source | Why Include |
|------|----------|--------|-------------|

## Removals
| Item | Reason |
|------|--------|

## Priority Adjustments
| Item | Original Priority | New Priority | Reason |
|------|-------------------|--------------|--------|

## Weight Adjustments
| Dimension | Original | New | Reason |
|-----------|----------|-----|--------|
`

## Section 03: Validation

Cross-reference all claims against multiple sources, flag uncertain data.

**Rules**:
- Every top-3 recommendation must have 2+ source verification
- Social media validation is mandatory (at least 1 Reddit/forum source)
- Flag any item with recent negative reports
- Distinguish tourist vs local opinions

`markdown
# Validation Report

## Claims Verified (2+ sources agree)
| Claim | Source 1 | Source 2 | Source 3 | Status |
|-------|----------|----------|----------|--------|

## Claims Rejected (contradicted by evidence)
| Claim | Contradicted By | Resolution |
|-------|-----------------|------------|

## Confidence Scores
| Item | Confidence | Reasoning |
|------|------------|-----------|
| | HIGH | 3+ recent sources agree |
| | MEDIUM | 2 sources agree, or 1 official + 1 social |
| | LOW | Single source, or conflicting data, or stale |

## Social Media Validation
| Topic | Sentiment | Key Quotes | Source Links |
|-------|-----------|------------|--------------|

## Red Flags
- [ ] Item X has recent scam reports
- [ ] Item Y permanently closed
- [ ] Item Z significantly overpriced per locals
`

## Section 04: Reasoning

Transparent trace of how decisions were made, including rejected options.

**Rules**:
- Show scores for ALL candidates, not just winners
- Explain every rejection
- Show geographic clustering logic
- Show budget trade-offs
- Never hide low-scoring options

`markdown
# Reasoning Trace

## Scoring Breakdown
| Item | Interest | Social | Cost | Location | Time | Auth | Penalties | Total |
|------|----------|--------|------|----------|------|------|-----------|-------|

## Rejected Options
| Item | Score | Rejection Reason |
|------|-------|------------------|

## Geographic Clustering
### Cluster 1: [Area Name]
- Items: [list]
- Avg travel time between items: X min

## Budget Allocation
| Category | Allocated | Used | Remaining | Notes |
|----------|-----------|------|-----------|-------|

## Optimization Decisions
- Decision: [what was chosen]
- Alternatives considered: [list]
- Why this won: [scoring breakdown]
- Trade-offs: [what was sacrificed]
`

## Section 05: Final Output

The polished itinerary, with every claim traced back to the wiki.

**Rules**:
- Every item links back to raw source
- Confidence level shown per item
- Budget shown per day
- Transport shown explicitly
- Source checklist completed

`markdown
# Travel Itinerary: [Destination]

## Quick Reference
- **Dates**: [arrival] to [departure]
- **Travelers**: [count] [composition]
- **Total Budget**: [amount]
- **Estimated Spend**: [amount]
- **Confidence Level**: HIGH/MEDIUM/LOW

## Day-by-Day

### Day 1: [Date] - [Theme]

| Time | Activity | Details | Source | Confidence |
|------|----------|---------|--------|------------|

## Source Transparency
All recommendations validated against:
- [ ] Official websites (opening hours, prices)
- [ ] Reddit discussions (real experiences)
- [ ] Local blogs/guides (insider knowledge)
- [ ] Multiple independent sources per item
`

## Workflow

`
USER INPUT
    |
    v
[00] Parse preferences -> Present back for confirmation
    |
    v
[01] Raw data gathering (parallel websearch)
    |
    v
Present raw findings -> WAIT FOR USER
    |
    v
[02] User bias injection (corrections, additions, removals)
    |
    v
[03] Validation (cross-reference, social media check)
    |
    v
Present validation report -> WAIT FOR USER
    |
    v
[04] Reasoning (scoring, clustering, budget allocation)
    |
    v
Present reasoning trace -> WAIT FOR USER
    |
    v
[05] Final output (polished itinerary)
    |
    v
Present itinerary -> ITERATION LOOP
    |
    v
User adjusts -> Re-run affected sections only
    |
    v
User confirms -> DONE
`

## Validation Checklist (Mandatory)

Before presenting final output, verify:

- [ ] Every top-3 attraction has 2+ independent sources
- [ ] Every top-3 hotel has 2+ independent sources
- [ ] Every top-3 restaurant has 2+ independent sources
- [ ] Opening hours verified against official site or recent (2025-2026) source
- [ ] Prices are ranges, not exact figures
- [ ] Social media validation completed for all recommendations
- [ ] No items with recent scam/warning reports included
- [ ] Geographic clustering minimizes travel time
- [ ] Budget allocation respects user constraints
- [ ] All user bias injections applied
- [ ] Rejected options documented with reasons
- [ ] Confidence levels assigned to every item
- [ ] Source URLs included for every claim

## Why This Structure Works

1. **Transparency**: User sees raw data before any LLM filtering
2. **Control**: User can inject bias at multiple checkpoints
3. **Traceability**: Every claim links back to a source
4. **Validation**: Social media cross-referencing prevents hallucination
5. **Reasoning**: Scoring breakdown shows why options were chosen/rejected
6. **Iteration**: Changes only re-run affected sections, not full research
7. **Confidence**: Every item has a confidence level based on source quality
