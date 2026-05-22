# Validation Checklist Template

Use this template to review and validate the agent's findings before final itinerary generation.

## How to Use

After Phase 1 (Raw Data Gathering), the agent will present findings in tables. Copy this checklist, fill in your responses, and return it to the agent.

---

## Attraction Validation

| # | Attraction Name | Keep? | Priority | Notes/Adjustments |
|---|-----------------|-------|----------|-------------------|
| 1 |                 | Y/N   | High/Med/Low |                  |
| 2 |                 | Y/N   | High/Med/Low |                  |
| 3 |                 | Y/N   | High/Med/Low |                  |

**Additions** (attractions you want included that weren't found):
- [ ] 
- [ ] 

**Removals** (attractions to exclude):
- [ ] 
- [ ] 

---

## Hotel/Area Validation

| # | Hotel/Area Name | Keep? | Priority | Notes/Adjustments |
|---|-----------------|-------|----------|-------------------|
| 1 |                 | Y/N   | High/Med/Low |                  |
| 2 |                 | Y/N   | High/Med/Low |                  |
| 3 |                 | Y/N   | High/Med/Low |                  |

**Preferences**:
- Preferred neighborhood(s): 
- Must-have amenities: 
- Deal-breakers: 

---

## Restaurant Validation

| # | Restaurant Name | Keep? | Priority | Notes/Adjustments |
|---|-----------------|-------|----------|-------------------|
| 1 |                 | Y/N   | High/Med/Low |                  |
| 2 |                 | Y/N   | High/Med/Low |                  |
| 3 |                 | Y/N   | High/Med/Low |                  |

**Dietary requirements to emphasize**:
- 

**Cuisines to prioritize**:
- 

**Cuisines to avoid**:
- 

---

## Bias Injection

Use this section to add personal preferences that might not be captured in the initial preferences:

**Neighborhood preferences**:
- Prefer: 
- Avoid: 

**Time preferences**:
- Earliest start time: 
- Latest end time: 
- Preferred meal times: 

**Activity preferences**:
- Prefer indoor/outdoor: 
- Prefer guided/self-guided: 
- Physical activity level: 

**Cultural preferences**:
- Interest in local experiences: 
- Interest in tourist attractions: 
- Photography interests: 

---

## Weight Adjustments

If you want to change the optimization priorities:

| Dimension | Default Weight | Your Weight (0-1) |
|-----------|----------------|-------------------|
| Interest match | 0.25 | |
| Social proof | 0.20 | |
| Cost efficiency | 0.20 | |
| Location convenience | 0.15 | |
| Time efficiency | 0.10 | |
| Authenticity | 0.10 | |

*Weights should sum to 1.0*

---

## Confidence Threshold

Set your minimum confidence level for recommendations:
- [ ] HIGH only (multiple recent sources agree)
- [ ] HIGH or MEDIUM (some uncertainty acceptable)
- [ ] Include LOW if no alternatives (will verify independently)

---

Reply with your completed checklist or simply say "proceed" to accept all findings as-is.
