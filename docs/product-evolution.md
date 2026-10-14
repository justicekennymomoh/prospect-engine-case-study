# Product evolution

Prospect Engine was built in short, focused passes. Each one was driven by a concrete failure or a sharper understanding of what a good lead is. All dates are in October 2026.

## 1. Stable baseline (10 Oct)

- Google Places search, normalised behind a provider interface, with a mock provider for development
- a first lead score
- a basic CRM: saved prospects, pipeline stages, notes, outreach

## 2. Authentication and semantic pass (12 Oct)

- Supabase authentication and a shared team workspace
- the first strict CRM semantics: **search result ≠ CRM record**, and **only logged outreach means Contacted**

## 3. Qualification engine (12 Oct)

- a deterministic **target fit** alongside the opportunity score
- exclusion of government bodies and large institutions; chains are lowered but not excluded
- an on-demand, protected **website check**, with human *Needs work / Looks fine* review
- a CRM state machine that rejects invalid transitions

## 4. Multi-market and hardening (13 Oct)

- **Nigeria** support alongside the United States and Ireland, with per-market time zones
- security hardening of database function grants and website fetching
- verified **digital presence** (social profiles) and **contactability** for prospects
- result geography read from Google's address, not from the request

## 5. Geography integrity (13–14 Oct)

- production regressions fixed: **Lagos, Portugal vs Lagos, Nigeria** and **Florida, NY vs Florida state**
- explicit **city vs region-wide** search scopes
- verified social discovery

## 6. Website opportunity correction (14 Oct)

- one website-opportunity model with three buckets: Recommended, Review and Deprioritised
- **unknown is not weak**: blocked or unassessable sites are capped and never shown as opportunities
- **popularity is not need**: large operations with no opportunity evidence are held low
- automatic triage of every listed website in Discover, plus *Best opportunities*
- *Needs work / Looks adequate / Unclear* human review

## 7. Campaigns (14 Oct)

- multi-category discovery toward a target (e.g. 100 strong leads)
- breadth-first, then yield-driven category scheduling
- Google Place ID deduplication across categories
- **already-saved CRM businesses excluded from the new-lead target**

## What's next

Engineering is giving way to **commercial validation**:

```text
Search → Save → Outreach → Demos → Replies → Customers
```

The ranking rules are deliberately conservative and easy to explain. The next changes to them should be based on real outcomes: which leads replied, asked for demos and bought.
