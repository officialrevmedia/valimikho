# Vali Mikho Real Estate — valimikho.com

Production source for the Vali Mikho Real Estate website. Static build, hosted on Vercel, with live MLS® listings supplied by myRealPage under Vali's ITSO agreement.

Built and maintained by **revmedia** (BrandRev Media & Designs Inc.).

---

## Quick start

```bash
git init
git add .
git commit -m "Vali Mikho website"
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
├── photos/                         Property and lifestyle photography
├── .gitignore
└── README.md
```

`index.html` is 148 KB. All images are real files rather than base64 blobs, so they cache independently and the HTML stays small.

---

## The myRealPage integration

### How it works

Vali's ITSO ATP agreement (signed 31 January 2025, finalized 4 February 2025) authorizes myRealPage to pull listing content on his behalf. Both `valimikho.com` and `valimikho.ca` are approved display domains on the agreement. The authorized feeds are **IDX** and **Office Active Listings**, both zero-fee. VOW was not purchased.

Three widgets are embedded:

| Widget ID | Section | Purpose |
|---|---|---|
| `129898` | `#listings` | Primary featured listings grid |
| `129899` | `#listings` | Secondary listings block |
| `129900` | `#search` | Full for-sale MLS® search |

Each sits inside a `.mrp-shell` wrapper providing the card styling, gold top rule, and a loading placeholder that clears via `MutationObserver` once the widget paints. A nine-second timeout ensures a spinner can never hang.

### Changing what a widget displays

Widget content is controlled in the **myRealPage Private Office**, not in this codebase. To change which listings appear, which cities are covered, or result counts: log in, find the widget by ID, adjust filters, save. Changes are live immediately with no deploy.

### Adding a widget

```html
<div class="mrp-shell reveal" id="mrp-XXXXXX">
  <div class="mrp-loading" data-for="mrp-XXXXXX">
    <div class="ring" aria-hidden="true"></div>
    <div class="txt">Loading listings</div>
  </div>
  <script src="https://idx.myrealpage.com/wps/rest/67205/l/showcase/v2/XXXXXX/in.js" async id="mrp-showcase-script-XXXXXX"></script>
</div>
```

Then add the new ID to the `shells` array in the loading-state script near the bottom of `index.html`.

### Styling

MRP injects its own markup. The `MYREALPAGE IDX WIDGET SKIN` CSS block overrides typography, colours, and radii to match the page. If myRealPage ships a markup change and something looks off, adjust selectors there.

---

## Recently Sold section

Eight real closings, carried over from the previous Wix site:

| Property | City | Result |
|---|---|---|
| 12 Solomon Crescent | Hamilton | $890,000 |
| 2 Trenholme Crescent | Hamilton | $861,777 |
| 541 East 16th Street | Hamilton | $856,000 |
| 75 Anna Capri Drive | Hamilton | $678,000 |
| 754 Upper Wellington | Hamilton | $545,000 |
| 401 Shellard Lane #808 | Brantford | $500,000 |
| 202 Fair Street | Hamilton | $4,500/month leased |
| 2200 Rymal Road E #8 | Stoney Creek | $20.00/sq ft net lease |

### The photos are still on Wix

These images point at `static.wixstatic.com` and **will stop resolving when the Wix subscription lapses.** Every one carries an `onerror` fallback to `assets/photo-placeholder.jpg`, so the layout degrades gracefully rather than showing broken-image icons. It still needs fixing properly.

**Before cancelling Wix:**

1. Download each image from the Wix media manager
2. Save into `photos/`
3. In the `#sold` section, change each `src="https://static.wixstatic.com/media/..."` to `src="photos/your-file.jpg"`
4. Leave the `onerror` attribute in place as insurance

### Adding a sold property

Copy an `<article class="sold-card reveal">` block and edit address, city, price, and image. Use `class="sold-seal lease"` instead of `class="sold-seal"` for leases, and `class="price small"` for lease figures so the type does not overflow.

---

## Forms

The evaluation form posts to Formspree. **It is not configured yet.**

1. Create a free form at [formspree.io](https://formspree.io) pointed at `valimikho@gmail.com`
2. In `index.html`, find `REPLACE_WITH_FORM_ID` and substitute the real form ID

Until that is done, submitting the form opens the visitor's mail client pre-filled with their answers addressed to Vali, so no enquiry is ever silently lost. Once configured, the fallback disengages automatically.

---

## SEO

### In place

- Title and meta description targeting Hamilton and Burlington REALTOR® intent
- Canonical to `https://www.valimikho.com/`
- Open Graph and Twitter Cards with a branded 1200x630 preview
- Geo meta for the Burlington office
- The Google Search Console verification token carried over from the Wix site, so verification survives the migration
- Preconnect and DNS-prefetch for the myRealPage IDX domain
- Lazy loading and async decoding on all below-the-fold images
- 301 redirects from the old Wix URL structure (see `vercel.json`)

### Structured data

Five JSON-LD entities in one `@graph`: `RealEstateAgent` (full NAP, hours, service areas, offer catalog), `Person`, `WebSite` with SearchAction, `BreadcrumbList`, and `FAQPage` with seven questions.

Validate changes at [validator.schema.org](https://validator.schema.org/) and Search Console's Rich Results Test. Keep the FAQPage JSON answers matching the visible FAQ text, or the rich result becomes ineligible.

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
- Listing brokerage name on listing displays (handled by the widget)
- Contact method visible beside listings
- CREA trademark statement for MLS® and REALTOR® marks
- Privacy policy at `#privacy`, disclosing that data may be shared with ITSO

Trademark form: `MLS®`, `Multiple Listing Service®`, and `REALTOR®` always in full capitals followed by the registered symbol. Never use MLS® in a domain, email address, or social handle.

---

## Domain and DNS

In Vercel, **Settings → Domains**, add `valimikho.com` and `www.valimikho.com`. At the registrar, remove the old Wix records and add:

- A record `@` → `76.76.21.21`
- CNAME `www` → `cname.vercel-dns.com`

SSL provisions automatically. Allow up to a few hours for propagation. Keep Wix live until DNS has cut over.

---

## Accessibility and quality baseline

- Single `<h1>`, ordered heading hierarchy
- Alt text on all 43 images
- `aria-label` on landmarks, `aria-expanded` on the FAQ accordion
- Mobile nav overlay traps focus, closes on Escape, restores focus on close
- `prefers-reduced-motion` respected
- No `localStorage` or `sessionStorage`
- Security headers and HSTS via `vercel.json`

---

## Still to do before launch

1. **Re-host the sold photos** off Wix. Highest priority.
2. **Configure Formspree** and remove the `REPLACE_WITH_FORM_ID` placeholder.
3. **Replace Unsplash stock photography** (29 images) in the hero, listings backgrounds, neighbourhood cards, and blog with Vali's real photography.
4. **Instagram grid** is placeholder tiles. Connect a feed widget or drop in real post images.
5. **Blog section** has placeholder articles. Write real posts or hide the section until there is content.
6. **Social links** in the footer point at generic profiles. Swap in Vali's real URLs.
7. **Confirm the Elite Developments logo** belongs on the site. It sits in the credentials row beside HomeLife.

---

## Contact

**revmedia** (BrandRev Media & Designs Inc.)
Web: [revmedia.ca](https://revmedia.ca)
Email: info@revmedia.ca
Phone / WhatsApp: 365-357-3006
