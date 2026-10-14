# Security and data quality

This page describes principles and controls at a high level. Implementation details are deliberately left out.

## Authentication and authorisation

- Accounts are handled by **Supabase Auth**, including sign-in, password recovery and invitation callbacks.
- **Row-level security** in PostgreSQL scopes every table to the member's workspace. The database enforces this as well as the application.
- Workspace membership is managed by owners and admins.
- Privileged service credentials are never used in the browser.

## Secrets

- Third-party API keys (places search, web search) are **server-only**. They are never put in client bundles or API responses.
- Only an environment template with empty values is committed. Real environment files and hosting credentials are excluded from version control.
- Automated tests use mocked responses, so they never need or call live paid APIs.

## Fetching untrusted websites safely (SSRF protection)

Checking a business website means the server fetches a URL it does not control. The protections:

- **URL validation**: only web schemes and standard ports are allowed, and embedded credentials are rejected.
- **DNS validation at connection time**: every resolved address is checked, and private, loopback, link-local and reserved ranges are blocked, including IPv4-mapped and encoded IPv6 forms.
- **Redirects**: every redirect destination is re-validated, and the redirect count is capped.
- **Resource limits**: a shared deadline per check, a maximum response size, and HTML-only responses.
- **Request origin**: for saved prospects, the URL comes from the stored record, not from the incoming request.
- **Abuse limits**: checks need an authenticated workspace member and are limited in concurrency and rate.
- **Output**: only structured observations are returned, never raw fetched HTML.
- Social media destinations are recognised and **not** fetched as business websites.

## Data minimisation

- Full provider responses are not stored.
- Search history keeps **query metadata and result counts** only.
- Unsaved search results are **never** written to the database.
- Provider content is handled under the provider's terms, with attribution shown alongside results.
- Users are told not to enter sensitive customer data.

## Data quality: geography

The product never trusts the *requested* location as the *returned* location.

| Regression | Root cause | Fix |
| --- | --- | --- |
| Lagos, Nigeria search → Lagos, **Portugal** | The returned country was inferred from the request | The returned country is read from the provider's structured address. If the country is missing, it is never assumed to be the requested one. |
| Florida (state) search → Florida, **New York** | A matching place name was treated as a matching state | Geography is matched against whole structured address parts. A result outside the requested state is excluded, even when the name matches. |
| "Arizona / Arizona" called Phoenix "outside Arizona" | A repeated region name was treated as a city search | Searches are classified explicitly as **city** or **region-wide**. |
| A street name containing the city's name | Substring matching | Only whole address components count. |

Each of these has regression tests that run with the full suite.

## Data quality: CRM truth

- A search result is not a CRM record. Saving creates *New*.
- Opening links, calling, copying a number or reviewing a website never marks a prospect *Contacted*.
- Only logged outreach sets the contact date. Moving a prospect to *Contacted* manually without outreach is rejected.
- Analytics are calculated from outreach records, not from inferred activity.
