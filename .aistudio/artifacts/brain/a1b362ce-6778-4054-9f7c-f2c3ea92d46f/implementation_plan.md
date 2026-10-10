# Caption Studio: Casual Social & Relationship Moments

An intuitive, clean editorial caption writing studio tailored for casual social media (Instagram, TikTok, Threads, Stories) with a rich focus on relationship scenarios, everyday life situations, and multimodal photo analysis.

### User Review & Critical Decisions

> [!IMPORTANT]
> The following directions were confirmed during clarification and form the foundation of the architecture:

- **Primary Platforms**: Casual social media focus (Instagram posts, Reels, TikTok, photo dumps, Threads, Stories).
- **Core Generation Workflow**: Relationship-centric situations (couples, anniversaries, soft launches, besties, situationships, breakups & healing, cute candid moments) complemented by broader everyday life scenarios.
- **Visual Aesthetic**: Clean minimalist editorial aesthetic with airy warm paper canvas (`#FAFAF8`), subtle hairline borders, refined typography, zero-pill unboxed metadata, and high-legibility layout.
- **AI Architecture**: Full-stack server proxy using `@google/genai` with `gemini-3.8-flash` for low-latency caption ideation, tone polishing, and multimodal photo-to-caption analysis.

---

### 1. Overview & Core Concept

- **What It Does**: Caption Studio empowers creators and everyday social media users to generate, refine, and organize engaging captions for any photo or moment. It provides deep situational prompts for every relationship dynamic (from flirting and soft launches to anniversary essays and bestie banter), multimodal image analysis to write captions directly from uploaded photos, and interactive polish tools (shorten, banter, aesthetic lowercase, romantic tension).
- **Target Audience / Persona**: Social media users, couples, friend groups, content creators, and casual posters who struggle with blank-page syndrome when posting to Instagram, TikTok, Threads, or photo dumps.
- **Key Value**: Delivers instant, voice-authentic captions that don't sound like generic corporate AI copy, formatted with catchy hooks, character counts, and curated hashtags.

---

### 2. User Experience & Visual Design

#### Key User Flows
1. **Explore Situations & Prompts**: Browse or filter curated relationship scenarios (e.g., *Soft Launching*, *First Trip Together*, *Unposted Photo Dump*, *Bestie Candid*, *Anniversary Without Being Cringe*, *Late Night Food Run*).
2. **Instant Generation & Customization**: Select or type custom context, choose voice/tone (Witty Banter, Soft & Romantic, Lowkey Minimalist, Chaotic Fun, Poetic, Dry Sarcasm), and generate 4–6 distinct stylistic variations.
3. **Photo Analysis ("Snap to Caption")**: Upload an image to analyze its setting, visual tone, and mood, automatically generating matching captions with situational context.
4. **Caption Polish Lab**: Refine any caption with 1-click transformation tools: *Make Funnier*, *Make More Subtle*, *Aesthetic Lowercase*, *Add Hook*, *Trim to One-Liner*.
5. **Copy & Collections**: Quick copy with instant toast feedback, character & word counts, and save to local collections (*Summer Dumps*, *Couples Vault*, *Besties*).

#### Visual Identity & Theme
- **Aesthetic Direction**: Clean editorial minimalism. Warm off-white canvas (`#FAFAF9` / `#F5F5F0`), slate charcoal typography (`#18181B`), crisp hairline dividers (`#E4E4E7`), and a warm terracotta/amber interactive accent (`#D97706` / `#B45309`).
- **Typography**: 
  - Primary Display & Headings: `Plus Jakarta Sans` with tight tracking for modern editorial crispness.
  - Body & Captions: Clean, highly legible sans-serif with balanced measure.
  - Tabular Numerals: Monospace `tabular-nums` for character counts, word counters, and timestamps.
- **Zero-Pill Discipline**:
  - Tags and categories rendered as unboxed inline text with subtle typographic separators (`·` or `/`).
  - Interactive filter controls use clean segmented button switches, never candy badges.
- **Top Bar Contract**:
  - Zone 1: Single clean wordmark (`Caption Studio`).
  - Zone 2: Navigation links (`Situations`, `Snap & Caption`, `Custom Studio`, `Saved Collections`).
  - Zone 3: Primary action (`New Caption`).

---

### 3. Key Product Decisions & Trade-Offs

- **Decision 1: Relationship-First Situational Taxonomy**
  - *Chosen Approach*: Deep preset catalog spanning 6 distinct relationship chapters: *Dating & New Love*, *Deeply in Love & Anniversaries*, *Best Friends & Chaos*, *Soft Launches & Sneaky Links*, *Healing & Solo Era*, and *Family & Roots*.
  - *Why*: Solves the hardest posting problem—finding the exact nuanced tone between "too cheesy" and "trying too hard".
  - *Alternatives Considered*: Generic keyword generator was rejected because real users post about specific human moments.

- **Decision 2: Multi-Part Caption Output Format**
  - *Chosen Approach*: Each generation yields 5 distinct stylistic angles: *The One-Liner*, *The Subtle Flex*, *The Playful Banter*, *The Soft Aesthetic*, and *The Storyteller*.
  - *Why*: Gives users immediate variety so they find an option matching their exact personal brand in one click.

- **Decision 3: Server-Side Gemini API Proxy with Express**
  - *Chosen Approach*: Express server backend with `@google/genai` calling `gemini-3.8-flash` via `/api/generate-captions`, `/api/analyze-photo`, and `/api/polish-caption`.
  - *Why*: High-speed generation, zero API key exposure to the client browser, full multimodal support for image uploads, and reliable structured JSON output.

- **Decision 4: LocalStorage Collections for Zero Friction**
  - *Chosen Approach*: Client-side persistence using `localStorage` for saved captions and custom collections.
  - *Why*: Instant access, zero signup barriers, offline resiliency, and immediate utility for casual users.

---

### 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────┐
│                      Client UI                         │
│  ┌─────────────────┐ ┌────────────────┐ ┌───────────┐  │
│  │ Situation Picker│ │ Studio Composer│ │Photo Snap │  │
│  └────────┬────────┘ └───────┬────────┘ └─────┬─────┘  │
│           │                  │                │        │
│           └───────────┬──────┴────────────────┘        │
│                       ▼                                │
│           ┌───────────────────────┐                    │
│           │   Caption State Store │                    │
│           │ (Filters, Draft, Savs)│                    │
│           └───────────┬───────────┘                    │
└───────────────────────┼────────────────────────────────┘
                        │ HTTP POST (JSON / base64)
                        ▼
┌────────────────────────────────────────────────────────┐
│                   Express Server                       │
│  ┌───────────────────────┐  ┌───────────────────────┐  │
│  │ /api/generate-captions│  │  /api/analyze-photo   │  │
│  └───────────┬───────────┘  └───────────┬───────────┘  │
│              │                          │              │
│              ▼                          ▼              │
│      GoogleGenAI Client (User-Agent: 'aistudio-build') │
│              │                                         │
│              ▼                                         │
│   Gemini 3.8 Flash (Structured JSON Schema Output)     │
└────────────────────────────────────────────────────────┘
```

#### Core Endpoints
1. `POST /api/generate-captions`: Accepts topic, relationship situation, tone, platform, and optional reference details; returns structured caption variations with hooks, body, and hashtag sets.
2. `POST /api/analyze-photo`: Accepts base64 image data + optional mood/situation hint; inspects visual cues and outputs tailored casual social captions.
3. `POST /api/polish-caption`: Modifies an existing caption according to a target action (*humor*, *aesthetic-lowercase*, *shorten*, *romantic*, *sarcasm*).

#### Data Model
- `CaptionItem`: `{ id, hook, text, hashtags: string[], styleAngle, characterCount, saved: boolean, situationTag }`
- `SituationScenario`: `{ id, category, title, description, promptSeed, suggestedTones: string[], popularExample }`
- `SavedCollection`: `{ id, title, captionIds: string[], createdAt }`

---

### 5. Verification Plan

1. **Compilation & Linting**: Run `compile_applet` to ensure TypeScript, Tailwind, and Express full-stack architecture build cleanly with zero errors.
2. **Server API Verification**: Confirm `/api/generate-captions`, `/api/analyze-photo`, and `/api/polish-caption` endpoints function seamlessly.
3. **End-to-End User Flow**:
   - Select relationship scenario $\rightarrow$ Generate captions $\rightarrow$ Verify 5 distinct stylistic angles appear.
   - Test "Snap & Caption" photo upload with sample image $\rightarrow$ Verify multimodal caption extraction.
   - Test Polish Lab transformation buttons $\rightarrow$ Verify caption transforms smoothly.
   - Test copy to clipboard with toast notifications $\rightarrow$ Verify character counts and hashtag copies.
   - Test saving captions and organizing collections in local storage.
