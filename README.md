# SiteHealth

A website audit tool for agencies. Enter a URL and get a prioritized report covering
performance, SEO, accessibility and technical issues, with fixes tailored to the
platform the site runs on (WordPress, Shopify, Webflow, Framer, GoHighLevel).

## What it checks

- **Performance:** mobile Lighthouse score, Largest Contentful Paint, layout stability (CLS), and blocking time, via Google PageSpeed Insights.
- **SEO:** title and meta description length, H1 count, canonical tag, Open Graph tags, structured data.
- **Accessibility:** page language, mobile viewport, image alt text, form labels.
- **Technical:** HTTPS, redirects, server response time, security headers, robots.txt, sitemap.xml.

Scores are weighted averages: pass counts 1, check counts 0.5, fix counts 0.

## Run locally

```bash
npm install
cp .env.example .env.local   # optional: add a PageSpeed API key
npm run dev
```

Open http://localhost:3000.

## Deploy

1. Push this folder to a GitHub repository.
2. In Vercel, choose **Add New → Project** and import the repo. Framework is detected as Next.js.
3. Add `PAGESPEED_API_KEY` under Settings → Environment Variables (optional but recommended).
4. Deploy. Each pull request gets its own preview URL.

Audits can take up to 30 seconds because of the PageSpeed call. `maxDuration` is set to 60 seconds,
which requires a Vercel plan that allows that limit. If you are on the Hobby plan and see timeouts,
lower it to 10 in `app/report/page.js` and `app/api/audit/route.js`, and the speed section will
fail more often.

## API

`GET /api/audit?url=example.com` returns the full report as JSON.

## Notes for production

- The app fetches arbitrary URLs from the server. `normalizeUrl` blocks localhost and private IP ranges, but for a public product also resolve the hostname and check the resulting IP before fetching.
- Reports live in the URL, so nothing is stored. To add history, monitoring and white-label PDFs, add Postgres (Neon or Supabase) and a Vercel Cron job for weekly re-audits.
- Accessibility is covered by static HTML checks. For full axe-core coverage, run the audit with Playwright in a separate worker, since headless browsers don't fit well in standard Vercel functions.

## Roadmap

1. Broken link checker (internal links, sampled).
2. Saved sites and history charts.
3. PDF export and white-label branding.
4. Weekly monitoring with email alerts.
