# ⚡ Vibe_Check

> **An AI-native marketplace connecting generative creators and brands, with verified workflow provenance, model-specific discovery, and structured creative briefs.**

![Next.js](https://img.shields.io/badge/Next.js-14-black?style=flat-square&logo=next.js)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178c6?style=flat-square&logo=typescript)
![Supabase](https://img.shields.io/badge/Supabase-Postgres%20%2B%20Auth-3ecf8e?style=flat-square&logo=supabase)
![License](https://img.shields.io/badge/license-MIT-blue?style=flat-square)

🏆 *Built for the Kampus.vc **AI Content Creator Marketplace** challenge.*

---

## 📑 Table of Contents

1. [🚨 The Problem](#-the-problem)
2. [💡 What Vibe_Check Does](#-what-vibe_check-does)
3. [✨ Feature Tour](#-feature-tour)
4. [💎 Bonus Features](#-bonus-features)
5. [🛠️ Tech Stack](#️-tech-stack)
6. [🗄️ Data Model](#️-data-model)
7. [🚀 Getting Started](#-getting-started)
8. [🔐 Environment Variables](#-environment-variables)
9. [📜 Scripts](#-scripts)
10. [📂 Project Structure](#-project-structure)
11. [🛡️ Security](#️-security)
12. [🌐 Deployment](#-deployment)
13. [👥 Team and Workflow](#-team-and-workflow)
14. [🗺️ Roadmap](#️-roadmap)
15. [📄 License](#-license)

---

## 🚨 The Problem

Generative AI has created a new class of creative professionals: AI filmmakers, animators, and generative content creators. Brands and agencies still have no good way to find them, judge their work, or hire them with confidence.

Legacy freelance marketplaces such as Upwork and Fiverr treat creative work as a static deliverable. They do not understand AI-native production, so brands run into three major roadblocks:

* ❌ **Unverifiable skills:** It is hard to tell who actually controls a pipeline and who just got a lucky generation.
* ⚖️ **Licensing risk:** Commercial-use rights depend on the tools, subscription tiers, and models behind each piece of work.
* 🎲 **Opaque workflows:** Brands cannot see how work was made, so revisions turn into "prompt gambling".

---

## 💡 What Vibe_Check Does

Vibe_Check is built around the way AI work is actually produced:

* 🏢 **For Brands:** Discover creators by exact tools, skills, and formats; publish structured briefs; evaluate AI-specific portfolios; and manage engagements from first contact to delivery.
* 🎨 **For Creators:** Present work together with the tools, models, and step-by-step workflows behind it; earn verification signals; and respond to briefs that match verified skills.

---

## ✨ Feature Tour

### 1. 🎨 Creator Profiles and AI Portfolios
* Rich profiles with specializations, skills, tools and models, availability, rates, and turnaround.
* Every portfolio item shows:
  * 🧰 The **tools and models** used and the role each played in the pipeline.
  * 🔄 A **workflow breakdown** (for example: base image, motion control, upscaling, audio).
  * 📐 **Technical specs:** aspect ratio, resolution, duration.
  * 📜 A **licensing panel:** commercial use, license type, output ownership, training-data disclosure.
* Image, video, and audio viewer with precise aspect-ratio handling.

### 2. 📝 Structured Brief Definition
Brands define a brief through a guided builder that captures:
* 🎯 Campaign objective and core requirements.
* 🎭 Content type and visual style.
* 🖥️ **Formats and aspect ratios** (9:16, 16:9, 1:1, 4:5, 21:9), resolution, duration, deliverables, and revision rounds.
* ⚙️ Preferred tools and required skills.
* ⚖️ **Commercial-use requirements:** usage channels, territory, license duration, exclusivity, ownership transfer, AI disclosure.
* 💰 Budget and timeline.

### 3. 🔍 Discovery and Filtering
* Search and filter creators by skills, specialization, tools, content type, style, aspect-ratio experience, rate, availability, location, language, commercial-readiness, and verification level.
* 🎯 Relevance ranking with matched-skill highlights (*"matches 4 of 5 skills"*).
* 🧭 **Useful empty states:** When no creator matches, the app explains which filters caused it, shows how many results removing each one would unlock, and offers the closest matches so users never hit a dead end.
* 🔗 Shareable URLs for every search state.

### 4. 🤝 Engagement Management
Invite or receive proposals, shortlist, hire, collaborate in a shared workspace, upload versioned deliverables, request revisions, and approve—all tracked through clear stages from discovery to delivery.

### 5. 💎 Experience
* Glass-style interface with dark and light themes, subtle motion, and 3D touches where they add value.
* Fully responsive from mobile to desktop, keyboard-accessible, with tailored loading, empty, and error states on every screen.

---

## 💎 Bonus Features

### 🛡️ Creator Verification Signals
Visible trust indicators that help brands rely on creator claims:
* ⚙️ **Verified tools:** Proof of commercial-tier subscriptions.
* 🧬 **Verified workflows:** Reproducible pipelines (for example, exported node graphs).
* 🏆 **Verified work:** Admin-reviewed evidence for individual portfolio items.
* *Each badge explains what was verified, how, and when. Trust is also a first-class search filter.*

### 🤖 AI-Assisted Brief Builder
Brands write a rough idea in plain language, and the builder returns a complete, structured brief: content type, style, formats, suggested toolstack, required skills, and commercial-use suggestions. Every field stays editable. It also lists its assumptions and asks clarifying questions, falling back to a rule-based generator if the AI service is unavailable.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| ⚡ **Framework** | Next.js 14 (App Router), React, TypeScript (strict) |
| 🎨 **Styling & UI** | Tailwind CSS, shadcn/ui (restyled), Framer Motion |
| 🗄️ **Backend** | Supabase: PostgreSQL, Auth, Storage, Realtime, Row Level Security |
| ✅ **Validation** | Zod, React Hook Form |
| 🧠 **AI Integration** | Google Gemini API (server-side only) |
| 🧪 **Testing** | Vitest / Jest, Playwright |
| ☁️ **CI & Hosting** | GitHub Actions, Vercel |

---

## 🗄️ Data Model

The full note, with an entity-relationship diagram and design rationale, is in [`docs/data-model-note.md`](./docs/data-model-note.md). The core shapes are highlighted below:

### 👤 Creator Profile
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

