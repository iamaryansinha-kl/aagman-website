# SEO / AEO Fix Phase — How to Prompt Aagman

Page: `/how-to-prompt-aagman.html`  
Target URL: `https://www.aagman.ai/how-to-prompt-aagman.html`

---

## Phase 1 — Crawl & Index Fundamentals (P0)
*Must fix before any public promotion.*

| # | Fix | Location | Why |
|---|-----|----------|-----|
| 1.1 | Add `<html lang="en">` | `<html>` root | Tells search engines the page language |
| 1.2 | Add `<title>` | `<head>` | Currently missing. Use: `How to Prompt Aagman | Aagman` |
| 1.3 | Add meta description | `<head>` | 150-160 char summary for SERP snippet |
| 1.4 | Add canonical link | `<head>` | `<link rel="canonical" href="https://www.aagman.ai/how-to-prompt-aagman.html">` |
| 1.5 | Add favicon link | `<head>` | Re-use `/favicon.ico` or `/favicon-32x32.png` |
| 1.6 | Update `robots.txt` | root | Allow `/how-to-prompt-aagman.html` |
| 1.7 | Update `sitemap.xml` | root | Add the URL with `lastmod` and `priority="0.7"` |

---

## Phase 2 — Social & Share Optimization (P1)
*Makes the page shareable on WhatsApp, Twitter/X, LinkedIn.*

| # | Fix | Location |
|---|-----|----------|
| 2.1 | Add OG title | `<meta property="og:title">` |
| 2.2 | Add OG description | `<meta property="og:description">` |
| 2.3 | Add OG URL | `<meta property="og:url" content="https://www.aagman.ai/how-to-prompt-aagman.html">` |
| 2.4 | Add OG image (1200×630) | `<meta property="og:image">` |
| 2.5 | Add OG type | `<meta property="og:type" content="article">` |
| 2.6 | Add Twitter card tags | `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image` |

---

## Phase 3 — Structured Data / AEO (P1)
*Helps Google show rich results and answer-engine snippets.*

| # | Fix | Schema Type |
|---|-----|-------------|
| 3.1 | Wrap the page topic in `HowTo` schema | `HowTo` |
| 3.2 | Mark each prompt example as a `HowToStep` | nested in `HowTo` |
| 3.3 | Add FAQ schema for the Tips section | `FAQPage` |
| 3.4 | Add Organization / WebSite breadcrumbs | `Organization` + `BreadcrumbList` |

---

## Phase 4 — Content & Keyword AEO (P2)
*Optimizes for natural-language and question-based search.*

| # | Fix | Example |
|---|-----|---------|
| 4.1 | Add an H2 "What is Aagman?" intro | Targets "what is aagman" |
| 4.2 | Add H2 "How do I write a trading prompt?" | Targets question queries |
| 4.3 | Add H2 "Can I use Hindi or Hinglish?" | Multilingual AEO |
| 4.4 | Add H2 "Backtest vs Live vs Screener prompts" | Captures feature-intent queries |
| 4.5 | Bold key entities: symbol, stop-loss, target, backtest, screener | Already partially done |
| 4.6 | Add a short FAQ section at the bottom | 5-6 Q&A pairs |

---

## Phase 5 — UX & Internal Linking (P2)
*Keeps users on-site and distributes link equity.*

| # | Fix | Location |
|---|-----|----------|
| 5.1 | Add a nav/header linking back to `/` | Top of page |
| 5.2 | Add footer with links to `/pricing`, `/founders-letter`, `/privacy-policy` | Bottom of page |
| 5.3 | Add a "Next: See pricing" CTA at the bottom | After Tips section |
| 5.4 | Link the word "backtest" to `/` anchor or docs | In-content |
| 5.5 | Ensure CTA buttons use `app.aagman.ai/login` not just `app.aagman.ai` | Already done |

---

## Phase 6 — Measurement (P3)

| # | Fix |
|---|-----|
| 6.1 | Add `/how-to-prompt-aagman.html` to Google Search Console |
| 6.2 | Request indexing after Phase 1-2 are live |
| 6.3 | Track ranking for: "how to prompt aagman", "aagman prompt examples", "prompt aagman trading" |

---

## Suggested meta block to drop in `<head>`

```html
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>How to Prompt Aagman | Aagman</title>
  <meta name="description" content="Learn how to write natural-language prompts for Aagman. Examples for backtests, screeners, live trades, and portfolios in English, Hindi, and Hinglish.">
  <link rel="canonical" href="https://www.aagman.ai/how-to-prompt-aagman.html">
  <link rel="icon" type="image/svg+xml" href="/favicon.svg">

  <!-- Open Graph -->
  <meta property="og:title" content="How to Prompt Aagman">
  <meta property="og:description" content="Learn how to write natural-language prompts for Aagman. Examples for backtests, screeners, live trades, and portfolios.">
  <meta property="og:url" content="https://www.aagman.ai/how-to-prompt-aagman.html">
  <meta property="og:image" content="https://www.aagman.ai/uploads/og-prompt-guide.png">
  <meta property="og:type" content="article">

  <!-- Twitter -->
  <meta name="twitter:card" content="summary_large_image">
  <meta name="twitter:title" content="How to Prompt Aagman">
  <meta name="twitter:description" content="Learn how to write natural-language prompts for Aagman.">
  <meta name="twitter:image" content="https://www.aagman.ai/uploads/og-prompt-guide.png">
</head>
```

---

## Suggested sitemap entry

```xml
<url>
  <loc>https://www.aagman.ai/how-to-prompt-aagman.html</loc>
  <lastmod>2026-06-24</lastmod>
  <priority>0.7</priority>
</url>
```

---

## Recommended OG image spec

- **Size:** 1200 × 630 px
- **Text:** "How to Prompt Aagman" + subheadline
- **Background:** dark (#07090A) with mint accent (#56E8B8)
- **Font:** Bricolage Grotesque / Hanken Grotesk
- **Save as:** `/uploads/og-prompt-guide.png`
