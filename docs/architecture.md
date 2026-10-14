# Architecture

This is a conceptual overview. It describes the shape of the system and its boundaries, not how they are implemented.

## High-level shape

```text
 Browser (Next.js UI)
   │  search inputs, saves, outreach logs, human reviews
   ▼
 Server routes (Next.js, server-only)
   ├── Business search ─────────► Google Places API (New)
   ├── Website assessment ──────► Public business websites (protected fetch)
   ├── Optional social discovery ► Web search provider
   └── Workspace data ──────────► Supabase (PostgreSQL + Auth + RLS)
```

- The browser **never** calls third-party providers directly. All provider requests go through server routes, which hold the keys.
- Server routes return **normalised observations**, never raw provider payloads or raw fetched HTML.
- Every workspace read and write goes through one repository boundary, backed by Supabase, with row-level security enforced in the database.

## Core domains

| Domain | Responsibility |
| --- | --- |
| **Search** | Builds the provider query from a market, region, city and category. Handles paging and the result limit, and deduplicates by Google Place ID. |
| **Geography** | Compares the requested and returned geography using structured address data, and classifies each search as a city search or a region-wide search. |
| **Qualification** | Gives each result a deterministic target fit, exclusion reasons (public bodies, large institutions, wrong market) and a priority. Every decision comes with *why promising*, *concerns* and *unknowns*. |
| **Website opportunity** | One derived model that combines Google's website presence, independent homepage observations and optional human review. It produces a state, a bucket, reasons and a priority ceiling. |
| **Campaigns** | Multi-category, target-driven discovery that reuses the same qualification and opportunity rules. |
| **Contactability and presence** | Public business phone and contact routes, plus verified social and digital presence. |
| **CRM** | Saved prospects, the pipeline, outreach, follow-ups, demos and analytics. |

## Determinism

Qualification and website classification are **deterministic rules over verifiable signals**. There is no machine-learning model, no LLM judgement and no screenshot-to-model scoring. The same inputs always give the same ranking, and each ranking has human-readable reasons.

## Transient vs persisted data

| Data | Lifetime |
| --- | --- |
| Search results (candidates) | Transient, held in the browser session |
| Website observations for unsaved results | Short-lived browser cache |
| Search history | Persisted: query metadata and result counts only |
| Saved prospects | Persisted CRM records, with a qualification snapshot |
| Outreach, notes, follow-ups, demos | Persisted, scoped to the workspace |

A business becomes persisted data only when a person deliberately saves it.

## Deployment

- The app is hosted on **Vercel**.
- **Supabase** provides PostgreSQL, authentication and row-level security.
- Secrets live only in the hosting environment's server configuration. The only file committed to the repository is a template with empty values.
- A local preview mode with fictional fixture data supports visual QA without touching production data. It is ignored in production builds.
