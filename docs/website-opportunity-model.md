# Website opportunity model

The question the product answers is not *"is this a good business?"* It is:

> **Is there evidence that this business would benefit from a new or better website?**

## Three buckets

Every result belongs to exactly one bucket:

| Bucket | States | Can rank high? | In *Best opportunities*? |
| --- | --- | --- | --- |
| **Recommended** | No standalone website listed · Social-only presence · Weak website signals · Confirmed unreachable · Human *Needs work* | Yes | Yes |
| **Review** | Needs manual review · Assessment unavailable · Not yet assessed · Human *Unclear* | No: priority is capped | Never |
| **Deprioritised** | Website appears healthy · Human *Looks adequate* | No: capped lower still | Never |

The priority ceilings are **eligibility limits, not scores**. A Review or Deprioritised business cannot outrank a Recommended one, whatever its rating or review count.

## Unknown is not weak

| Situation | Wrong conclusion | What Prospect Engine says |
| --- | --- | --- |
| Google lists no website | "This business has no website" | "No website listed on Google. Whether a website exists elsewhere is unknown." |
| The site blocks bots or uses a challenge page | "Website is broken" | "Assessment unavailable" |
| A single DNS or certificate failure | "Website is dead" | Retry. A second matching failure is needed before the site is called unreachable, and temporary outages are still flagged as possible. |
| The HTML is mostly JavaScript | "Website has no content" | "Needs manual review" |

## Popularity is not need

Ratings and reviews show that a business is **viable**. They do not show website need, budget or buying intent. A very large operation (thousands of reviews) with no website-opportunity evidence is held to a low ceiling. A smaller business with weak website signals can and should rank above it.

## What counts as "weak"

Weak signals need **several meaningful, observable deficiencies**:

- the homepage is served without HTTPS
- there is no mobile viewport
- there is no contact route (no phone link, email link, contact page or contact form)
- there is no discernible navigation

A parked domain or an error page counts as weak on its own. A **single** missing item, such as a meta description or a booking link, is **never** enough.

## What counts as "healthy"

A site looks healthy when it has HTTPS, a mobile viewport, a title, a contact route, navigation and **a call to action that fits the category**:

- appointment businesses (salons, barbers, spas, dentists): a booking or appointment path
- restaurants: a menu, order or reservation path, or a contact path
- other services: a contact route

These are homepage checks of technical and contact basics. **They are not a judgement of visual design.**

## Human review

Automated checks narrow the field, and a person makes the final call:

- **Needs work** makes the business Recommended. This is the only way a site is ever recorded as "weak website" in the CRM.
- **Looks adequate** makes it Deprioritised.
- **Unclear** keeps it in Review.

A human review is tied to the specific URL that was reviewed. Reviewing a website does **not** mark a prospect as contacted.

## Zero is a valid answer

When a search turns up no strong opportunities, the product says so plainly and suggests another category or location. It never promotes unknown or healthy results to fill the list.
