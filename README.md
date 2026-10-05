# GLM — Global Linkage Money · Landing Page

Next.js 15 (App Router) · React 19 · TypeScript (strict) · Tailwind CSS v4 · react-icons

## Run it

```bash
npm install
npm run dev        # http://localhost:3000
npm run typecheck
npm run lint
npm run build
npx prettier --check "src/**/*.{ts,tsx,css}"
```

## Structure

```
src/
  app/
    layout.tsx            # Inter font, SEO metadata, Open Graph, Twitter
    page.tsx              # Landing page (Header → Hero → Stats → Why GLM → Footer) + JSON-LD
    globals.css           # Design tokens (@theme) + reusable utilities
    [slug]/page.tsx       # Branded "coming soon" page for links not built yet
    (auth)/               # /login, /register, /forgot-password (shared background layout)
    api/auth/login/route.ts  # forwards credentials to AUTH_API_URL
    api/newsletter/route.ts
    robots.ts, sitemap.ts
  components/icons/       # Custom brand icons (hexagon badges, AI chip)
  components/landing/     # Header, MobileMenu, Logo, Hero, HeroArt, HeroFeatures,
                          # HeroActions, VideoModal, StatsSection, WhyChooseGLM,
                          # FeatureCard, Footer, FooterColumn, SocialLinks,
                          # Newsletter, LanguageSelector
  config/site.ts          # Brand, routes, nav — change links in one place
  data/landing.ts         # All section content (stats, features, footer links, socials)
  hooks/useModalBehavior.ts  # scroll lock, Esc, focus trap, focus restore
public/
  assets/hero-artwork.webp
  icon.svg
```

## Adding to an existing project

Copy `src/components/landing`, `src/config`, `src/data`, `src/hooks`, `public/assets`,
`public/icon.svg`, and merge `src/app/globals.css` tokens into your global stylesheet.
Use `src/app/page.tsx` as your home page. If your project already has real pages
at `/login`, `/register`, `/packages`, etc., they automatically win over the
`[slug]` placeholder — remove those slugs from `placeholderPages` in `config/site.ts`.

## Configuration (env)

| Variable | Purpose |
| --- | --- |
| `NEXT_PUBLIC_SITE_URL` | Canonical URL for SEO/Open Graph |
| `NEXT_PUBLIC_INTRO_VIDEO_URL` | mp4/webm URL; enables playback in the "Watch Video" modal |
| `AUTH_API_URL` | GLM auth backend. `/api/auth/login` POSTs `{ email, password, remember }` there and passes its `Set-Cookie` (session) through. 2xx = success → `/dashboard`; 400/401/403/404/422 → generic "Invalid email or password"; 429 → rate-limit message. Without it, login reports sign-in isn't available yet |
| `AUTH_REGISTER_URL` | GLM sign-up backend. `/api/auth/register` re-validates the form, then POSTs `{ fullName, email, phone (E.164), country, password, referralCode?, acceptTerms }`. 2xx → "verify your email" step; 409 → generic "couldn't create an account" message; 400/422 → "review the form"; 429 → rate-limit message. Without it, the form reports registration isn't open yet |
| `WALLET_API_URL` | GLM wallet backend. `/wallet` GETs it server-side, forwarding the user's session cookie, and expects the `WalletOverview` JSON in `src/lib/wallet/types.ts` (money as decimal strings). 401/403 → redirect to `/login`; other failures → error state |
| `WALLET_PREVIEW` | `true` shows clearly-labelled sample wallet data when `WALLET_API_URL` is unset (always on in development). Production without either shows "wallet not available yet" — never invented balances |
| `NEWSLETTER_WEBHOOK_URL` | Where newsletter emails are POSTed (`{ email }`). Without it the form reports sign-up isn't live yet |

## To replace before launch

- `public/assets/hero-artwork.webp` is cut from the design screenshot (upscaled). Drop the
  original high-resolution artwork in with the same name.
- Social profile URLs in `src/data/landing.ts`.
