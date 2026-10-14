# Vibe_Check ⚡

> **AI-native marketplace connecting generative creators and brands with verified workflow provenance, model-specific discovery, and structured creative briefs.**

---
## 📌 Executive Summary

Legacy freelance marketplaces (Upwork, Fiverr) treat creative work as static deliverables without understanding AI-native production workflows. Brands face "prompt gambling," non-commercial copyright liabilities, and lack standardized methods to evaluate generative pipelines.

**Vibe_Check** is built from the ground up for the generative AI era. It empowers brands to discover creators with verified tools, evaluate repeatable pipelines (ComfyUI node graphs, LoRAs, seeds), turn rough concepts into structured briefs with an AI agent, and manage production through milestone deliverables.

---
## 🚀 Key Features

### 1. AI Creator Profiles & Workflow Provenance (30% Criteria)
- **Model & Toolstack Breakdown:** Profiles surface granular tools used across the stack (e.g., Midjourney, FLUX.1, Runway Gen-3, Kling, Topaz Video AI, ComfyUI).
- **Pipeline Transparency:** Each portfolio item includes step-by-step production breakdowns (e.g., Base Image Generation $\rightarrow$ Motion Control $\rightarrow$ Video Upscaling $\rightarrow$ Audio Synthesis).
- **Specialization Tagging:** Filter by consistent character animation, product placement, cinematic AI shorts, and photorealism.

### 2. Structured Brand Brief Definition (20% Criteria)
- **Technical Deliverables:** Aspect ratios (9:16 vertical, 16:9 widescreen, 1:1 square), target framerates, and resolutions.
- **Commercial & Legal Clarity:** Explicit terms for commercial buyouts, IP assignment, training data compliance, and source node graph handover.
- **Creative Constraints:** Strict guidelines, aesthetic keywords, and negative parameters to prevent unwanted generation artifacts.

### 3. Intelligent Discovery & Semantic Fallback (25% Criteria)
- **Granular Multi-Filter Engine:** Filter creators by exact tools, media formats, commercial certifications, and specializations.
- **Sensible Empty-State Handling:** When a specific tool query yields zero direct matches, the discovery engine suggests creators specializing in equivalent diffusion pipelines rather than dead-ending with a blank screen.

### 4. High-Trust UX & Milestone Delivery (15% Criteria)
- High-polish dark-mode interface built for creative technologists.
- Side-by-side asset preview and pipeline inspector modal.

---

## 🌟 Bonus Credit Implementations

### 🛡️ Creator Verification Signals (Trust Layer)
- **Verified Commercial Tooling:** Proof of commercial-tier subscriptions (Midjourney Pro, Runway Unlimited, ElevenLabs Commercial) protecting brands against licensing disputes.
- **Reproducible Pipeline Badge:** Verification for creators who supply deterministic, reproducible node graphs (ComfyUI JSON export).

### 🤖 AI-Assisted Brief Builder
- Converts raw natural-language campaign prompts into complete, structured technical creative briefs with suggested toolstacks, format presets, and IP terms.

---

## 🗄️ Data Model Overview

### Creator Profile (`creator_profile`)
```typescript
interface CreatorProfile {
  id: string;
  name: string;
  handle: string;
  specializations: string[];
  tools: {
    category: "Image" | "Video" | "Audio" | "Upscaling";
    name: string;
  }[];
  verifiedSignals: {
    commercialLicenseVerified: boolean;
    workflowProvenanceVerified: boolean;
  };
  portfolio: PortfolioItem[];
}
