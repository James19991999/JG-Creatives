# JG Creative Tech — Production Website v4

**Next.js 14.2** · TypeScript · TailwindCSS · Material Symbols · Claude AI · M-Pesa Daraja

Build: ✅ 15/15 routes compiled · 0 TypeScript errors · 0 type errors

---

## Quick Start

```bash
npm install
cp .env.example .env.local   # fill in your keys
npm run dev                   # http://localhost:3000
npm run build && npm start    # production
```

---

## All Routes

| Route                    | Type    | Description                                              |
|--------------------------|---------|----------------------------------------------------------|
| `/`                      | Static  | Home — full-bleed video hero + all sections              |
| `/about`                 | Static  | Story, values, animated stats                            |
| `/solutions`             | Static  | 6 service cards + 5-step process timeline                |
| `/portfolio`             | Static  | 6 project cards                                          |
| `/contact`               | Static  | Validated contact form + FAQ sidebar                     |
| `/portal`                | Static  | Client portal — 5 interactive sections + M-Pesa payments |
| `/api/chat`              | Dynamic | Claude AI streaming chat assistant                       |
| `/api/contact`           | Dynamic | Contact form — rate-limited, validated, Resend email     |
| `/api/mpesa`             | Dynamic | M-Pesa STK push initiator                                |
| `/api/mpesa/callback`    | Dynamic | Safaricom payment confirmation webhook                   |
| `/api/mpesa/query`       | Dynamic | Poll STK push payment status                             |
| `/sitemap.xml`           | Static  | Auto-generated sitemap                                   |
| `/robots.txt`            | Static  | Auto-generated (portal + API excluded)                   |

---

## Project Structure — 50 files

```
src/
├── app/
│   ├── layout.tsx                 Root layout · SEO metadata · JSON-LD schemas
│   ├── globals.css                Tailwind · Dark/light CSS tokens · Fonts
│   ├── page.tsx                   Home
│   ├── about/page.tsx
│   ├── solutions/page.tsx
│   ├── portfolio/page.tsx
│   ├── contact/page.tsx
│   ├── portal/page.tsx            ★ Client portal with M-Pesa payments
│   ├── not-found.tsx              404 page
│   ├── sitemap.ts
│   ├── robots.ts
│   └── api/
│       ├── chat/route.ts          ★ Claude AI streaming chat
│       ├── contact/route.ts       Contact form handler
│       ├── mpesa/route.ts         ★ M-Pesa STK push
│       ├── mpesa/callback/route.ts Safaricom webhook receiver
│       └── mpesa/query/route.ts   Payment status polling
│
├── components/
│   ├── layout/
│   │   ├── TopBar.tsx             Scroll-aware nav + dark mode toggle + mobile drawer
│   │   ├── Footer.tsx
│   │   └── PageWrapper.tsx
│   ├── sections/
│   │   ├── HeroSection.tsx        ★ Full-bleed video · IntersectionObserver · reduced-motion
│   │   ├── AboutStrip.tsx         Scroll-reveal + animated stats
│   │   ├── ServicesGrid.tsx       Staggered card reveals
│   │   ├── TestimonialsSection.tsx Staggered testimonial reveals
│   │   └── CTABanner.tsx
│   └── ui/
│       ├── Button.tsx             Polymorphic button/link
│       ├── FeatureCard.tsx        light / dark / tonal variants
│       ├── SectionLabel.tsx       Orange kick-line label
│       ├── ContactForm.tsx        Validated form
│       ├── ThemeProvider.tsx      ★ Dark mode · CSS vars · no-flash script
│       ├── ThemeToggle.tsx        Sun/moon toggle button
│       ├── RevealSection.tsx      ★ Scroll-triggered fade/slide reveals
│       ├── AnimatedStats.tsx      ★ Count-up numbers on scroll
│       └── MpesaPaymentModal.tsx  ★ Full STK push flow with countdown + polling
│
├── lib/
│   ├── constants.ts               SITE config · NAV_LINKS · STATS
│   ├── utils.ts                   cn() class merge
│   ├── hooks.ts                   ★ useScrollReveal · useCountUp
│   ├── chat-knowledge.ts          ★ Claude AI knowledge base (update this file)
│   └── mpesa.ts                   ★ M-Pesa Daraja SDK (token · STK push · callback parser)
│
└── types/index.ts                 Shared TypeScript interfaces

public/
├── manifest.json                  ★ PWA manifest — installable on any device
├── videos/hero-video.mp4          Your hero video
├── images/                        Add og-cover.jpg + project images
└── icons/                         Add favicon-32x32.png · apple-touch-icon.png · icon-192x192.png
```

---

## Environment Variables

```env
# ── Email (https://resend.com — free: 100/day) ────────────────
RESEND_API_KEY=re_xxxxxxxxxxxxxxxxxxxx
CONTACT_TO_EMAIL=jamesmaruti560@gmail.com

# ── AI Chat (https://console.anthropic.com) ───────────────────
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxxxxxxxxx

# ── M-Pesa Daraja (https://developer.safaricom.co.ke) ─────────
MPESA_ENV=sandbox                  # sandbox | production
MPESA_CONSUMER_KEY=xxx
MPESA_CONSUMER_SECRET=xxx
MPESA_SHORTCODE=174379             # Sandbox default paybill
MPESA_PASSKEY=bfb279f9aa9bdbcf158e97dd71a467cd2e0c893059b10f78e6b72ada1ed2c919
MPESA_CALLBACK_URL=https://jgcreativetech.co.ke/api/mpesa/callback

# ── Site ──────────────────────────────────────────────────────
NEXT_PUBLIC_SITE_URL=https://jgcreativetech.co.ke
```

---

## Feature Guide

### ★ AI Chat Assistant
The floating chat button (bottom-right) opens a streaming Claude-powered assistant.
It knows everything in `src/lib/chat-knowledge.ts` — update that file whenever your
services, pricing, or projects change. Requires `ANTHROPIC_API_KEY`.

**To customise:** Edit `src/lib/chat-knowledge.ts` — all knowledge is in one place.

### ★ Dark Mode
System-aware toggle in the TopBar (desktop) and mobile drawer. Reads
`prefers-color-scheme` on first visit, remembers choice in `localStorage`.
Zero flicker — a synchronous script runs before React hydrates.

All 30+ design tokens are CSS custom properties in `globals.css`:
- `:root { }` — light mode
- `.dark { }` — dark mode

### ★ Scroll Animations
Two reusable systems:
- `<RevealSection delay={200} direction="up">` — wraps any element with scroll-triggered reveal
- `<AnimatedStats stats={[...]} />` — count-up numbers when scrolled into view
Both respect `prefers-reduced-motion`.

### ★ M-Pesa Payments (Client Portal)
1. Client clicks **M-Pesa** button on a Due invoice
2. `MpesaPaymentModal` opens — they enter their phone number
3. `POST /api/mpesa` calls Safaricom STK push → client receives push on phone
4. UI shows animated countdown while polling `GET /api/mpesa/query` every 3s
5. On success → invoice marked Paid, receipt shown
6. Safaricom sends confirmation to `POST /api/mpesa/callback`

**To wire to your database:** Add Supabase/Firebase update logic in
`src/app/api/mpesa/callback/route.ts` (marked with TODO comments).

**Sandbox test numbers:** Use `0711111111` with PIN `1234` on sandbox.

### ★ PWA (Progressive Web App)
`public/manifest.json` makes the site installable. Users can add it to their
home screen. Add icon files to `public/icons/`:
- `icon-192x192.png`
- `icon-512x512.png`
- `apple-touch-icon.png` (180×180)

### ★ Performance Optimisations
- Hero video: `preload="metadata"` + IntersectionObserver (only plays in viewport)
- Video pauses automatically when `prefers-reduced-motion: reduce` is set
- All images: AVIF/WebP formats, `minimumCacheTTL: 31536000`
- Security headers on all routes (HSTS, X-Frame-Options, Referrer-Policy, Permissions-Policy)
- Static asset immutable caching (1 year for fonts, images, icons)
- `aria-pressed` on video controls, `dl` for stats, semantic HTML throughout

### Enabling Critical CSS (Lighthouse +5 points)
```bash
npm install critters
# Then uncomment in next.config.js:
# experimental: { optimizeCss: true }
```

---

## Deployment

```bash
# Vercel (recommended)
npm i -g vercel
vercel --prod
```

Set all env vars in Vercel dashboard → Project → Settings → Environment Variables.

**M-Pesa production checklist:**
1. Switch `MPESA_ENV=production`
2. Replace sandbox shortcode/passkey with live credentials from Daraja
3. Set `MPESA_CALLBACK_URL` to your live HTTPS domain
4. Test with a real KES 1 payment before going live

---

## Contact

**James Maruti** · Founder, JG Creative Tech
📧 jamesmaruti560@gmail.com
🌐 james-maruti.vercel.app · jg-creative-tech.vercel.app
🐙 github.com/James19991999

---

## v5 Additions

### ★ Feature 6 — Swahili / Multilingual (next-intl v4)

Full English + Kiswahili support across the entire site.

| File | Purpose |
|---|---|
| `src/i18n.ts` | next-intl v4 config — locale detection, message loading |
| `src/messages/en.json` | Complete English translations (all nav, hero, sections, chat, portal) |
| `src/messages/sw.json` | Complete Kiswahili translations — 100% coverage |
| `src/components/ui/LanguageSwitcher.tsx` | Dropdown toggle in TopBar (desktop + mobile) |
| `src/components/ui/IntlProvider.tsx` | Client-side provider — detects locale from URL path |
| `middleware.ts` | next-intl middleware — routes `/sw/...` for Swahili |

**How it works:**
- English: `jgcreativetech.co.ke/` (default, no prefix)
- Swahili: `jgcreativetech.co.ke/sw/`
- Language toggle in the TopBar auto-detects and switches
- `prefers-language` HTTP header used for first-visit auto-detection
- Translations live in `src/messages/` — update once, applies everywhere

**To add a new language:** Add `"fr"` to `locales` in `src/i18n.ts`, create `src/messages/fr.json`, add to `localeNames` and `localeFlags`.

### ★ Feature 7 — Editorial Blog (`/blog`)

A fully static MDX-powered blog — zero runtime, instant load, perfect SEO.

| File | Purpose |
|---|---|
| `content/blog/*.mdx` | Blog posts — add new `.mdx` files here |
| `src/lib/blog.ts` | MDX/frontmatter parser — `getAllPosts()`, `getPostBySlug()`, `getRelatedPosts()` |
| `src/app/blog/page.tsx` | Blog listing — featured post hero + card grid + newsletter CTA |
| `src/app/blog/[slug]/page.tsx` | Individual post — full article with prose styles, tags, share buttons, related posts |

**Three seed articles included:**
1. *Why Kenyan SMEs Keep Outgrowing Their Websites in 12 Months* — Infrastructure
2. *Editorial Design Is the Fastest Way to Look Like a Global Brand* — Design
3. *Integrating M-Pesa Into Your Next.js Application: A Complete Production Guide* — Engineering

**To publish a new article:** Create `content/blog/your-slug.mdx` with this frontmatter:

```mdx
---
title: "Your Article Title"
excerpt: "One paragraph summary shown in the listing and as meta description."
date: "2024-12-01"
author: "James Maruti"
authorTitle: "Founder & Lead Engineer, JG Creative Tech"
category: "Engineering"
tags: ["tag1", "tag2", "kenya"]
coverImage: "/images/og-cover.jpg"
locale: "en"
---

Your content here in MDX/Markdown format...
```

Each article gets:
- Automatic reading time estimate
- Article JSON-LD structured data (Google rich results)
- Open Graph + Twitter Card meta tags
- Related posts by matching tags
- Share buttons (Twitter/X + LinkedIn)
- Syntax-highlighted code blocks
- Full prose typography system

**Routes:** `/blog` (listing) · `/blog/[slug]` (individual post, statically generated)
