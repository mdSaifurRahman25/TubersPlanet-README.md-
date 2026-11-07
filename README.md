# TubersPlanet Website

The public marketing website for **TubersPlanet**, an AI-powered thumbnail generator
for YouTube and TikTok.

This repository contains the marketing site, SEO landing pages, example galleries,
pricing and policy pages, contact form, newsletter signup, and marketing
analytics. The generator, accounts, billing, and dashboard live in a separate
application.

- Website: [tubersplanet.com](https://tubersplanet.com)
- Product app: [app.tubersplanet.com](https://app.tubersplanet.com)

> 🔒 **Source code:** Private (contains proprietary business logic).
> Available for review on request, feel free to reach out on [LinkedIn](https://www.linkedin.com/in/md-saifur-rahman25/).

## Overview

The site is built with the Next.js App Router and is optimized for fast, animated,
accessible marketing pages. Product calls to action link to the external app through
a shared URL helper, so the website can run independently from the generator.

Key areas:

- Home page with hero, before/after examples, generator prompt form, features,
  how-it-works, platforms, use cases, testimonials, pricing, FAQ, and CTA sections.
- SEO landing pages for the TikTok thumbnail maker and YouTube thumbnail examples.
- Common pages: About, Pricing, How It Works, FAQs, Contact, Privacy Policy,
  Terms & Conditions, and Refund Policy.
- Marketing attribution through web GTM, first-party campaign cookies, and a
  self-hosted server-side GTM (sGTM) stack.
- Contact inquiries delivered through Resend.
- Newsletter subscriptions stored in MongoDB, with an optional Resend welcome email.
- Database health endpoint for deployment monitoring.

## Tech Stack

| Area | Technology |
| --- | --- |
| Framework | Next.js 16 (App Router) |
| UI | React 19, Tailwind CSS 4 |
| Language | TypeScript (strict) |
| Animation | Motion (`motion/react` + `motion/react-client` RSC entrypoint), Lenis smooth scroll |
| Icons | Lucide React, React Icons |
| Validation | Zod |
| Database | MongoDB via Mongoose |
| Email | Resend HTTP API; Nodemailer helper for future use |
| Analytics | Google Tag Manager (web + self-hosted server-side GTM) |
| Notifications | react-hot-toast |
| Linting | ESLint 9 + `eslint-config-next` |
| Package manager | npm |

## Requirements

- Node.js `>=20.9.0`
- npm
- MongoDB connection string for newsletter and database health features
- Resend account and verified sender domain for contact emails
- Optional GTM web container and sGTM deployment for analytics (tracking is
  disabled when `NEXT_PUBLIC_GTM_ID` is not set)
- The separate product app running locally if you want to test login, registration,
  generation, or billing links end to end

## Getting Started

```bash
git clone git@github.com:saifur25/AI_Thumbnail.git
cd tubersplanet-website
npm install
```

Create a `.env.local` file in the project root. The site can boot without every
integration configured, but contact and newsletter features need their respective
variables.

At minimum for local browsing:

```env
NEXT_PUBLIC_SITE_URL=http://localhost:3000
NEXT_PUBLIC_APP_URL=http://localhost:3001
```

Start the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Environment Variables

Environment files are ignored by git. Never commit `.env.local` or production
secrets.

### Public configuration

These values are exposed to the browser and must not contain secrets.

| Variable | Required | Description |
| --- | --- | --- |
| `NEXT_PUBLIC_SITE_URL` | Optional | Canonical base URL for metadata and links. Defaults to `http://localhost:3000`. |
| `NEXT_PUBLIC_APP_URL` | Optional | Product app base URL. Defaults to `http://localhost:3001` in development and `https://app.tubersplanet.com` in production. |
| `NEXT_PUBLIC_GTM_ID` | Optional | Web GTM container ID (`GTM-...`). Tracking scripts are skipped when this is empty or malformed. |
| `NEXT_PUBLIC_GTM_SERVER_URL` | Optional | Server-side GTM endpoint used as the `transport_url` on events. Defaults to `https://sgtm.tubersplanet.com`. |
| `NEXT_PUBLIC_USD_TO_BDT_RATE` | Optional | Display-only USD → BDT rate for the pricing sections. Defaults to `125`, matching the product app's `USD_TO_BDT_RATE`. |
| `NEXT_PUBLIC_SUPPORT_EMAIL` | Optional | Support email shown in the footer and used as a contact recipient fallback. Footer default: `tubersplanet@gmail.com`. |
| `NEXT_PUBLIC_WHATSAPP_NUMBER` | Optional | WhatsApp number. If omitted, the footer hides the link and the contact page directs users to the form. |
| `NEXT_PUBLIC_YOUTUBE_URL` | Optional | Footer YouTube link. |
| `NEXT_PUBLIC_TWITTER_URL` | Optional | Footer X/Twitter link. |
| `NEXT_PUBLIC_LINKEDIN_URL` | Optional | Footer LinkedIn link. |
| `NEXT_PUBLIC_REDDIT_URL` | Optional | Footer Reddit link. |

### Server-only configuration

| Variable | Required | Description |
| --- | --- | --- |
| `MONGODB_URI` | For newsletter and DB health | MongoDB connection string. |
| `RESEND_API_KEY` | For contact and welcome emails | Resend API key. |
| `CONTACT_TO_EMAIL` | For contact | Inquiry recipient. Falls back to `NEXT_PUBLIC_SUPPORT_EMAIL`. |
| `CONTACT_FROM_EMAIL` | For contact | Verified Resend sender address. |
| `NEWSLETTER_FROM_EMAIL` | Optional | Welcome email sender. Without it, subscriptions still save but no welcome email is sent. |
| `SMTP_HOST` | Reserved | For `src/lib/email/mailer.ts`; not used by current routes. |
| `SMTP_PORT` | Reserved | SMTP port; defaults to `587` when the mailer is used. |
| `SMTP_SECURE` | Reserved | `true`/`false`; defaults to `true` for port 465. |
| `SMTP_USER` | Reserved | SMTP username. |
| `SMTP_PASS` | Reserved | SMTP password. |
| `SMTP_FROM` | Reserved | Default sender; falls back to `TubersPlanet <SMTP_USER>`. |
| `SMTP_REPLY_TO` | Reserved | Optional reply-to address. |

Server-side GTM has its own deployment configuration in `sgtm/.env`, separate
from the Next.js environment. Start from `sgtm/.env.example` and set
`CONTAINER_CONFIG` plus optional preview values. The `.env` file is git-ignored.

## Available Scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Start the development server. |
| `npm run build` | Create a production build. |
| `npm run start` | Serve the production build. |
| `npm run lint` | Run ESLint. |
| `npx tsc --noEmit` | Run a strict TypeScript check. |

There is currently no automated test suite or formatter configuration.

## Project Structure

```text
src/
  app/
    layout.tsx                 Root layout and global providers
    page.tsx                   Home page composition
    globals.css                Tailwind import and global theme
    providers.tsx              Lenis, MotionConfig, and toast providers
    (commonpages)/             About, Contact, FAQs, How It Works, Pricing, legal
    (otherservicepages)/       TikTok maker, example galleries
    api/
      contact/                 Contact form endpoint
      newsletter/              Newsletter subscription endpoint
      health/database/         MongoDB health check
  components/
    homepage/                  Home page sections and generator form island
    pricing/                   USD/BDT currency state, toggle, credit cards, plan guide
    web-layout/                Navbar shell, navigation islands, and Footer
    contact/                   Contact form
    ui/                        Shared UI primitives and backdrop
    providers/                 Client providers
    testimonial/               Testimonial card and marquee island
    tracking/                  GTM scripts, persistence, link enhancer, tracked app links
    ReferralCapture.tsx        Affiliate referral cookie capture
  lib/
    app-url.ts                 App URL + tracking handoff helpers
    pricing.ts                 Credit pack data and USD/BDT formatting
    tracking/                  GTM dataLayer helpers and tracking cookies
    db/connect-db.ts           Cached Mongoose connection
    email/mailer.ts            Nodemailer helper (future use)
    validations/               Zod schemas
  types/                       Shared TypeScript types
public/
  assets/                      Site imagery
  logo.svg favicon.svg         Brand assets
  robots.txt sitemap.xml       Crawling and sitemap files
sgtm/                          Self-hosted server-side GTM (Docker + Nginx)
  README.md docker-compose.yml nginx-sgtm.conf .env.example
```

The `@/*` import alias resolves to `src/*`.

## Routes

### Pages

| Route | Description |
| --- | --- |
| `/` | Home page |
| `/about` | About TubersPlanet |
| `/pricing` | Pricing and credit packs |
| `/how-it-works` | Product walkthrough |
| `/faqs` | Frequently asked questions |
| `/contact` | Contact page with working inquiry form |
| `/privacy-policy` | Privacy policy |
| `/terms-and-conditions` | Terms and conditions |
| `/refund-policy` | Refund policy |
| `/tiktok-thumbnail-maker` | TikTok thumbnail landing page |
| `/youtube-thumbnail-examples` | YouTube thumbnail example gallery |
| `/tiktok-thumbnail-examples` | Placeholder page |

### API

| Endpoint | Method | Description |
| --- | --- | --- |
| `/api/contact` | `POST` | Validate a contact inquiry, apply rate limiting and spam checks, then deliver it through Resend. |
| `/api/newsletter` | `POST` | Validate and upsert a newsletter subscriber in MongoDB; optionally send a welcome email. |
| `/api/health/database` | `GET` | Report whether MongoDB is reachable. |

Both form endpoints accept JSON or `FormData`. Rate limits are in-memory and limited
to 5 requests per IP per 10-minute window per server instance. They also use a
hidden `company` honeypot field and return a fake success response for bot
submissions.

## How Product Links Work

`src/lib/app-url.ts` builds links to the separate product application:

- Development default: `http://localhost:3001`
- Production default: `https://app.tubersplanet.com`
- Override with `NEXT_PUBLIC_APP_URL`

Login, registration, buy-credit, and generation CTAs use `getAppUrl()` or
`getAppUrlWithContext()`. The context-aware helper forwards whitelisted
attribution parameters from the current URL: `ref`, all `utm_*` keys, `fbclid`,
`gclid`, `msclkid`, `ttclid`, `prompt`, and `pack`. Marketing CTAs point to the
app's `/register` page; update `NEXT_PUBLIC_APP_URL` rather than editing
individual links.

Campaign context is also stored in first-party cookies on `.tubersplanet.com`
(`tp_ref`, `tp_utm_*`, click IDs, and `tp_prompt`). `TrackingLinkEnhancer`
appends saved context to app-bound links at click time as a fallback, so
affiliate attribution and the user's draft prompt survive the website → app
handoff even when a link is built without context.

## Analytics and Tracking

The site loads web Google Tag Manager and can forward events to a self-hosted
server-side GTM container at `sgtm.tubersplanet.com`, which relays them to Meta
Conversions API and Google Ads (and can be extended to TikTok Events API).

- `GtmScripts` loads the web container when `NEXT_PUBLIC_GTM_ID` is a valid
  `GTM-...` ID, with a `<noscript>` fallback.
- `TrackingPersistence` saves UTM parameters and ad click IDs, `ReferralCapture`
  saves `?ref=` in `tp_ref`, and `GeneratorForm` saves the draft prompt in
  `tp_prompt` for one hour. Cookies are scoped to `.tubersplanet.com` in
  production so the product app can read them.
- Events pushed to `dataLayer`: `cta_click` for mobile menu and pricing CTAs,
  `generate_click` when the homepage prompt form is submitted, and
  `contact_submit` after a successful contact form submission. Each event
  includes `event_id` and `transport_url`; the product app is responsible for
  `purchase`.
- The generator form waits up to about 2.5 seconds for GTM to finish before
  navigating to `/register`, so the conversion event is not lost.
- `getAppUrlWithContext()` and `TrackingLinkEnhancer` keep `ref`, UTM, click IDs,
  and `prompt` attached across the website → app handoff.

Analytics is optional. Without `NEXT_PUBLIC_GTM_ID`, no GTM script loads and no
tags fire. Server-side GTM deployment, GTM container tags, DNS, Docker, and
Nginx/TLS setup are documented in [`sgtm/README.md`](sgtm/README.md).

Do not send raw names, email addresses, passwords, uploaded images, or payment
details to `dataLayer`. The privacy policy (`/privacy-policy`) discloses Google
Analytics, Google Ads, and Meta remarketing, including hashed email/account
identifiers for sign-up and purchase matching. Keep it aligned with the tags
actually deployed.

## Design and Development Notes

- The site is dark-first with pink/purple accents. Reuse the existing Tailwind
  utility patterns and shared components.
- Pages and most sections are Server Components. Interactive islands declare
  `"use client"` at the smallest practical level, so copy, links, prices, FAQs, and
  footer content stay in the server-rendered HTML.
- Motion is used through both entrypoints: `motion/react-client` in Server
  Components and `motion/react` inside client islands. `MotionConfig` respects
  `prefers-reduced-motion`, and Lenis smooth scrolling also honors it.
- Images are rendered with `next/image`. Remote images from Unsplash and Cloudinary
  are allowed in `next.config.ts`.
- Forms validate with Zod and share field constraints between the client and API.
- Analytics events go through `src/lib/tracking/gtm.ts`; do not call
  `window.dataLayer.push()` directly, and avoid raw PII in event payloads.
- Update the privacy policy and its `lastUpdated` date when analytics,
  remarketing, or tracking cookies change.
- New pages should include page metadata, canonical URLs, Open Graph, Twitter, and
  robots directives where appropriate.
- `public/robots.txt` and `public/sitemap.xml` must be reviewed before launch. The
  current robots file disallows crawling, and the sitemap contains routes that are
  not implemented yet.

## Deployment

The project is a standard Next.js application and can be deployed to any host that
supports Node.js `>=20.9.0` or to a Next.js-compatible platform.

Before deploying:

1. Set all required environment variables in the hosting provider.
2. Set `NEXT_PUBLIC_SITE_URL` to the production website URL.
3. Set `NEXT_PUBLIC_APP_URL` to the production product app URL.
4. Set `NEXT_PUBLIC_GTM_ID` and, when using server-side GTM, set
   `NEXT_PUBLIC_GTM_SERVER_URL`.
5. Deploy the sGTM stack from `sgtm/` (server container, `sgtm/.env`, Docker,
   Nginx, TLS) and confirm events in GTM Preview.
6. Confirm MongoDB network access and the Resend sender domain.
7. Run `npm run build` to verify the production build.
8. Review `robots.txt` and `sitemap.xml` before enabling search indexing.

## Current Limitations

- Pricing cards hydrate because the USD/BDT toggle uses React context. The prices
  and copy still appear in the server HTML, but the subtree carries client JS.
- Some FAQ links point to routes that have not been built yet:
  `/youtube-thumbnail-maker` and `/fortnite-thumbnail-maker`.
- `/tiktok-thumbnail-examples` is a placeholder.
- `src/lib/email/mailer.ts` and the auth/thumbnail validation schemas are prepared
  for future features but are not currently used by website routes.
- Newsletter and contact rate limiting is process-local and is not shared across
  serverless instances.
- GTM is disabled unless `NEXT_PUBLIC_GTM_ID` is configured with a valid
  container ID.
- The `sgtm/` deployment is manual infrastructure: it needs a GTM server
  container, `CONTAINER_CONFIG`, DNS, Docker, and Nginx/TLS.
- No cookie-consent banner or Consent Mode initialization is implemented yet.
  The `CookieConsentState` type exists but is not connected to the GTM loader.

## License

This repository is private and proprietary. All rights reserved.
