# Implementation Plan: Campaign Engine — Visual PDF Generator

## Objective
Build a CLI-driven Campaign Engine that orchestrates a multi-agent workflow (Strategist, Creative Director, Designer) to generate a unified campaign strategy, complete with visual assets and a final, **publication-quality PDF report** with heavy emphasis on **design renders and visual storytelling**.

---

## Key Files & Context

| File | Purpose |
|:-----|:--------|
| `GEMINI.md` | Core project instructions — data flow, PDF generation, visual standards |
| `.agents/.strategy.md` | Detailed agent execution workflow and formatting rules (v2.0) |
| `client_brief/` | Input directory for project descriptions and assets |
| `results/` | Output directory — intermediate markdown files, final deliverables, and visual assets |
| `results/inspiration_board.md` | Phase 0 output — web search research and visual references |
| `package.json` | Project manifest — dependencies include `md-to-pdf` for PDF export |

---

## Phase-by-Phase Implementation

### Phase 0: Inspiration Research (NEW)
**Purpose:** Research competitor visuals, industry trends, and design references before any content generation. This informs all downstream phases with real-world visual data.

**Implementation:**
1. Scan `client_brief/` for brand name, industry, product, target market
2. Run 8-12 web searches in parallel across: brand identity, competitor campaigns, industry trends, design aesthetics, typography trends
3. Analyze search results and extract: color hex codes, font styles, visual themes, mood keywords, competitor visual strategies
4. Create `results/inspiration_board.md` with: visual style references, color trends, typography trends, competitor analysis, mood keywords, image generation prompt templates

**Deliverable:** `results/inspiration_board.md`

### Phase 1: `results/benchmark_report.md`
**Purpose:** Strategic analysis document.

**Must include:**
- **Brand Audit** — company overview, market context, digital presence assessment, campaign need
- **Competitor Landscape** — comparison matrix, differentiators table
- **Positioning** — essence statement (italicized callout), brand pillars (visual layout with badges), tone of voice pills
- **SWOT Analysis** — 4-quadrant CSS grid layout
- **Gaps & Opportunities** — table (Gap → Opportunity → Impact → Visual Deliverable)
- **Target Persona** — visual card with avatar, name, demographics table
- **Strategic Recommendations** — priority table, action steps

**Input:** `client_brief/` + `inspiration_board.md`

### Phase 2: `results/visual_direction.md`
**Purpose:** Creative direction document.

**Must include:**
- **Moodboards** — 2+ themes (Primary + Secondary/Billboard), each with: vision, mood attribute pills, lighting direction, color temperature, composition rules
- **Color Palette Visual Swatches** — inline HTML color squares (80×80px, 5 colors minimum), color psychology table, color roles & usage table
- **Typography Specimen** — full font hierarchy with live rendered examples (Outfit + Inter)
- **Style Guidelines** — imagery style, lighting, composition, layout rules (Standard vs. Billboard)
- **Before/After Visual Direction** — comparison of current vs. proposed identity
- **Visual Direction Summary** — styled callout box with gradient

**Input:** `results/benchmark_report.md` + `inspiration_board.md`

### Phase 3: `results/media_kit.md` + `results/assets/`
**Purpose:** Asset delivery document with full design render showcase.

**Image Generation Pipeline (3 steps):**
1. **Research:** Read `visual_direction.md` + `inspiration_board.md` for asset-specific requirements
2. **Draft Prompt:** Use Phase 3 prompt templates from `.agents/.strategy.md`, customized with: color hex codes, mood keywords, composition details, lighting direction from visual_direction
3. **Generate → Save → Document:** Run `generate_image`, save to `results/assets/`, update `manifest.json`, embed in `media_kit.md`

**Must generate minimum 6 visual assets:**
| Asset | Format | Dimensions | Purpose | Theme |
|:------|:------:|:-----------|:--------|:------|
| `{product}_product_hero.svg` | SVG | 400×400 | Primary key visual | Primary |
| `billboard_mockup.svg` | SVG | 800×500 | OOH campaign | Billboard |
| `lifestyle_tableau.svg` | SVG | 600×400 | Slice-of-life imagery | Primary |
| `lifestyle_tableau.png` | PNG | — | Additional lifestyle | Primary |
| `brand_heritage.svg` | SVG | 1080×1080 | Heritage brand asset | Brand story |
| `brand_urban.svg` | SVG | 1080×1080 | Urban/youth brand asset | Secondary |
| `brand_texture.svg` | SVG | 1080×1920 | Texture/sensory brand | Any |

**Media Kit Structure:**
- Overview with asset summary table and full inventory table
- Design Render Showcase — every asset as a render card (HTML card with image, spec table, visual narrative)
- Usage Guidelines — color consistency, typography, format recommendations
- Next Steps → Phase 4

**Deliverables:** `results/media_kit.md`, `results/assets/` (all files), `results/assets/manifest.json`

### Phase 4: `results/final_strategy.md` + `results/final_strategy.pdf`
**Purpose:** Consolidated PDF-ready document.

**Document Structure:**
1. Cover Page (standalone, `page-break-after: always`)
2. Table of Contents
3. Executive Summary (key findings, deliverables table)
4. Phase 0: Inspiration Board (condensed reference)
5. Phase 1: Benchmark Report (full content)
6. Phase 2: Visual Direction (full content with color swatches + typography)
7. Phase 3: Media Kit (full render showcase — all 6+ assets with render cards)
8. Appendix (Color Palette Reference, Typography Reference, Asset Manifest, Technical Notes)
9. CSS `<style>` block (page-level, heading, image, table, page-break styling)

**PDF Generation:**
```bash
npx --yes md-to-pdf results/final_strategy.md
```

**Deliverable:** `results/final_strategy.pdf`

---

## Visual Component Templates

### Color Swatch Rendering

```html
<div style="display: flex; gap: 16px; flex-wrap: wrap; margin: 24px 0; justify-content: center;">
  <div style="text-align: center;">
    <div style="width: 80px; height: 80px; background: #HEXCODE; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15); border: 2px solid #eee;"></div>
    <div style="font-size: 12px; font-weight: 700; margin-top: 6px; color: #333;">#HEXCODE</div>
    <div style="font-size: 11px; color: #666;">Color Name</div>
    <div style="font-size: 10px; color: #999;">Role Description</div>
  </div>
</div>
```

### Design Render Card

```html
<div style="border: 1px solid #D49A36; border-radius: 16px; padding: 24px; margin: 24px 0; background: #FFFFFF; box-shadow: 0 4px 16px rgba(0,0,0,0.08); page-break-inside: avoid;">
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px;">
    <h3 style="margin: 0; color: #3D2314;">Render Name</h3>
    <span style="background: #D49A36; color: #F9F6F0; padding: 4px 12px; border-radius: 20px; font-size: 12px; font-weight: 600;">Category</span>
  </div>
  <img src="./assets/filename.svg" alt="Description" style="max-width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); display: block; margin: 16px auto;" />
  <table>
    <tr><th style="width: 140px;">Specification</th><th>Detail</th></tr>
    <tr><td>Format</td><td>SVG</td></tr>
    <tr><td>Dimensions</td><td>1920 × 1080 px</td></tr>
    <tr><td>File</td><td>results/assets/filename.svg</td></tr>
    <tr><td>Palette</td><td>#3D2314, #D49A36, #F4D03F</td></tr>
    <tr><td>Purpose</td><td>Primary key visual</td></tr>
    <tr><td>Usage</td><td>Digital, print, OOH</td></tr>
  </table>
  <p style="color: #555; line-height: 1.6; margin-top: 12px;"><strong>Visual Narrative:</strong> [Composition, color psychology, and intended emotional response].</p>
</div>
```

### Persona Card

```html
<div style="border: 2px solid #D49A36; border-radius: 16px; padding: 24px; margin: 24px 0; background: linear-gradient(135deg, #F9F6F0 0%, #F5E6D3 100%); box-shadow: 0 4px 16px rgba(0,0,0,0.08);">
  <div style="display: flex; align-items: center; gap: 20px; flex-wrap: wrap;">
    <div style="width: 80px; height: 80px; border-radius: 50%; background: linear-gradient(135deg, #D49A36, #F4D03F); display: flex; align-items: center; justify-content: center; font-size: 36px; color: #FFF; font-weight: bold; flex-shrink: 0;">{INITIALS}</div>
    <div>
      <h3 style="margin: 0; color: #3D2314;">{PERSONA_TITLE}</h3>
      <p style="margin: 4px 0; color: #D49A36; font-weight: 600;">{PERSONA_NAME} · {PERSONA_AGE}</p>
      <p style="margin: 4px 0; color: #666; font-size: 14px;">{PERSONA_ROLES}</p>
    </div>
  </div>
  <table style="margin-top: 16px;">
    <tr><th style="width: 140px;">Psychographics</th><td>{PSYCHOGRAPHICS}</td></tr>
    <tr><th>Media Habits</th><td>{MEDIA_HABITS}</td></tr>
    <tr><th>Needs</th><td>{NEEDS}</td></tr>
    <tr><th>Pain Points</th><td>{PAIN_POINTS}</td></tr>
    <tr><th>Decision Drivers</th><td>{DECISION_DRIVERS}</td></tr>
  </table>
</div>
```

---

## Web Search Research Strategy (Phase 0)

Run searches in the following order and categories:

### Category 1: Brand Discovery
```
websearch: "{BRAND_NAME} brand visual identity campaigns 2024 2025"
websearch: "{BRAND_NAME} advertising campaign examples"
```

### Category 2: Industry Context
```
websearch: "{INDUSTRY} food advertising visual trends 2024 2025"
websearch: "{PRODUCT_CATEGORY} packaging and campaign design 2024 2025"
websearch: "premium biscuit cookie brand visual campaigns"
```

### Category 3: Competitor Intelligence
```
websearch: "{COMPETITOR_1} advertising visual campaign"
websearch: "{COMPETITOR_2} packaging design visual identity"
websearch: "FMCG food brand visual campaign North Africa Middle East"
```

### Category 4: Design Inspiration
```
websearch: "warm golden hour food photography style"
websearch: "lifestyle food photography authentic aesthetic"
websearch: "premium food brand visual identity warm tones"
websearch: "outdoor billboard food campaign design"
```

### Category 5: Typography & Color
```
websearch: "food brand typography trends 2024 2025"
websearch: "warm color palette food advertising hex codes"
```

---

## Image Generation Prompt Templates (Phase 3)

### Product Hero Prompt
```
[Product name] cookie with [flavor description], on a warm [background description] background.
[Lighting style: natural golden hour / studio dramatic lighting].
[Composition: centered, overhead, 3/4 angle].
High quality, 4K, professional food photography. No text or typography.
Colors: [hex codes from color palette]. Rich textures, appetizing, premium feel.
[Additional: realistic, photorealistic, professional product shot]
```

### Lifestyle Tableau Prompt
```
[Product name] on a [surface description] beside [context elements], warm sunlight from [direction].
[Style: authentic, slice-of-life, documentary feel].
[Emotion: warm, inviting, nostalgic].
High quality, 4K, professional lifestyle photography. No text or typography.
Colors: [hex codes from color palette].
[Additional: candid, natural light, editorial quality]
```

### Billboard Mockup Prompt
```
[Product name] product shot with [headline text] on [background description], designed for [OOH format].
Bold, high-contrast, readable from [distance]. Professional advertising photography style.
Colors: [hex codes from color palette]. Sharp, clean, premium.
[Additional: billboard format, out-of-home advertising, large format]
```

---

## PDF Generation Workflow

### Step 1: Merge Phase Reports
Concatenate phase reports into `final_strategy.md` with proper section headers and page breaks.

### Step 2: Inject CSS Styling
Add a `<style>` block at the end of `final_strategy.md`:

```html
<style>
  @page { margin: 24mm 20mm 20mm 20mm; }
  body {
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    color: #3D2314; background-color: #FFFFFF; line-height: 1.7; font-size: 14px;
    max-width: 1000px; margin: 0 auto; padding: 20px;
  }
  h1, h2, h3, h4 {
    font-family: 'Outfit', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    color: #3D2314; font-weight: 700;
  }
  h1 { color: #D49A36; font-size: 32px; border-bottom: 3px solid #F4D03F; padding-bottom: 10px; margin-top: 40px; }
  h2 { color: #D49A36; font-size: 24px; border-bottom: 2px solid #F4D03F; padding-bottom: 8px; margin-top: 32px; }
  h3 { color: #3D2314; font-size: 20px; margin-top: 24px; }
  h4 { color: #3D2314; font-size: 16px; margin-top: 20px; }
  img { max-width: 80%; height: auto; display: block; margin: 20px auto; border-radius: 12px; box-shadow: 0 6px 20px rgba(61,35,20,0.12); }
  table { width: 100%; border-collapse: collapse; margin: 16px 0; font-size: 13px; }
  th, td { padding: 10px 14px; border: 1px solid #D49A36; text-align: left; vertical-align: top; }
  th { background-color: #D49A36; color: #F9F6F0; font-family: 'Outfit', sans-serif; font-weight: 600; }
  tr:nth-child(even) { background-color: #FDF8F2; }
  hr { border: none; border-top: 2px solid #F4D03F; margin: 30px 0; }
  blockquote { border-left: 4px solid #D49A36; padding-left: 16px; margin-left: 0; color: #5A3E2B; font-style: italic; }
  ul { padding-left: 20px; }
  li { margin-bottom: 4px; }
</style>
```

### Step 3: Generate PDF
```bash
npx --yes md-to-pdf results/final_strategy.md
```

### Step 4: Quality Check
- [ ] Cover page renders on its own page
- [ ] All embedded images display correctly
- [ ] Color swatches show as colored squares
- [ ] Page breaks create clean chapter separation
- [ ] Tables are properly formatted
- [ ] Typography hierarchy is clear
- [ ] All 6+ design renders displayed with full specification cards
- [ ] No raw HTML source code visible in output
- [ ] Relative asset paths resolve correctly

---

## Deliverables Checklist

### Documents (Markdown)
- [ ] `results/inspiration_board.md` — Phase 0: Web search research and visual references
- [ ] `results/benchmark_report.md` — Phase 1: Strategic analysis with visual data tables
- [ ] `results/visual_direction.md` — Phase 2: Creative direction with color swatches and specimens
- [ ] `results/media_kit.md` — Phase 3: Media kit with design render showcase
- [ ] `results/final_strategy.md` — Phase 4: Consolidated PDF-ready document

### Visual Assets
- [ ] `results/assets/{product}_product_hero.svg` — Primary hero visual
- [ ] `results/assets/billboard_mockup.svg` — OOH billboard mockup
- [ ] `results/assets/lifestyle_tableau.svg` — Lifestyle still life
- [ ] `results/assets/lifestyle_tableau.png` — Lifestyle PNG
- [ ] `results/assets/brand_heritage.svg` — Heritage-themed visual
- [ ] `results/assets/brand_urban.svg` — Urban-themed visual
- [ ] `results/assets/brand_texture.svg` — Texture-focused visual
- [ ] `results/assets/manifest.json` — Asset metadata catalog

### Final Deliverable
- [ ] `results/final_strategy.pdf` — Publication-quality PDF with all renders and strategy

---

## Dependency Installation

```bash
npm init -y
npm install --save-dev md-to-pdf
```

`md-to-pdf` is the primary PDF generation tool. It converts Markdown to PDF using Chromium (headless) and supports custom CSS via `<style>` blocks embedded in the Markdown.

---

## Standards
- All outputs MUST be in the `results/` folder.
- Maintain consistency in brand identity (colors, fonts, tone) across all phases.
- Use explicit composition and delegation for complex sub-tasks.
- **Research-First:** Always run web searches before generating visual content.
- **Visual Priority:** Design renders MUST be displayed prominently with detailed specifications.
- **PDF Quality:** The final PDF must be publication-ready with no raw HTML rendering issues.
- **Universal:** No hardcoded brand names — use `{PLACEHOLDER}` variables throughout.