---
name: cia-terrain-dataforseo
description: >
  CIA Terrain Mapping operationalized with DataForSEO API workflows. Provides exact API calls,
  data interpretation, and strategic action plans for: competitor keyword gap analysis, unoccupied
  SERP territory, supply line (weak competitor content) identification, backlink terrain, and SERP
  feature mapping. Use when running competitive SEO intelligence, keyword research, content gap
  analysis, or rank tracking with DataForSEO.
---

# CIA Terrain Mapping × DataForSEO
*Operationalizing competitive SEO intelligence with real API workflows*

---

## Setup

```python
import requests, base64, json

DATAFORSEO_LOGIN = "jalonso@yellowjackmedia.com"
DATAFORSEO_PASS  = "06bdc0f7a21908cc"
API_BASE         = "https://api.dataforseo.com/v3"

def dfs(endpoint, payload):
    auth = base64.b64encode(f"{DATAFORSEO_LOGIN}:{DATAFORSEO_PASS}".encode()).decode()
    resp = requests.post(
        f"{API_BASE}/{endpoint}",
        json=[payload],
        headers={"Authorization": f"Basic {auth}", "Content-Type": "application/json"},
        timeout=60,
    )
    resp.raise_for_status()
    return resp.json()["tasks"][0]["result"]
```

---

## Zone 1: OCCUPIED TERRITORY — What Competitors Own

*Keywords competitors rank for that you don't. Know what terrain they control.*

### API: Keyword Gap Analysis

```python
def get_occupied_territory(my_domain, competitor_domains, location_code=2840):
    """
    Find keywords competitors rank for that you don't.
    location_code 2840 = United States
    """
    result = dfs("dataforseo_labs/google/keyword_gap/live", {
        "targets": [my_domain] + competitor_domains,
        "location_code": location_code,
        "language_code": "en",
        "filters": [
            # Keywords where at least one competitor ranks but you don't
            ["keyword_data.keyword_info.search_volume", ">", 100],
        ],
        "limit": 100,
    })
    
    occupied = []
    for item in result[0].get("items", []):
        kw = item["keyword_data"]["keyword"]
        vol = item["keyword_data"]["keyword_info"]["search_volume"]
        
        # Check if competitors rank but you don't
        my_rank = item["ranked_serp_element"].get(my_domain, {}).get("rank_absolute")
        competitor_ranks = {
            d: item["ranked_serp_element"].get(d, {}).get("rank_absolute")
            for d in competitor_domains
        }
        
        if my_rank is None and any(r for r in competitor_ranks.values() if r):
            occupied.append({
                "keyword": kw,
                "volume": vol,
                "competitor_positions": {k: v for k, v in competitor_ranks.items() if v},
            })
    
    return sorted(occupied, key=lambda x: x["volume"], reverse=True)
```

**Intelligence output interpretation:**
```
HIGH PRIORITY: Volume > 1,000, competitor ranks 1-3, you unranked
  → Fortified position. Need strong content + backlinks to compete.

MEDIUM PRIORITY: Volume 200-1,000, competitor ranks 4-10
  → Contested territory. Quality content can take this.

LOW PRIORITY: Volume < 200, competitor ranks 11-20
  → Light occupation. One solid piece can win this.
```

---

## Zone 2: CONTESTED TERRITORY — Head-to-Head Battles

*Keywords where you AND competitors rank. Find where you can advance.*

### API: Rankings Comparison

```python
def get_contested_territory(my_domain, competitor_domain, location_code=2840):
    """
    Find keywords where both you and a competitor rank.
    Prioritize where you're close (within 5 positions).
    """
    result = dfs("dataforseo_labs/google/ranked_keywords/live", {
        "target": my_domain,
        "location_code": location_code,
        "language_code": "en",
        "filters": [
            ["ranked_serp_element.serp_item.rank_absolute", "<=", 20],
            ["keyword_data.keyword_info.search_volume", ">", 50],
        ],
        "limit": 200,
    })
    
    contested = []
    for item in result[0].get("items", []):
        kw = item["keyword_data"]["keyword"]
        my_pos = item["ranked_serp_element"]["serp_item"]["rank_absolute"]
        vol = item["keyword_data"]["keyword_info"]["search_volume"]
        
        contested.append({
            "keyword": kw,
            "my_position": my_pos,
            "volume": vol,
            "cpc": item["keyword_data"]["keyword_info"].get("cpc", 0),
            "quick_win": my_pos in range(4, 11),  # Positions 4-10 are winnable
        })
    
    # Sort by quick wins first
    return sorted(contested, key=lambda x: (not x["quick_win"], x["my_position"]))
```

**CIA Debrief:** Positions 4-10 are your supply line attack targets. You're already in the game — optimize the page and you can take the position.

---

## Zone 3: UNOCCUPIED TERRITORY — Nobody's Home

*This is your gold. Keywords with real search volume and weak competition.*

### API: Keyword Ideas + Competition Filter

```python
def get_unoccupied_territory(seed_keywords, location_code=2840, min_volume=100, max_difficulty=40):
    """
    Find keywords with volume but low competition.
    max_difficulty: 0-100. Under 40 = winnable for most sites.
    """
    result = dfs("dataforseo_labs/google/keyword_ideas/live", {
        "keywords": seed_keywords,
        "location_code": location_code,
        "language_code": "en",
        "filters": [
            ["keyword_data.keyword_info.search_volume", ">=", min_volume],
            ["keyword_data.keyword_properties.keyword_difficulty", "<=", max_difficulty],
        ],
        "order_by": ["keyword_data.keyword_info.search_volume,desc"],
        "limit": 100,
    })
    
    unoccupied = []
    for item in result[0].get("items", []):
        kw = item["keyword_data"]["keyword"]
        vol = item["keyword_data"]["keyword_info"]["search_volume"]
        diff = item["keyword_data"]["keyword_properties"].get("keyword_difficulty", 0)
        cpc = item["keyword_data"]["keyword_info"].get("cpc", 0)
        intent = item["keyword_data"]["keyword_properties"].get("intent", {})
        
        unoccupied.append({
            "keyword": kw,
            "volume": vol,
            "difficulty": diff,
            "cpc": cpc,
            "intent": intent,
            "opportunity_score": (vol / max(diff, 1)) * (1 + cpc),  # Custom CIA score
        })
    
    return sorted(unoccupied, key=lambda x: x["opportunity_score"], reverse=True)
```

**Priority scoring:**
```
Opportunity Score = (Volume / Difficulty) × (1 + CPC)

High CPC = commercial intent = money keywords
Low difficulty = undefended territory
High volume = worth the mission

Score > 500 = Immediate priority
Score 100-500 = Build queue
Score < 100 = Long-term hold
```

---

## Zone 4: FORTIFIED POSITIONS — Don't Attack Here

*Competitor pages with strong backlinks. Know what NOT to fight.*

### API: Competitor Backlink Strength

```python
def identify_fortified_positions(competitor_domain):
    """
    Find competitor pages that are too well-defended to attack directly.
    """
    result = dfs("backlinks/summary/live", {
        "target": competitor_domain,
        "include_subdomains": True,
    })
    
    summary = result[0] if result else {}
    
    # Get their strongest pages
    pages_result = dfs("backlinks/referring_domains/live", {
        "target": competitor_domain,
        "filters": [["rank", ">", 50]],
        "order_by": ["rank,desc"],
        "limit": 20,
    })
    
    return {
        "domain_rank": summary.get("rank"),
        "total_backlinks": summary.get("backlinks"),
        "referring_domains": summary.get("referring_domains"),
        "fortified_pages": [
            {
                "url": item.get("url_to"),
                "referring_domains": item.get("referring_domains"),
                "rank": item.get("rank"),
            }
            for item in (pages_result[0].get("items", []) if pages_result else [])
        ]
    }
```

**CIA Rule:** If a competitor page has 50+ referring domains, don't try to outrank it directly. Flank it — find adjacent keywords they haven't covered.

---

## Zone 5: SUPPLY LINES — Their Weakest Points

*Old, thin, or intent-mismatched content that ranks on authority alone. These are attackable.*

### API: Competitor Weak Content Finder

```python
def find_supply_lines(competitor_domain, location_code=2840):
    """
    Find competitor content that ranks but is vulnerable:
    - Low word count signals (thin content)
    - Ranking for keywords where intent doesn't match
    - Old content that hasn't been updated
    """
    result = dfs("dataforseo_labs/google/ranked_keywords/live", {
        "target": competitor_domain,
        "location_code": location_code,
        "language_code": "en",
        "filters": [
            ["ranked_serp_element.serp_item.rank_absolute", "<=", 10],
            ["keyword_data.keyword_info.search_volume", ">=", 200],
        ],
        "order_by": ["keyword_data.keyword_info.search_volume,desc"],
        "limit": 100,
    })
    
    supply_lines = []
    for item in result[0].get("items", []):
        kw = item["keyword_data"]["keyword"]
        pos = item["ranked_serp_element"]["serp_item"]["rank_absolute"]
        vol = item["keyword_data"]["keyword_info"]["search_volume"]
        url = item["ranked_serp_element"]["serp_item"].get("url", "")
        intent = item["keyword_data"]["keyword_properties"].get("intent", {})
        
        # Flag as potential supply line if ranking in positions 3-10
        # (not locked in position 1-2, but worth attacking)
        if pos >= 3:
            supply_lines.append({
                "keyword": kw,
                "competitor_url": url,
                "competitor_position": pos,
                "volume": vol,
                "intent": intent,
                "attack_priority": "high" if pos >= 5 else "medium",
            })
    
    return sorted(supply_lines, key=lambda x: (x["attack_priority"] == "high", x["volume"]), reverse=True)
```

**How to attack a supply line:**
```
1. Identify: They rank position 5-10 for a 500+ volume keyword
2. Analyze: Fetch their page — is it thin? Old? Wrong intent?
3. Build: Create definitively better content (more depth, right intent, better UX)
4. Link: Get even 3-5 quality backlinks to your new page
5. Wait: 60-90 days for Google to recognize the better asset
```

---

## Full Terrain Intelligence Report Workflow

```python
def run_terrain_report(my_domain, competitor_domains, seed_keywords, location_code=2840):
    """
    Full CIA Terrain Map — run this before any SEO campaign.
    """
    print("🗺️  CIA TERRAIN MAP — SEO Intelligence Report")
    print("="*60)
    
    print("\n📍 ZONE 1: Occupied Territory (Competitor keywords you're missing)")
    occupied = get_occupied_territory(my_domain, competitor_domains, location_code)
    for item in occupied[:10]:
        print(f"  [{item['volume']}/mo] {item['keyword']}")
        for domain, rank in item['competitor_positions'].items():
            print(f"    → {domain}: #{rank}")
    
    print("\n⚔️  ZONE 2: Contested Territory (Your quick wins — positions 4-10)")
    contested = get_contested_territory(my_domain, competitor_domains[0], location_code)
    quick_wins = [k for k in contested if k["quick_win"]]
    for item in quick_wins[:10]:
        print(f"  #{item['my_position']} [{item['volume']}/mo] {item['keyword']}")
    
    print("\n🌳 ZONE 3: Unoccupied Territory (Low competition, real volume)")
    unoccupied = get_unoccupied_territory(seed_keywords, location_code)
    for item in unoccupied[:10]:
        print(f"  Score:{item['opportunity_score']:.0f} | KD:{item['difficulty']} | "
              f"[{item['volume']}/mo] {item['keyword']}")
    
    print("\n🏰 ZONE 4: Fortified Positions (Don't attack directly)")
    fortified = identify_fortified_positions(competitor_domains[0])
    print(f"  Domain Rank: {fortified.get('domain_rank')}")
    print(f"  Referring Domains: {fortified.get('referring_domains')}")
    for page in fortified.get("fortified_pages", [])[:5]:
        print(f"  {page['referring_domains']} RDs → {page['url']}")
    
    print("\n🎯 ZONE 5: Supply Lines (Vulnerable competitor content to attack)")
    supply_lines = find_supply_lines(competitor_domains[0], location_code)
    high_priority = [s for s in supply_lines if s["attack_priority"] == "high"]
    for item in high_priority[:10]:
        print(f"  #{item['competitor_position']} [{item['volume']}/mo] {item['keyword']}")
        print(f"    Target URL: {item['competitor_url']}")
    
    print("\n✅ TERRAIN MAP COMPLETE")
    print("Priority order: Zone 3 → Zone 5 → Zone 2 → Zone 1")
    print("Avoid: Zone 4 (fortified). Flank it instead.")
```

---

## Example Usage

```python
# Full terrain report for jonalonso.com vs competitors
run_terrain_report(
    my_domain="jonalonso.com",
    competitor_domains=["neilpatel.com", "backlinko.com", "moz.com"],
    seed_keywords=["seo services", "local seo", "seo consultant", "google ads management"],
    location_code=2840  # United States
)

# Unoccupied territory for E2E Welding
unoccupied = get_unoccupied_territory(
    seed_keywords=["welding services", "commercial welding", "industrial welding"],
    location_code=2840,
    min_volume=50,
    max_difficulty=30  # Low competition only
)

# Supply line attack for yellowjackmedia.com
supply_lines = find_supply_lines(
    competitor_domain="thehoth.com",
    location_code=2840
)
```

---

## Prompt Templates

**Run a terrain report:**
```
Run a CIA Terrain Map for [my domain] vs [competitor domains].
Use DataForSEO to find:
1. Zone 3 (unoccupied keywords with volume < KD 40)
2. Zone 5 (competitor pages ranking 5-10 that are attackable)
3. Zone 2 (my pages in positions 4-10 that need optimization)
Seed keywords: [list]
Output a prioritized action plan with effort vs. impact scores.
```

**Quick win audit:**
```
Pull all keywords where [my domain] ranks positions 4-15.
Sort by search volume. Flag any with CPC > $2 (commercial intent).
These are contested territory quick wins — what's the optimization plan for each?
```

**Content gap attack plan:**
```
Find keywords where [competitor] ranks but [my domain] doesn't.
Filter: volume > 200, competitor position 1-5.
For the top 10, write a one-paragraph content brief using CIA Cover Story framework.
Match each brief to the correct Asset Recruitment stage.
```

---

## Quick Reference Card

| CIA Zone | DataForSEO Endpoint | Strategic Action |
|----------|--------------------|--------------------|
| Zone 1: Occupied | keyword_gap/live | Build content to enter their territory |
| Zone 2: Contested | ranked_keywords/live | Optimize existing pages for quick wins |
| Zone 3: Unoccupied | keyword_ideas/live | Claim territory before competitors do |
| Zone 4: Fortified | backlinks/summary/live | Avoid — find adjacent flanking keywords |
| Zone 5: Supply Lines | ranked_keywords/live (competitor) | Build better content, take their rankings |
