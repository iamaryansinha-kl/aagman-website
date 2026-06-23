# Āagman Blog — Astro Implementation Scope

> Senior-engineer scoping doc for adding a blog to the existing static-HTML Aagman site using Astro.
> Last updated: 2026-06-23

---

## 1. Current state

The Aagman site is a set of pre-built static HTML files at the repo root:

```
/
├── index.html                  (homepage — terminal variant)
├── landing-terminal.html       (duplicate of homepage)
├── pricing.html
├── founders-letter.html
├── terms.html
├── privacy-policy.html
├── risk-disclosure.html
├── cancellation-refunds.html
├── support.js                  (shared client-side runtime)
├── uploads/                    (images)
├── robots.txt
├── sitemap.xml
└── docs/                       (project docs)
```

- No build step, no framework, no `package.json`.
- Deployed to Vercel as static files.
- Domain target: `aagman.ai`.
- SEO/GEO/AEO foundation already applied (meta tags, canonicals, schema, sitemap, etc.).

---

## 2. Goal

Add a high-performance, SEO/AEO-optimized blog that:
1. Can be authored and maintained by Claude / Kimi Code with minimal friction.
2. Supports non-technical contributors later (via a Markdown editor or headless CMS).
3. Does not break the existing static HTML site or its SEO work.
4. Lives cleanly under the Aagman brand and domain.

---

## 3. Recommended architecture

### Decision: run Astro as a **subpath**, not a subdomain

| Option | URL | Pros | Cons |
|--------|-----|------|------|
| **A. Subpath** `aagman.ai/blog/` | Keeps SEO authority on root domain; users stay on same site; simpler analytics. | Requires Vercel routing config to combine static files + Astro output. |
| **B. Subdomain** `blog.aagman.ai` | Clean separation; no routing conflicts. | Splits domain authority; requires DNS + separate Vercel project; cross-domain analytics. |
| **C. Full migration** | Everything in Astro. | Best long-term. | Highest effort; re-implements existing pages. |

**Recommendation: start with Option A (`/blog`).** It gives the strongest SEO signal, keeps everything under one property, and can be evolved into Option C later without URL changes.

---

## 4. Repo structure after implementation

```
aagman-website-work/
│
├── index.html                          (existing static pages — unchanged)
├── pricing.html
├── terms.html
├── privacy-policy.html
├── risk-disclosure.html
├── cancellation-refunds.html
├── founders-letter.html
├── support.js
├── uploads/
├── robots.txt                          (updated to include /blog)
├── sitemap.xml                         (updated to include /blog posts)
│
├── blog/                               (new Astro app root)
│   ├── astro.config.mjs
│   ├── package.json
│   ├── src/
│   │   ├── content/
│   │   │   ├── config.ts             (content collection schema)
│   │   │   └── posts/
│   │   │       ├── first-post.mdx
│   │   │       └── second-post.mdx
│   │   ├── layouts/
│   │   │   ├── BaseLayout.astro      (shared shell)
│   │   │   └── PostLayout.astro      (blog post template)
│   │   ├── components/
│   │   │   ├── Seo.astro             (meta + OG + Twitter + schema)
│   │   │   ├── Header.astro          (minimal nav back to main site)
│   │   │   ├── Footer.astro          (link back to main site footer)
│   │   │   ├── PostCard.astro        (listing card)
│   │   │   ├── RelatedPosts.astro
│   │   │   └── Breadcrumbs.astro
│   │   ├── pages/
│   │   │   ├── index.astro           (/blog — listing page)
│   │   │   ├── [slug].astro          (/blog/:slug — post page)
│   │   │   ├── category/[category].astro
│   │   │   ├── author/[author].astro
│   │   │   └── rss.xml.js            (RSS feed)
│   │   └── styles/
│   │       └── blog.css
│   └── public/
│       └── blog-og/                  (fallback OG images)
│
├── vercel.json                         (new — routes static files + rewrites /blog/*)
└── docs/
    └── blog-astro-scope.md             (this file)
```

---

## 5. Why Astro specifically

| Requirement | How Astro handles it |
|-------------|----------------------|
| **Static generation** | `output: 'static'` produces plain HTML at build time. Best for SEO crawlability. |
| **Zero JS by default** | Astro ships no client-side JS unless explicitly added. Fast Core Web Vitals. |
| **Markdown/MDX native** | Content lives as `.md` / `.mdx` files. Claude / Kimi can write and edit them directly. |
| **Content Collections** | Type-safe frontmatter schema via Zod. Prevents broken metadata. |
| **Image optimization** | Built-in `<Image />` component with automatic sizing and formats. |
| **Sitemap + RSS** | Official `@astrojs/sitemap` and simple RSS endpoint. |
| **Partial hydration** | Only hydrate interactive components (e.g., newsletter form) when needed. |
| **Vercel-ready** | `@astrojs/vercel` adapter supports static output and serverless if needed later. |

---

## 6. Technical SEO design

### 6.1 URL structure

```
/blog/                              → listing page
/blog/first-post/                   → individual post (no .html extension)
/blog/category/ai-trading/          → category archive
/blog/author/ajit-kumar/            → author archive
/blog/rss.xml                       → RSS feed
```

All post slugs derived from frontmatter `slug` field, not file name, to allow Hindi/English flexibility.

### 6.2 Frontmatter schema (content collection)

```ts
const posts = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string().max(70),
    description: z.string().max(160),
    slug: z.string(),
    pubDate: z.date(),
    updatedDate: z.date().optional(),
    author: z.string(),
    authorSlug: z.string(),
    category: z.string(),
    tags: z.array(z.string()).default([]),
    featured: z.boolean().default(false),
    canonical: z.string().optional(),
    ogImage: z.string().optional(),
    readingTime: z.number().optional(),
  }),
});
```

### 6.3 Per-page SEO output

Each page generates:
- `<title>` (≤ 60 chars)
- `<meta name="description">`
- `<link rel="canonical">`
- Open Graph tags (`og:title`, `og:description`, `og:image`, `og:url`, `og:type=article`)
- Twitter Card tags
- `html lang="en"` (extendable to `hi` later)
- `robots` meta (default `index, follow`)

### 6.4 Structured data

Each post embeds JSON-LD:
- `BlogPosting` schema
- `BreadcrumbList` schema
- `Organization` schema (consistent with main site)
- `Person` schema for author
- `FAQPage` schema when post contains FAQs

Listing pages use `CollectionPage` or `WebSite` schema.

### 6.5 Sitemap & RSS

- `@astrojs/sitemap` generates `/blog/sitemap-index.xml` and `/blog/sitemap-0.xml`.
- Combine with root `sitemap.xml` by updating it to reference the Astro sitemap, OR generate a unified sitemap at build time.
- `/blog/rss.xml` generated via `src/pages/rss.xml.js`.

### 6.6 Internal linking

- Each post links to 2–3 related posts automatically via `tags` overlap.
- Breadcrumbs on every post page.
- Category and author archive links.
- Listing page links from main site nav/footer.

### 6.7 Performance

- Astro static output = no runtime JS for content pages.
- `<Image />` for all post images (WebP/AVIF, responsive sizes).
- Self-host blog fonts or reuse the same Google Fonts import as main site for brand consistency.
- Lazy-load below-fold images.

---

## 7. Design & UX approach

### 7.1 Visual consistency

- Reuse Aagman color tokens: `#06080A` (bg), `#E7EEEA` (text), `#56E8B8` (accent), `#8a938e` (muted).
- Use the same font families already loaded on the main site.
- Keep the blog header minimal: logo → back to home, "Blog" label, CTA back to main site.

### 7.2 Blog listing page (`/blog/`)

- H1: "Āagman Blog — AI Trading for Indian Markets"
- Intro paragraph (2–3 sentences) targeting primary keyword.
- Featured post (larger card).
- Grid of recent posts with title, excerpt, date, category, reading time.
- Category filter links.

### 7.3 Post page (`/blog/:slug/`)

- H1 = post title.
- Author byline with link to author archive.
- Published/updated dates.
- Category + tags.
- Table of contents (auto-generated from H2s).
- Related posts section.
- CTA section linking back to main site waitlist/pricing.

---

## 8. Vercel deployment strategy

### 8.1 Build configuration

Add at repo root:

```json
// vercel.json
{
  "buildCommand": "cd blog && npm install && npm run build",
  "outputDirectory": "blog/dist",
  "installCommand": "cd blog && npm install",
  "rewrites": [
    { "source": "/blog/:path*", "destination": "/blog/:path*" }
  ]
}
```

Astro config:

```js
// blog/astro.config.mjs
import { defineConfig } from 'astro/config';
import sitemap from '@astrojs/sitemap';
import vercel from '@astrojs/vercel/static';

export default defineConfig({
  site: 'https://aagman.ai',
  base: '/blog',
  output: 'static',
  adapter: vercel(),
  integrations: [sitemap()],
  build: {
    format: 'directory',
  },
});
```

### 8.2 Combining with existing static files

Challenge: Vercel expects either a single `outputDirectory` or root static files. Since the existing HTML files live at root and Astro builds into `blog/dist`, we need a build step that copies root static files into `blog/dist` before deployment.

Recommended approach:

```json
// blog/package.json scripts
{
  "build": "astro build && node scripts/copy-root-assets.js"
}
```

```js
// blog/scripts/copy-root-assets.js
import fs from 'fs';
import path from 'path';
import { fileURLToPath } from 'url';

const __dirname = path.dirname(fileURLToPath(import.meta.url));
const root = path.resolve(__dirname, '../..');
const dist = path.resolve(__dirname, '../dist');

const filesToCopy = [
  'index.html',
  'landing-terminal.html',
  'pricing.html',
  'founders-letter.html',
  'terms.html',
  'privacy-policy.html',
  'risk-disclosure.html',
  'cancellation-refunds.html',
  'support.js',
  'robots.txt',
  'sitemap.xml',
  'favicon.ico',
  'favicon-32x32.png',
  'apple-touch-icon.png',
];

for (const file of filesToCopy) {
  const src = path.join(root, file);
  const dest = path.join(dist, file);
  if (fs.existsSync(src)) {
    fs.cpSync(src, dest, { recursive: true });
  }
}

// Copy uploads directory
fs.cpSync(path.join(root, 'uploads'), path.join(dist, 'uploads'), { recursive: true });

console.log('Root assets copied to Astro dist.');
```

Then `vercel.json` points to `blog/dist` as the output directory.

**Alternative:** keep the existing Vercel project for root static files and create a second Vercel project for `/blog` routed via reverse proxy. More complex; not recommended.

---

## 9. Long-term migration path

Phase 1 (now): Astro blog under `/blog`, existing HTML untouched.

Phase 2 (later): Convert `pricing.html` → `/pricing/` Astro route. Then legal pages. Then homepage. Eventually delete the static HTML root files and run the entire site from Astro.

Phase 3 (future): Add server-side features if needed (waitlist API, search, auth) by switching Astro to `output: 'hybrid'` or `server`.

---

## 10. Authoring workflow

### 10.1 Kimi / Claude workflow (primary)

1. User provides topic or brief.
2. Agent researches keywords and AEO questions.
3. Agent creates `blog/src/content/posts/<slug>.mdx` with frontmatter.
4. Agent writes content optimized for target keyword + FAQ schema.
5. Agent runs `npm run build` locally to verify.
6. Agent commits, pushes, deploys via Vercel.

### 10.2 Non-technical writer workflow (future)

Options:
- **Keystatic**: add a visual editor on top of the Markdown files. Writers edit in browser; changes commit to Git.
- **Outstatic**: similar, Next.js-focused but usable with Astro via adapter patterns.
- **Notion as CMS**: writers draft in Notion; build script syncs to Markdown before deploy.

**Recommendation:** start with pure Markdown. Add Keystatic later if a non-technical writer joins.

---

## 11. Risks & mitigations

| Risk | Mitigation |
|------|------------|
| Existing SEO work on root pages gets broken during build copy | Keep root files unchanged; verify canonicals/OG after first deploy. |
| Astro build output conflicts with root static paths | Use `base: '/blog'` so Astro assets are scoped. |
| Blog styling drifts from main site | Document tokens; reuse fonts and colors. |
| Non-technical team cannot publish | Defer CMS UI to Phase 2; start with Markdown. |
| `/blog` URLs need to be updated everywhere when moving to full migration | Keep slug-based URLs stable; only the build output location changes. |
| Sitemap becomes fragmented | Generate unified sitemap or maintain root sitemap + Astro sitemap. |

---

## 12. Implementation checklist

- [ ] Initialize Astro project in `blog/`
- [ ] Configure `astro.config.mjs` with `base: '/blog'`, static output, sitemap
- [ ] Set up content collection schema in `src/content/config.ts`
- [ ] Create `BaseLayout.astro` and `PostLayout.astro`
- [ ] Create `Seo.astro` component (meta + OG + JSON-LD)
- [ ] Build listing page (`src/pages/index.astro`)
- [ ] Build post page (`src/pages/[slug].astro`)
- [ ] Build category and author archive pages
- [ ] Add RSS feed (`src/pages/rss.xml.js`)
- [ ] Create sample MDX post
- [ ] Add root asset copy script
- [ ] Create/update `vercel.json`
- [ ] Update root `robots.txt` to allow `/blog/`
- [ ] Update root `sitemap.xml` to include `/blog/` routes
- [ ] Add "Blog" link to main site nav and footer
- [ ] Deploy and verify
- [ ] Submit new sitemap to Google Search Console

---

## 13. Cost summary

| Item | Cost |
|------|------|
| Astro framework | Free (open source) |
| Hosting on Vercel Hobby | Free (until traffic limits) |
| Domain (`aagman.ai`) | ~$60–$100/year |
| Sitemap/RSS/SEO plugins | Free |
| Keystatic or Outstatic (future) | Free open-source tiers |
| **Total at launch** | **$0/month + domain** |

---

## 14. Decision required

Before implementation, confirm:

1. **Path**: Proceed with `/blog` subpath on the same domain? *(recommended)*
2. **CMS**: Start with pure Markdown files, or integrate a headless CMS from day one?
3. **Design**: Match the existing dark terminal aesthetic, or create a lighter blog theme?
4. **Content**: Provide 1–2 seed blog topics so we can build the first real posts alongside the scaffold.

Once you confirm, I can initialize the Astro app, create the first post, wire the Vercel build, and deploy.
