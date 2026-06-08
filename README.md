# Vali Mikho Real Estate Website

Production source for [valimikho.com](https://valimikho.com). Static site, hosted on Vercel, listings curated through `listings.json`.

## Tech stack at a glance

- **Frontend:** Single-page HTML, no framework. All CSS and JS inline in `index.html`.
- **Listings:** `listings.json` (manually curated, Option A approach).
- **Hosting:** Vercel (free tier).
- **Domain:** valimikho.com (DNS managed at registrar).
- **MLS authorization:** ITSO ATP agreement, signed January 31, 2025. Vendor: myRealPage. Both `valimikho.com` and `valimikho.ca` are approved display domains.

## Repository structure

```
valimikho-website/
├── index.html          Main homepage. All design + scripts live here.
├── listings.json       Active and sold listings. Edit this to update the site.
├── photos/             Property photos (optional, see below).
├── robots.txt          SEO crawler rules.
├── sitemap.xml         SEO sitemap.
├── .gitignore          Files Git should ignore.
└── README.md           This file.
```

## Adding a new listing

1. Open `listings.json` in any text editor (VS Code recommended).
2. Copy an existing listing block, paste it at the top of the file.
3. Edit the values for the new property. The required fields:

```json
{
  "id": "8",
  "status": "active",
  "mls": "H4209999",
  "address": "Street Address Here",
  "city": "Hamilton",
  "neighbourhood": "Westdale",
  "price": 1295000,
  "beds": 4,
  "baths": 3,
  "sqft": 2450,
  "acres": "0.18",
  "type": "Detached",
  "photo": "https://images.unsplash.com/...",
  "shortDescription": "One-line headline",
  "brokerage": "HomeLife Professionals Realty Inc."
}
```

4. Optional fields:
   - `"tag": "New"` or `"tag": "Pre-Con"` shows a corner badge.
   - `"priceDisplay": "From $749K"` overrides the auto-formatted price (useful for ranges).
   - `"acres"` only shows if included.

5. Commit and push:
```bash
git add listings.json
git commit -m "Added 142 Main St listing"
git push
```

Vercel auto-rebuilds in about 30 seconds. The site is live with the new listing.

## Marking a listing as sold

In `listings.json`, find the listing and change two things:
```json
"status": "sold",
"soldPrice": 1612000
```

It drops off the active grid on the homepage. Once the sold archive page is built, it'll show up there with the over/under-asking calculation.

## Photo handling

Three options, in order of recommendation:

1. **Cloudinary (free tier).** Upload property photos, get optimized URLs, paste into `listings.json`. Best for large galleries and image performance.
2. **Imgur or another image host.** Quick and free, less control over compression.
3. **Local `photos/` folder.** Add photos to the `photos/` folder in the repo, then reference as `"photo": "photos/142-sterling-1.jpg"`. Works fine for low volume but bloats the repo over time.

For the starter listings using Unsplash URLs, those are placeholders only. Replace with real property photos before going live.

## Editing rules for listings.json

The file is JSON, so it's strict about formatting. Common pitfalls:

- Every listing except the very last one needs a comma after the closing `}`.
- The whole file starts with `[` and ends with `]`.
- Numbers (price, beds, sqft) do not get quotation marks.
- Everything else (addresses, text) needs double quotes around it.
- No trailing commas. The last entry inside any object cannot have a comma after it.

If you push a broken `listings.json`, the listings section on the homepage will show a fallback "listings being updated" message instead of breaking the whole site.

## Deployment

The site auto-deploys on every push to the `main` branch:

1. Push to GitHub.
2. Vercel detects the change.
3. Vercel rebuilds and deploys in about 30 seconds.
4. Live at valimikho.com.

To deploy manually, open the project in [vercel.com](https://vercel.com) and trigger a redeploy from the dashboard.

## Compliance reminders

Per the ITSO agreement, every listing display must include:

- Listing brokerage name (handled automatically by the template).
- MLS® reference number (handled automatically).
- Trademark statement in the footer (already in the homepage footer).
- "Deemed reliable but not guaranteed" disclaimer (already in the homepage footer).

Don't strip these out. If a listing's `brokerage` field is missing, the template defaults to "HomeLife Professionals Realty Inc."

## Domain and DNS

Domain `valimikho.com` is owned by Vali. DNS points at Vercel:

- A record `@` → `76.76.21.21`
- CNAME `www` → `cname.vercel-dns.com`

SSL is auto-provisioned by Vercel.

## Contact

Built and maintained by **revmedia** (BrandRev Media & Designs Inc.).

- Web: [revmedia.ca](https://revmedia.ca)
- Email: info@revmedia.ca
- Phone: 365-357-3006
