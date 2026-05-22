# Travel Plan Preference Template

Copy this template and fill in your preferences before starting a travel planning session.

```yaml
# Required fields
destination: "Tokyo, Japan"
dates:
  arrival: "2025-04-01"
  departure: "2025-04-07"

# Optional fields (defaults shown)
travelers:
  count: 2
  composition: "couple"  # solo|couple|family|friends

budget:
  total: 3000  # in USD, optional
  daily_limit: 500  # alternative to total
  breakdown:  # weights 0-1, must sum to 1.0
    accommodation: 0.30
    food: 0.25
    transport: 0.15
    activities: 0.30

preferences:
  pace: "moderate"  # relaxed (3/day)|moderate (5/day)|packed (7/day)
  interests:
    - history
    - food
    - culture
  must_see:
    - "Senso-ji Temple"
  avoid:
    - "tourist traps"
    - "crowded places"
  dietary:
    - "vegetarian options preferred"
  accommodation_style: "mid-range"  # budget|mid-range|luxury|boutique|hostel|apartment
  transport_preference: "mixed"  # public|walk|rideshare|rental|mixed

constraints:
  mobility: "none"  # none|limited|wheelchair
  language: "english"  # english|local|mixed
  safety_priority: "medium"  # low|medium|high

# Optional: Your own research to inject
my_research:
  attractions:
    - name: "TeamLab Planets"
      reason: "Friend recommended, want to visit"
  hotels:
    - name: "Any hotel in Shinjuku area"
      reason: "Want to stay near train station"
  restaurants:
    - name: "Ichiran Ramen"
      reason: "Seen on YouTube, want to try"
```

## Quick Start Examples

### Example 1: Budget Solo Trip
```
Plan a 5-day budget trip to Bangkok for solo traveler, $1500 total, interested in street food and temples, relaxed pace, avoid tourist areas.
```

### Example 2: Family Vacation
```
Plan a 7-day family trip to Orlando with 2 adults and 2 kids (ages 8 and 12), $5000 budget, must include theme parks, moderate pace, need family-friendly hotels with pool.
```

### Example 3: Luxury Couple Getaway
```
Plan a 4-day luxury trip to Paris for couple, unlimited budget, interested in fine dining and art galleries, moderate pace, want boutique hotel in Le Marais.
```
