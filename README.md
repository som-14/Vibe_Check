# Vibe_Check

**An AI-native marketplace connecting generative creators and brands, with verified workflow provenance, model-specific discovery, and structured creative briefs.**

![Next.js](https://img.shields.io/badge/Next.js-14-black)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178c6)
![Supabase](https://img.shields.io/badge/Supabase-Postgres%20%2B%20Auth-3ecf8e)
![License](https://img.shields.io/badge/license-[LICENSE]-blue)

> Built for the Kampus.vc **AI Content Creator Marketplace** challenge.

---

## Table of contents

1. [The problem](#the-problem)
2. [What Vibe_Check does](#what-vibe_check-does)
3. [Feature tour](#feature-tour)
4. [Tech stack](#tech-stack)
5. [Data model](#data-model)
6. [Getting started](#getting-started)
7. [Environment variables](#environment-variables)
8. [Scripts](#scripts)
9. [Project structure](#project-structure)
10. [Security](#security)
11. [Deployment](#deployment)
12. [Team and workflow](#team-and-workflow)
13. [Roadmap](#roadmap)
14. [License](#license)

---

## The problem

Generative AI has created a new class of creative professionals: AI filmmakers, animators and generative content creators. Brands and agencies still have no good way to find them, judge their work or hire them with confidence.

Legacy freelance marketplaces such as Upwork and Fiverr treat creative work as a static deliverable. They do not understand AI-native production, so brands run into three problems:

- **Unverifiable skills.** It is hard to tell who actually controls a pipeline and who just got a lucky generation.
- **Licensing risk.** Commercial-use rights depend on the tools, subscription tiers and models behind each piece of work.
- **Opaque workflows.** Brands cannot see how work was made, so revisions turn into "prompt gambling".

## What Vibe_Check does

Vibe_Check is built around the way AI work is actually produced.

- **Brands** discover creators by exact tools, skills and formats, publish structured briefs, evaluate AI-specific portfolios and manage engagements from first contact to delivery.
- **Creators** present their work together with the tools, models and step-by-step workflow behind it, earn verification signals and respond to briefs that match their skills.

---

## Feature tour

### 1. Creator profiles and AI portfolios

- Rich profiles with specializations, skills, tools and models, availability, rates and turnaround.
- Every portfolio item shows:
  - the **tools and models** used and the role each played in the pipeline
  - a **workflow breakdown** (for example base image, motion control, upscaling, audio)
  - **technical specs**: aspect ratio, resolution, duration
  - a **licensing panel**: commercial use, license type, output ownership, training-data disclosure
- Image, video and audio viewer with correct aspect-ratio handling.

### 2. Structured brief definition

Brands define a brief through a guided builder that captures:

- campaign objective and requirements
- content type and visual style
- **formats and aspect ratios** (9:16, 16:9, 1:1, 4:5, 21:9), resolution, duration, deliverables and revision rounds
- preferred tools and required skills
- **commercial-use requirements**: usage channels, territory, license duration, exclusivity, ownership transfer, AI disclosure
- budget and timeline

### 3. Discovery and filtering

- Search and filter creators by skills, specialization, tools, content type, style, aspect-ratio experience, rate, availability, location, language, commercial-readiness and verification level.
- Relevance ranking with matched-skill highlights ("matches 4 of 5 skills").
- **Useful empty states.** When no creator matches, the app explains which filters caused it, shows how many results removing each one would unlock and offers the closest matches, so users never hit a dead end.
- Shareable URLs for every search.

### 4. Engagement management

Invite or receive proposals, shortlist, hire, collaborate in a shared workspace, upload versioned deliverables, request revisions and approve, all tracked through clear stages from discovery to delivery.

### 5. Experience

- Glass-style interface with dark and light themes, motion and 3D touches where they help.
- Responsive from mobile to desktop, keyboard-accessible, with loading, empty and error states on every screen.

---

## Bonus features

### Creator verification signals

Visible trust indicators that help brands rely on creator claims:

- **Verified tools:** proof of commercial-tier subscriptions
- **Verified workflows:** reproducible pipelines (for example exported node graphs)
- **Verified work:** admin-reviewed evidence for individual portfolio items

Each badge explains what was verified, how and when. Trust is also a search filter.

### AI-assisted brief builder

Brands write a rough idea in plain language, and the builder returns a complete structured brief: content type, style, formats, suggested toolstack, required skills and commercial-use suggestions. Every field stays editable. It also lists its assumptions and asks clarifying questions, and it falls back to a rule-based generator if the AI service is unavailable.

---

## Tech stack

| Layer | Technology |
|---|---|
| Framework | Next.js 14 (App Router), React, TypeScript (strict) |
| Styling and UI | Tailwind CSS, shadcn/ui (restyled), Framer Motion, [GSAP / Three.js if used] |
| Backend | Supabase: PostgreSQL, Auth, Storage, Realtime, Row Level Security |
| Validation | Zod, React Hook Form |
| AI | Google Gemini API (server-side only) |
| Testing | [Vitest / Jest], Playwright |
| CI and hosting | GitHub Actions, Vercel |

---

## Data model

The full note, with an entity-relationship diagram and design rationale, is in [`docs/data-model-note.md`](./docs/data-model-note.md). The core shapes are below.

### Creator profile

```ts
interface CreatorProfile {
  id: string;
  handle: string;
  displayName: string;
  headline: string;
  bio: string;
  location: string;
  languages: string[];
  availability: "available" | "busy" | "booked";
  rateRange: { min: number; max: number; currency: string };
  turnaroundDays: number;
  specializations: string[];            // e.g. "AI film", "product ads"
  skills: { name: string; level: "beginner" | "intermediate" | "expert" }[];
  tools: {
    name: string;                        // e.g. "Runway", "ComfyUI", "FLUX"
    category: "Image" | "Video" | "Audio" | "Animation" | "3D" | "Upscaling" | "Editing";
    yearsUsed: number;
    verified: boolean;
  }[];
  openToCommercialWork: boolean;
  level: "Rising" | "Pro" | "Elite";
  portfolio: PortfolioItem[];
}
```

### Portfolio item

```ts
interface PortfolioItem {
  id: string;
  title: string;
  description: string;
  mediaType: "video" | "image" | "animation" | "audio" | "mixed";
  mediaUrl: string;
  aspectRatio: "16:9" | "9:16" | "1:1" | "4:5" | "21:9";
  resolution: string;
  durationSeconds?: number;
  contentType: string;
  styleTags: string[];
  toolsUsed: { tool: string; roleInPipeline: string }[];
  workflow: { step: number; title: string; tool: string; notes: string }[];
  licensing: {
    commercialUseAllowed: boolean;
    licenseType: string;
    outputOwnership: string;
    trainingDataDisclosure: string;
  };
  verification: { tools: boolean; workflow: boolean; work: boolean };
}
```

### Brief

```ts
interface Brief {
  id: string;
  title: string;
  objective: string;
  campaignRequirements: string;
  contentType: string;
  styleTags: string[];
  formats: string[];                     // deliverable file formats
  aspectRatios: string[];
  resolution: string;
  durationSeconds?: number;
  deliverablesCount: number;
  revisionRounds: number;
  preferredTools: string[];
  requiredSkills: string[];
  minimumVerification: "none" | "tools" | "workflow" | "full";
  commercialUse: {
    usageChannels: string[];             // organic social, paid ads, TV, web, ...
    territory: string;
    licenseDuration: string;
    exclusivity: boolean;
    ownershipTransfer: boolean;
    aiDisclosureRequired: boolean;
    paidMediaAllowed: boolean;
  };
  budget: { min: number; max: number; currency: string };
  deadline: string;
  status: "draft" | "open" | "in_review" | "closed";
}
```

---

## Getting started

### Prerequisites

- Node.js 20 or later and npm
- A [Supabase](https://supabase.com) project (the free tier works), or the [Supabase CLI](https://supabase.com/docs/guides/cli) for a local instance
- A Google Gemini API key (optional, for the AI brief builder)

### 1. Clone and install

```bash
git clone https://github.com/som-14/Vibe_Check.git
cd Vibe_Check
npm install
```

### 2. Configure environment variables

```bash
cp .env.example .env.local
```

Fill in the values described in [Environment variables](#environment-variables). Never commit `.env.local`.

### 3. Set up the database

Using a hosted Supabase project:

```bash
npx supabase login
npx supabase link --project-ref [YOUR-PROJECT-REF]
npx supabase db push
```

Or run locally:

```bash
npx supabase start
npx supabase db reset
```

### 4. Seed demo data (optional)

```bash
npm run seed:demo
```

This creates demo creators, portfolio items, brands, briefs and engagements, all clearly marked as demo data, and prints the demo account logins in your terminal.

### 5. Run the app

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000). Signed-out visitors land on the login page.

---

## Environment variables

| Variable | Scope | Description |
|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Public | Supabase project URL |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Public | Supabase anon key (protected by RLS) |
| `NEXT_PUBLIC_SITE_URL` | Public | Base URL of the app, used for auth redirects |
| `NEXT_PUBLIC_DEMO_MODE` | Public | `true` shows one-click demo logins |
| `SUPABASE_SERVICE_ROLE_KEY` | **Server only** | Admin key. Never expose to the client |
| `GEMINI_API_KEY` | **Server only** | Powers the AI brief builder |
| `[UPSTASH_REDIS_REST_URL]` | **Server only** | Rate limiting (optional) |
| `[UPSTASH_REDIS_REST_TOKEN]` | **Server only** | Rate limiting (optional) |

`.env.example` lists every variable with comments. Server-only secrets must never use the `NEXT_PUBLIC_` prefix.

---

## Scripts

| Command | What it does |
|---|---|
| `npm run dev` | Start the development server |
| `npm run build` | Create a production build |
| `npm run start` | Run the production build locally |
| `npm run lint` | Run ESLint |
| `npm run typecheck` | Run the TypeScript compiler without emitting |
| `npm run test` | Run unit and integration tests |
| `npm run test:e2e` | Run Playwright end-to-end tests |
| `npm run seed:demo` | Seed demo data |
| `npm run seed:wipe-demo` | Remove demo data only |
| `npm run assets:generate` | Generate avatars, covers and thumbnails |

> Adjust this table to match the scripts in `package.json`.

---

## Project structure

```text
.
├── src/
│   ├── app/                 # Routes (App Router): auth, creators, briefs, dashboards, admin
│   ├── components/          # UI components and the glass design system
│   ├── features/            # Feature modules: creators, portfolio, briefs, search, engagements
│   ├── lib/                 # Supabase clients, auth and permission helpers, validation schemas
│   └── styles/              # Design tokens and global styles
├── supabase/
│   └── migrations/          # Numbered SQL migrations (schema, RLS, search functions)
├── scripts/                 # Seed and asset generation scripts
├── docs/                    # Architecture, data model note, security notes, team docs
├── tests/                   # Unit, integration and Playwright tests
└── .github/                 # CI workflow, issue and PR templates, CODEOWNERS
```

> Adjust to match the actual folders.

---

## Security

- **Authentication:** email and password, Google OAuth and magic links, with email verification before sensitive actions and HTTP-only secure session cookies.
- **Authorization in three layers:** route middleware by role, permission checks in every server action, and Row Level Security on every database table.
- **No privilege escalation:** users cannot change their own role, verification status or trust score. Verification is granted only by admins and logged in an audit table.
- **Input safety:** Zod validation on client and server, sanitized rich text, and parameterized queries.
- **Uploads:** type and size checks, randomized file names and private signed URLs for deliverables.
- **Abuse protection:** rate limiting on login, search, messaging, uploads and the AI endpoint.
- **Headers:** Content-Security-Policy, HSTS and other protective headers.
- **Secrets:** server-only keys, environment validation at boot, and secret scanning in the repository.

See [`docs/security.md`](./docs/security.md) for the threat model and permission matrix.

---

## Deployment

The app is deployed on **Vercel** with **Supabase** as the production backend.

1. Push the repository to GitHub and import it in Vercel (framework preset: Next.js).
2. Add all environment variables in Vercel under Settings, Environment Variables.
3. In Supabase, open Authentication, URL Configuration and set the Site URL and Redirect URLs to your Vercel domain.
4. Redeploy. Every push to `main` deploys automatically.

> The free Vercel Hobby plan is for personal, non-commercial use only. A commercial launch needs a paid plan.

---

## Team and workflow

| Member | Workstream |
|---|---|
| [@som-14](https://github.com/som-14) | [WORKSTREAM] |
| [@archidwivedi1-png](https://github.com/archidwivedi1-png) | [WORKSTREAM] |
| [@ysharma-png](https://github.com/ysharma-png) | [WORKSTREAM] |
| [@varsha-patel2](https://github.com/varsha-patel2) | [WORKSTREAM] |

We work through issues, short-lived feature branches and pull requests. `main` is protected: every change needs a passing CI run and a review from another member. See [`CONTRIBUTING.md`](./CONTRIBUTING.md) and [`docs/team/WORKFLOW.md`](./docs/team/WORKFLOW.md).

---

## Roadmap

- Payments with escrow and milestone releases
- Creator collaboration and team profiles
- Weekly creative challenges and a workflow library
- Personalized feed and smart match alerts
- Consent-based onboarding of real creators with claimable profiles
- Mobile app and push notifications

---

## License

Released under the [LICENSE] license. See [`LICENSE`](./LICENSE) for details.
