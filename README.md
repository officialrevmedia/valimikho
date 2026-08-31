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
| `129898` | `#listings` | Vali's Listings |
| `129899` | `#listings` | Office Listings (HomeLife office active) |
| `129932` | `#search` | For Sale Search (full search with filters) |

Each sits inside a `.mrp-shell` wrapper providing the card styling, gold top rule, and a loading placeholder that clears via `MutationObserver` once the widget paints. A nine-second timeout ensures a spinner can never hang.

The two blocks in `#listings` each carry an `<h3 class="mrp-group-h">` heading so a visitor can tell Vali's own listings from the wider office inventory.

**Widget `129900` is retired.** It was the original showcase embedded in `#search`, which displayed a fixed set of listings with no search controls. It was replaced by `129932` on 31 August 2026. Nothing in the codebase references `129900` any more.

### If a widget ID ever changes

Three places must agree, or the loading spinner will hang for nine seconds and then clear on the timeout:

1. The shell `id` attribute, `id="mrp-XXXXXX"`
2. The `data-for` attribute on the inner `.mrp-loading` div
3. The script `src` and its `id="mrp-showcase-script-XXXXXX"`

Then add or update the ID in the `shells` array in the loading-state script near the bottom of `index.html`.

The quick-search bar under the hero scrolls the visitor to `#search`, where widget `129932` provides the real filters.

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

Fifteen closings. Seven added 31 August 2026 from Vali's OneHome listing sheets, eight carried over from the previous Wix site.

### 2026 closings (added 31 Aug 2026)

Prices confirmed 31 Aug 2026 from the OneHome "solds for website" and "some of the solds" browse exports.

| Property | City | Result | Price | Listed | Specs | MLS® |
|---|---|---|---|---|---|---|
| 80 Summerberry Way | Hamilton | Sold | $790,000 | $809,990 | 3 bed, 3 bath, 1,587 sq ft semi | 40833544 |
| 177 Whittington Drive | Ancaster | Rented | $4,600/mo | $4,600 | 4 bed, 5 bath, 3,451 sq ft detached | 40846668 |
| 360 Conklin Road, Unit E4 | Brantford | Leased | $35.00/sq ft | $35.00 | 1,000 sq ft retail / commercial | 40839263 |
| 1100 South Service Road, Unit 424 | Stoney Creek | Sold | $660,000 | $749,000 | 2,189 sq ft office condominium | 40794021 |
| 575 Conklin Road, Unit 602 | Brantford | Rented | $2,100/mo | $2,150 | 2 bed, 2 bath, 800 sq ft condo | 40813800 |
| 11 Honey Comb Trail | Welland | Sold | $625,000 | $697,990 | 4 bed, 3 bath, 2,383 sq ft detached | 40774926 |
| 6109 Chippewa Road East | Mount Hope | Sold | $1,600,000 | $1,900,000 | 3 bed, 2 bath, 2,000 sq ft on 74 acres | 40765351 |

Photos for all seven are local files in `photos/`, cropped to 3:2 and optimized. No Wix dependency.

Lease and per-square-foot figures use `class="price small"` so the type does not overflow, matching the two carried-over lease cards.

### Further closings available but not yet added

The OneHome browse exports list roughly 22 further closed transactions from 2025 and 2026 that are not yet on the site, including sales in Hamilton, Stoney Creek, Ancaster, Cambridge, Hagersville, and Brantford, and a large block of rentals at 575 Conklin Road and 305 Garner Road West. Addresses, prices, specs, and MLS® numbers are all in the exports; **photography is the only missing piece**, since the browse export does not carry usable images.

Two entries in the exports are **Pending, not closed**, and must not be added to a sold display until they firm up and close:

- 75 Ridge Road, Stoney Creek, $3,625,000
- 1270 1 Side Road, Burlington, $2,900,000


### Seal types

Three seal variants are available on `.sold-seal`:

| Markup | Renders | Used for |
|---|---|---|
| `class="sold-seal"` | Gold **Sold** | Sales |
| `class="sold-seal lease"` | Navy **Leased** | Commercial leases |
| `class="sold-seal rented"` | Green **Rented** | Residential leases |

Residential leases in the 2026 batch use **Rented**; the commercial lease uses **Leased**. The two carried-over Wix leases (202 Fair Street, 2200 Rymal Road E) still use **Leased**. Switch 202 Fair Street to `rented` if the residential distinction should apply throughout.

### The Wix photos on the earlier eight

Eight earlier closings still point at `static.wixstatic.com` and **will stop resolving when the Wix subscription lapses.** Every one carries an `onerror` fallback to `assets/photo-placeholder.jpg`, so the layout degrades gracefully rather than showing broken-image icons. It still needs fixing properly.

**Before cancelling Wix:**

1. Download each image from the Wix media manager
2. Save into `photos/`
3. In the `#sold` section, change each `src="https://static.wixstatic.com/media/..."` to `src="photos/your-file.jpg"`
4. Leave the `onerror` attribute in place as insurance

### Adding a sold property

Copy an `<article class="sold-card reveal">` block and edit address, city, specs, price, and image. The optional `<div class="specs">` line carries beds, baths, and size.

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

1. **Re-host the eight earlier sold photos** off Wix. Highest priority.
2. **Optionally add the ~22 further closings** listed in the OneHome exports. Needs photography; all other data is available. See "Further closings available but not yet added" above.
3. **Configure Formspree** and remove the `REPLACE_WITH_FORM_ID` placeholder.
4. **Replace Unsplash stock photography** (29 images) in the hero, listings backgrounds, neighbourhood cards, and blog with Vali's real photography.
5. **Instagram grid** is placeholder tiles. Connect a feed widget or drop in real post images.
6. **Blog section** has placeholder articles. Write real posts or hide the section until there is content.
7. **Social links** in the footer point at generic profiles. Swap in Vali's real URLs.
8. **Confirm the Elite Developments logo** belongs on the site. It sits in the credentials row beside HomeLife.

---

## Contact

**revmedia** (BrandRev Media & Designs Inc.)
Web: [revmedia.ca](https://revmedia.ca)
Email: info@revmedia.ca
Phone / WhatsApp: 365-357-3006
