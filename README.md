# 🕵️ OPTHINK Marketing Skills

> CIA-derived Operational Thinking applied to marketing, sales, SEO, PPC, and competitive intelligence.

## Skills in This Repo

| Skill | Description | Best For |
|-------|-------------|----------|
| [`opthink`](skills/opthink/) | Core OPTHINK framework — MICE, AIPDA, Elicitation, Churn Warning | Sales, copywriting, client intelligence |
| [`cia-seo`](skills/cia-seo/) | Cover Story + Asset Recruitment + Terrain Mapping for SEO | Content strategy, keyword planning, SEO briefs |
| [`cia-ppc-meta`](skills/cia-ppc-meta/) | Cover Story + Asset Recruitment + Terrain Mapping for Paid Ads | Google Ads, Meta Ads, campaign structure |
| [`cia-terrain-dataforseo`](skills/cia-terrain-dataforseo/) | Full Terrain Mapping operationalized with DataForSEO API | Competitive SEO intelligence, keyword gap analysis |

---

## The Core CIA Frameworks

### 1. 🎭 Cover Story — Lead with Their Mission, Not Your Product
CIA officers never open with who they are. They open with what matters to the target.

In marketing: stop leading with your company, product, or features. Lead with the customer's problem or aspiration. Your brand is the cover story. Their outcome is the mission.

**Application:**
- SEO → Content speaks to the searcher's intent, not the brand
- PPC → Ad copy mirrors the buyer's mission, not the product description
- Sales → Discovery calls uncover real pain before presenting solutions

---

### 2. 🕸️ Asset Recruitment — Never Ask for the Big Commitment First
CIA recruits assets in stages. No handler asks for full commitment on first contact.

In marketing: every audience has a stage. Cold, warm, hot. Matching your ask to their stage is the difference between a conversion and a bounce.

```
COLD (Stranger)     → SPOTTING     → Low-friction ask (content, awareness)
WARM (Engaged)      → ASSESSMENT   → Medium ask (download, demo)
HOT (High-intent)   → DEVELOPMENT  → Full ask (buy, book, call)
EXISTING            → HANDLING     → Retention, upsell, referral
```

**Application:**
- SEO → Match content type to funnel stage (informational → consideration → transactional)
- PPC → Campaign structure mirrors recruitment stages (cold/warm/hot audiences)
- Email → Nurture sequence paces commitment asks properly

---

### 3. 🗺️ Terrain Mapping — Know the Battlefield Before You Spend a Dollar
CIA maps every threat and asset before any operation.

In SEO and PPC: map competitor terrain before creating a single piece of content or spending a dollar on ads.

```
Zone 1: OCCUPIED    → What competitors own (avoid or outflank)
Zone 2: CONTESTED   → Where you both rank (quick wins: positions 4-10)
Zone 3: UNOCCUPIED  → High volume, low competition (claim this first)
Zone 4: FORTIFIED   → Strong backlinks / brand lock (don't attack directly)
Zone 5: SUPPLY LINES → Their weak/thin/old content (build better, take the ranking)
```

**Application:**
- SEO → DataForSEO keyword gap analysis, competitor content audit
- PPC → Competitor ad deconstruction, unoccupied keyword discovery
- Content → Zone 3 and Zone 5 are your highest-leverage starting points

---

## The OPTHINK Core (MICE + AIPDA)

### MICE — What Drives Every Buyer Decision
| Driver | Trigger | Marketing Lead |
|--------|---------|----------------|
| **Money** | ROI, savings, revenue | Lead with numbers and outcomes |
| **Ideology** | Values, identity, mission | Lead with belief and alignment |
| **Coercion** | Fear, FOMO, urgency | Lead with risk of inaction |
| **Ego** | Status, recognition, prestige | Lead with social proof and exclusivity |

### AIPDA — The Persuasion Structure
```
ANCHOR  → Make the problem or stakes visceral and immediate
IDENTIFY → Mirror the audience's identity or situation back
PROVE   → One specific, concrete example (real names, real results)
DISARM  → Address the objection before they think it
ACTIVATE → One clear action. No ambiguity.
```

---

## Example Prompts by Skill

### `opthink` — Core Sales & Marketing
```
Apply OPTHINK to this prospect email. Identify their MICE driver,
hidden objections, and the 3 questions I should ask on the next call.
[paste email]
```

### `cia-seo` — Content Strategy
```
Run a CIA Asset Recruitment map for [keyword set].
Classify each keyword by recruitment stage.
Find the gaps in the funnel and suggest the 3 missing content pieces.
```

### `cia-ppc-meta` — Paid Ads
```
Write a Google Search ad using CIA Cover Story framework for:
Product: [X]
Searcher mission: [what they're trying to accomplish]
Top objection to disarm: [Y]
CTA: [single action]
```

```
Audit this Meta campaign structure against CIA Asset Recruitment.
Are cold/warm/hot audiences getting the right creative and ask?
[paste campaign structure]
```

### `cia-terrain-dataforseo` — Competitive Intelligence
```
Run a CIA Terrain Map for [my domain] vs [competitors].
Find Zone 3 (unoccupied keywords) and Zone 5 (their weak content).
Seed keywords: [list]
Use DataForSEO workflows. Output a prioritized action plan.
```

---

## Installation (OpenClaw)

Clone or download this repo and place the `skills/` folder contents into your OpenClaw skills directory:

```bash
git clone https://github.com/YellowJackMedia/opthink-marketing-skill
cp -r opthink-marketing-skill/skills/* ~/.openclaw/skills/
```

Each skill activates automatically when a matching task is detected.

---

## Source Frameworks

- CIA Operational Psychology (declassified training concepts)
- *Never Split the Difference* — Chris Voss (FBI negotiation)
- *Spy the Lie* — Houston, Floyd, Carnicero (CIA/NSA)
- *What Every Body Is Saying* — Joe Navarro (former FBI)
- *Influence* — Robert Cialdini
- Everyday Spy — Andrew Bustamante (former CIA officer)
- DataForSEO API documentation

---

## File Structure

```
opthink-marketing-skill/
├── README.md
└── skills/
    ├── opthink/
    │   └── SKILL.md          # Core OPTHINK — MICE, AIPDA, Elicitation, Churn
    ├── cia-seo/
    │   └── SKILL.md          # Cover Story + Asset Recruitment + Terrain for SEO
    ├── cia-ppc-meta/
    │   └── SKILL.md          # Cover Story + Asset Recruitment + Terrain for PPC/Meta
    └── cia-terrain-dataforseo/
        └── SKILL.md          # Terrain Mapping with DataForSEO API workflows
```

---

## License

MIT — use freely, build on it, make it yours.

---

*Built by Rex Alonso 🦖 — Jonathan Alonso's AI agent*
