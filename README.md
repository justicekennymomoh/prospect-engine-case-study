# Prospect Engine

## Finding businesses worth building for

Prospect Engine is a private-source prospecting and CRM product that finds local businesses with a meaningful website-sales opportunity. A small team then works those businesses from first discovery to a closed deal.

> **Raw businesses are input. Qualified website opportunities are output.**

> The application source code is private. This repository contains a sanitised case study of the product, architecture, design decisions and lessons learned.

---

## The problem

A traditional local-business search returns businesses. It does not return qualified sales opportunities.

For someone selling websites, those are different things:

- A popular business with a strong, modern website may be a **poor** lead, however many reviews it has.
- A smaller, established business may be a **much better** lead if it has:
  - no standalone website
  - only a social media presence
  - a website with clear, observable weaknesses

Directories and map searches rank businesses by relevance and popularity. Prospect Engine ranks them by **evidence of website need**.

## Product workflow

```text
DISCOVER → ASSESS → QUALIFY → SAVE → CONTACT → DEMO → FOLLOW UP → CLOSE
```

| Stage | What happens |
| --- | --- |
| **Discover** | Search a market (country, state/region, city) for one category or several. |
| **Assess** | Objective, server-side checks of each listed website's homepage. |
| **Qualify** | Each result gets a target fit, an opportunity state, and a priority with the reasons behind it. |
| **Save** | The user deliberately saves a result. Only then does it become a CRM record, at stage *New*. |
| **Contact** | Outreach is logged deliberately (email, Instagram, phone, text, in person). |
| **Demo** | Demo references move through *Not prepared → Prepared → Sent*. |
| **Follow up** | Follow-up due dates are labelled overdue, due today, due soon, future or none. |
| **Close** | The pipeline ends at *Won* or *Not interested*. |

## Website opportunity model

Every result is put in exactly one website-opportunity state:

| State | Meaning | Treatment |
| --- | --- | --- |
| **No standalone website listed** | Google did not supply a website URL. | Recommended |
| **Social-only presence** | The listed "website" is a social page, or redirects to one. | Recommended |
| **Weak website signals** | Several meaningful, observable deficiencies, or a parked/error page. | Recommended |
| **Needs manual review** | The page loads, but the evidence is ambiguous (e.g. JavaScript-rendered). | Review |
| **Assessment unavailable** | The site blocked automated access, timed out, or returned something unsupported. | Review |
| **Website appears healthy** | The basic technical and contact checks pass. | Deprioritised |

People then make the final call with a one-tap human review: **Needs work**, **Looks adequate** or **Unclear**. A *Needs work* review promotes the business, *Looks adequate* deprioritises it, and *Unclear* keeps it in review.

### Unknown is not weak

This is the model's central rule.

- If Google lists no website, that does **not** prove the business has no website. It only proves Google lists none, and the product says exactly that.
- A website that blocks automated checks is **unknown**, not weak.
- Unknown and unassessed results have a hard ceiling on their priority. They never appear in *Best opportunities*, however popular the business is.

More detail: [docs/website-opportunity-model.md](docs/website-opportunity-model.md)

## Website assessment

Website checks are objective, homepage-level observations:

- reachability and the final URL after redirects
- HTTPS
- a mobile viewport
- the page title and meta description basics
- contact routes: phone links, email links and contact pages
- a contact form
- navigation
- booking, menu, order, reservation or quote paths, where relevant to the category
- platform evidence
- limited freshness signals, such as an explicit copyright year, which is never used as sole evidence

**Prospect Engine does not claim to judge visual beauty or design quality.** A "healthy" result means only that few obvious technical or contact weaknesses were found. Visual judgement stays with a person.

## Geography integrity

Location data needed real engineering. The product compares the **requested** geography (what the user searched for) with the **returned** geography (where Google's structured address says the business is). The two are never merged.

Real regressions this fixed include:

- **Lagos, Portugal vs Lagos, Nigeria.** A search for Lagos, Nigeria could return businesses in Lagos, Portugal. The returned country now comes from Google's structured address data, never from the search request. Results in the wrong country are excluded.
- **Florida, New York vs Florida state.** A search across Florida could return businesses in the town of Florida, New York. That result is now correctly identified as outside the requested state, rather than being labelled "located in Florida".
- **City vs state/region-wide searches.** "Arizona / Arizona" means a statewide search, so Phoenix is inside it. "Arizona / Phoenix" is a city search, so Tucson is not an exact match. A city that is also a region alias, such as Abuja and the FCT, stays a city search.
- **Matching against address parts.** A street name that contains the searched city's name is not a city match.

The product currently supports markets in the **United States**, **Ireland** and **Nigeria**.

More detail: [docs/security-and-data-quality.md](docs/security-and-data-quality.md)

## Multi-category lead campaigns

A user can choose:

- a **market**
- **several categories**, e.g. hair salons, barbers, plumbers, cleaning services
- a **target**, e.g. *100 new strong leads*

The engine then screens candidates progressively:

1. Every selected category gets its first, most relevant page of results before any category gets a second.
2. After that, the category with the best recent strong-lead yield is searched next.
3. Each business is qualified with the same rules as single searches, and listed websites are checked as they arrive.
4. The campaign stops as soon as **the target is reached** or **the available search capacity is exhausted**.

The integrity rules:

- Businesses are deduplicated by **Google Place ID** across categories.
- **Businesses already saved in the CRM do not count** toward the new-lead target.
- **Healthy and unknown websites never pad the result.** If only 63 strong leads exist, the campaign reports 63.

More detail: [docs/campaign-discovery.md](docs/campaign-discovery.md)

## CRM semantics

> **Search result ≠ CRM record.**

- A search result is temporary. It has no stage, owner, notes or outreach history.
- **Save → New.** Saving creates the record at *New*, unassigned, with no contact date.
- Opening a website, Google Maps or Instagram, calling, copying a phone number, or reviewing a website **does not** mark a prospect *Contacted*.
- **Only deliberately logged outreach** changes contact status. Logging outreach is the only way a contact date gets set, and it never overwrites a later pipeline stage.
- Response, interest, demo and win rates are calculated from the outreach records themselves.

Members of the workspace share the same saved prospects, pipeline, follow-ups and demos.

## Security

At a high level:

- **Authentication** is handled by Supabase Auth, including password recovery.
- **Row-level security** is enforced in PostgreSQL. All data access is scoped to the member's workspace.
- **Server-side keys.** Third-party API keys are read only by server code. They are never sent to the browser or included in responses.
- **SSRF protection** applies to every website check:
  - only safe URL schemes and ports are allowed
  - DNS answers are checked at connection time, and private or reserved IP ranges are blocked, including encoded IPv6 forms
  - every redirect is re-validated, and the number of redirects is capped
  - response size and total time are limited
  - only HTML is accepted
- **Abuse limits.** Website checks need an authenticated workspace member and are concurrency- and rate-limited.
- **Data minimisation.** Full provider responses are never stored. Search history keeps query metadata and result counts only. Unsaved results are never written to the database.

More detail: [docs/security-and-data-quality.md](docs/security-and-data-quality.md)

## Technology

- **Next.js** (App Router)
- **TypeScript** (strict)
- **Tailwind CSS**
- **Supabase** with **PostgreSQL**
- **Supabase Auth** and **row-level security**
- **Google Places API (New)**
- **Vercel**

Architecture overview: [docs/architecture.md](docs/architecture.md)

## Quality assurance

At the time of writing:

- **559 automated tests across 46 test files**, all passing. They cover qualification, website-opportunity classification, geography matching, campaign scheduling, CRM semantics, fetch protection and UI behaviour.
- **Lint** passes.
- **TypeScript** typecheck passes.
- **Production build** passes.
- **Desktop and mobile browser QA** runs against fixture data, including 390px mobile layouts and no-horizontal-overflow checks.

Automated tests use mocked provider responses and never call paid APIs.

## Product lessons

1. **A directory is not a prospecting engine.** Finding businesses is easy. Finding the ones worth approaching is the product.
2. **Unknown must stay unknown.** A missing or blocked signal is not evidence of weakness. Treating it as weakness creates confident-looking wrong leads.
3. **Business popularity is not website need.** Thousands of reviews show a viable business. They say nothing about whether it needs a better website.
4. **High-quality filtering is worth more than an arbitrary result count.** Zero strong opportunities is a valid, honest answer, and padding the list wastes sales time.
5. **Automation should narrow the field; humans make the final commercial judgement.** The engine collects evidence and ranks it. People decide who to pitch.

## Current phase

Engineering is largely done, and the product is moving into **real commercial validation**:

```text
Search → Save → Outreach → Demos → Replies → Customers
```

Future ranking changes should come from actual sales evidence (which leads reply, request demos and buy), not from assumptions.

More detail: [docs/product-evolution.md](docs/product-evolution.md)

## Source availability

**The application source code is private. This repository contains a sanitised case study of the product, architecture, design decisions and lessons learned.**

It contains no application code, database schema, migrations, API implementation, credentials, or customer or prospect data.
