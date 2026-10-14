# Multi-category campaign discovery

Single searches answer "what's out there for *plumbers in this city*?" Campaigns answer a sales question:

> **"Build me a list of 100 new, strong website opportunities in this market."**

## Inputs

- **Market**: a country, plus an optional state/region and city
- **Categories**: several at once, e.g. hair salons, barbers, spas, cleaning services, plumbers, electricians, car washes, caterers
- **Target**: e.g. 25, 50, 100 or 150 *new* strong leads

## How the engine works

1. **Breadth first.** Every selected category gets its first page of results, which is also the most relevant, before any category gets a second. This keeps the pool diverse.
2. **Then follow the yield.** After that, the category whose last page produced the highest share of strong leads goes next. A category that yielded nothing is tried only when nothing better remains. Categories are never forced into equal quotas.
3. **Cheapest decisions first.** Results that can be decided without a website check (no listed site, social-only, excluded) are decided immediately. Website checks run with limited concurrency, and only while the pool is still short of the target.
4. **Reuse evidence.** A website already checked earlier in the campaign is not checked again.
5. **Stop honestly.** The campaign ends when:
   - the target is reached,
   - every category is exhausted, or
   - the campaign's search capacity is used up.

   The user can also stop it at any time.

## Integrity rules

| Rule | Why |
| --- | --- |
| Deduplicate by **Google Place ID** across categories | The same business often appears as both a "spa" and a "hair salon". It counts once, and the other categories are noted. |
| **Already-saved CRM businesses don't count** | The target is *new* leads. Businesses already in the workspace are skipped before qualification and website checks, and reported separately. |
| **Healthy and unknown websites never pad the result** | Only Recommended-bucket results count. The others are set aside with a reason (healthy, needs review, low fit, excluded). |
| **Shortfalls are reported, not hidden** | If capacity runs out at 63 strong leads, the campaign reports 63. |

## What the user sees

- live progress for each category: pages searched, businesses screened, strong leads found, already in the pool, already in the CRM
- the **discovery yield** for each category, e.g. *14 / 40* (strong leads out of businesses screened). This is a discovery metric, not a sales conversion rate.
- the category mix of the final lead pool
- the set-aside results, grouped by reason, for anyone who wants to look further

Saving a lead from a campaign follows the same CRM rules as everywhere else: **Save → New**, with no contact date.
