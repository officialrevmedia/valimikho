# Vali Mikho Real Estate — valimikho.com

Production source for the Vali Mikho Real Estate website. Static single-page build, hosted on Vercel, with live MLS® listings supplied by myRealPage under Vali's ITSO agreement.

Built and maintained by **revmedia** (BrandRev Media & Designs Inc.).

---

## Quick start

```bash
git init
git add .
git commit -m "Vali Mikho website, city listings build"
git remote add origin git@github.com:officialrevmedia/valimikho-website.git
git push -u origin main
```

Then in Vercel: **Add New Project → Import** the repo → **Deploy**. No build step, no framework preset. It is a static site.

---

## Repository structure

```
valimikho-website/
├── index.html                      Markup, CSS, JS, and schema. Single file.
├── 404.html                        Branded not-found page
├── og-cover.jpg                    1200x630 social preview card
├── robots.txt                      Crawler rules
├── sitemap.xml                     XML sitemap
├── vercel.json                     Redirects, cache headers, security headers
├── assets/
│   ├── vm-logo-navy.png            Wordmark (footer uses a CSS invert filter)
│   ├── vali-mikho-headshot.jpg     Full portrait, About section
│   ├── vali-mikho-portrait.jpg     Smaller portrait, About strip
│   ├── homelife-logo.png           Brokerage mark
│   ├── elite-developments.png      Affiliate mark
│   └── photo-placeholder.jpg       Branded fallback for missing images
├── photos/                         Sold property photography
├── .gitignore
└── README.md
```

---

## The listings system

### Two separate things

**Vali's own listings** sit in the `#listings` section near the top of the page. Two widgets, both loaded on page view:

| Widget ID | Shows |
|---|---|
| `129898` | Vali's own active listings |
| `129899` | HomeLife Professionals Realty office listings |

**Browse by city** sits in the `#search` section. Nine widgets, one per city, each behind a tab:

| City | Widget ID |
|---|---|
| Hamilton | `129949` |
| Ancaster | `129950` |
| Burlington | `129951` |
| Oakville | `129952` |
| Mississauga | `129953` |
| Guelph | `129954` |
| Milton | `129955` |
| Toronto | `129956` |
| Niagara | `129957` |

There are no property-type, price, or bedroom filters anywhere on the site. City is the only choice a visitor makes.

### How the tabs load

Nine Mapbox widgets loading at once would be punishing on a phone, so each city's script is injected only when its tab is first opened. Hamilton loads when the search section scrolls into view; the rest load on demand and stay cached for the rest of the session.

The injection matters: the myRealPage script looks itself up by `id` when it executes, and falls back to `document.write` if it can't find itself, which would wipe the page. The loader sets the `id` on the script element **before** appending it, so the lookup always succeeds. Keep that ordering if you touch this code.

### Adding or changing a city

1. Build the predefined search and showcase in the myRealPage Private Office
2. In `index.html`, find the `city-tabs` block and add a button:

```html
<button class="city-tab" role="tab" id="tab-SLUG" aria-controls="panel-SLUG"
        aria-selected="false" data-city="SLUG" data-mrp="WIDGETID">City Name</button>
```

3. Add the matching panel in `city-panels`, copying an existing one and swapping the slug, name, description, and widget id
4. Add the city to the `#qs-where` dropdown in the hero
5. Optionally add it to the area cards under the map with `data-goto-city="SLUG"`

The JavaScript picks up new tabs automatically. Nothing else to wire.

### Changing what a widget shows

Widget content is controlled in the **myRealPage Private Office**, not in this codebase. Adjust the predefined search filters and save. Changes are live immediately with no deploy.

### The 100-listing cap

ITSO MLS® Rules, Article 9.05(m), limit any single IDX inquiry to 100 current listings. This is in Vali's signed agreement and cannot be raised. Cities with more than 100 active listings will show the first hundred. Set the sort order to newest-first in myRealPage so the hundred shown are the most recent.

---

## Recently Sold

Fifteen real transactions. Seven use locally-hosted photos in `photos/`. Eight still load from `static.wixstatic.com`.

### The Wix photos will break

Those eight images **stop resolving when the Wix subscription lapses.** Each carries an `onerror` fallback to `assets/photo-placeholder.jpg`, so the layout degrades to a branded card rather than showing broken-image icons. It still needs fixing properly.

**Before cancelling Wix:**

1. Download each image from the Wix media manager
2. Save into `photos/`
3. In the `#sold` section, change each `src="https://static.wixstatic.com/media/..."` to `src="photos/your-file.jpg"`
4. Leave the `onerror` attribute in place as insurance

### Adding a sold property

Copy an `<article class="sold-card reveal">` block and edit address, city, price, and image. Use `class="sold-seal lease"` instead of `class="sold-seal"` for leases, and `class="price small"` for lease figures so the type does not overflow.

---

## Forms

The evaluation form builds a pre-filled email to `valimikho@gmail.com` when submitted. It works with no third-party service and no configuration.

To route submissions into a CRM later, add a Formspree or serverless endpoint as the form's `action` and remove the submit handler in the `EVALUATION FORM` script block.

---

## What was removed, and why

Three sections were deleted because the content was fabricated rather than merely unfinished:

**Testimonials.** Invented client names with stock-photo faces making specific performance claims. For a licensed REALTOR that is a RECO advertising problem, not a polish issue. Do not re-add testimonials unless they are genuine and the client has consented.

**Area statistics.** Median prices and sold counts on the neighbourhood cards were invented. Replaced with a link into the matching city tab. If Vali wants real numbers there, pull them from the board and update quarterly.

**Blog and Instagram.** Placeholder articles and stock tiles posing as his feed. Both removed along with their now-dead CSS.

---

## SEO

- Title and meta description targeting the nine served cities
- Canonical to `https://www.valimikho.com/`
- Open Graph and Twitter Cards with a branded 1200x630 preview
- Geo meta for the Burlington office
- Google Search Console verification token carried over from the Wix site, so verification survives the migration
- Preconnect and DNS-prefetch for the myRealPage IDX domain
- Lazy loading and async decoding on all below-the-fold images
- 21 redirects from old Wix URLs and from each city slug (see `vercel.json`)

### Structured data

Five JSON-LD entities in one `@graph`: `RealEstateAgent` (full NAP, hours, nine service areas, offer catalog), `Person`, `WebSite` with SearchAction, `BreadcrumbList`, and `FAQPage` with seven questions.

Validate changes at [validator.schema.org](https://validator.schema.org/). Keep the FAQPage JSON answers matching the visible FAQ text, or the rich result becomes ineligible.

### Post-launch checklist

1. Submit `https://www.valimikho.com/sitemap.xml` in Google Search Console
2. Request indexing on the homepage
3. Confirm the Wix redirects resolve
4. Claim and link the Google Business Profile
5. Add GA4 (tag goes just before `</head>`)

---

## Compliance

Required by the ITSO agreement and the CREA Trademark Manual. Already in the build. **Do not remove.**

- Copyright: "Listing content protected by copyright and licensed by Information Technology Systems Ontario"
- Personal, non-commercial use notice
- "Deemed reliable but not guaranteed accurate" disclaimer
- Listing brokerage name on every listing (handled by the widget, do not hide or shrink it with CSS)
- Contact method visible beside listings
- CREA trademark statement for MLS® and REALTOR® marks
- Privacy policy at `#privacy`, disclosing that data may be shared with ITSO

Trademark form: `MLS®`, `Multiple Listing Service®`, and `REALTOR®` always in full capitals followed by the registered symbol. Never use MLS® in a domain, email address, or social handle.

### Authorized domains

The ITSO agreement authorizes exactly two display domains: **valimikho.com** and **valimikho.ca**. The widgets may be domain-locked, and displaying ITSO listing content anywhere else is outside what Vali signed. Do not run the live widgets on a GitHub Pages URL or any other staging domain.

---

## Domain and DNS

In Vercel, **Settings → Domains**, add `valimikho.com` and `www.valimikho.com`. At the registrar, remove the old Wix records and add:

- A record `@` → `76.76.21.21`
- CNAME `www` → `cname.vercel-dns.com`

SSL provisions automatically. Allow up to a few hours for propagation. Keep Wix live until DNS has cut over.

---

## Quality baseline

Verified in a headless browser across eleven viewports from 320px to 1920px:

- Zero horizontal overflow at every breakpoint
- Zero console errors
- Single `<h1>`, ordered heading hierarchy
- Alt text on every image
- Tabs support arrow keys, Home and End, with `aria-selected` and `hidden` maintained
- Mobile nav traps focus, closes on Escape, restores focus on close
- `prefers-reduced-motion` respected
- No `localStorage` or `sessionStorage`
- Security headers and HSTS via `vercel.json`

---

## Still to do before launch

1. **Re-host the eight sold photos** off Wix. Highest priority.
2. **Replace the remaining stock photography** in the hero, seller, and invest sections with Vali's own images.
3. **Social links** in the footer point at generic profiles. Swap in Vali's real URLs.
4. **Confirm the Elite Developments logo** belongs in the credentials row beside HomeLife.
5. **Set sort order to newest-first** on each of the nine city searches in myRealPage.

---

## Contact

**revmedia** (BrandRev Media & Designs Inc.)
Web: [revmedia.ca](https://revmedia.ca)
Email: info@revmedia.ca
Phone / WhatsApp: 365-357-3006
