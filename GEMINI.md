# Project Instructions: Campaign Engine — Visual PDF Generation

This project is a CLI-driven campaign creation engine that uses a unified strategy workflow to produce a **comprehensive, visually rich PDF report** with emphasis on **design renders and visual assets**.

---

## /strategy Agent

When the user invokes the `/strategy` command or asks to run the strategy/campaign workflow:

### 1. Ingestion
Scan the root directory and `client_brief/` for inputs (text, URLs, images). If none are found, prompt the user for a brand or project description.

### 2. Execution Phase
Follow the orchestration logic defined in `.agents/.strategy.md`.

- **Phase 0 (Inspiration Research):** Run web searches to collect competitor visuals, industry trends, and design references. Build `results/inspiration_board.md`. Skip if already exists.
- **Phase 1 (Strategist):** Generate `results/benchmark_report.md`
  - Must include visual market data tables, persona cards, competitor landscape grids, SWOT analysis.
- **Phase 2 (Creative Director):** Generate `results/visual_direction.md`
  - Must include moodboards, inline SVG color palette swatches, typography specimens, style guidelines.
- **Phase 3 (Designer):** Generate `results/media_kit.md` and visual assets in `results/assets/`.
  - Must include a **Design Render Showcase** with every generated asset displayed with full specifications.
  - **Image generation follows a 3-step pipeline: (1) Web search for references, (2) Draft prompt from research, (3) generate_image → save → document.**

### 3. Consolidation & Export

Merge the strategic reports into a **visually stunning** Markdown document (`results/final_strategy.md`), then export to PDF.

#### Cover Page Structure

```markdown
<div style="page-break-after: always; text-align: center; padding: 120px 40px 80px;">
  <div style="font-size: 14px; letter-spacing: 6px; color: #D49A36; margin-bottom: 40px; text-transform: uppercase;">Campaign Strategy Document</div>
  <h1 style="font-size: 56px; color: #3D2314; margin: 20px 0; font-family: 'Outfit', sans-serif; font-weight: 700;">{BRAND_NAME}</h1>
  <hr style="width: 120px; border: none; border-top: 3px solid #F4D03F; margin: 30px auto;">
  <p style="font-size: 26px; color: #D49A36; font-weight: 600; margin: 20px 0;">{CAMPAIGN_TAGLINE}</p>
  <p style="font-size: 18px; color: #888; font-style: italic; margin-bottom: 40px;">{ESSENCE_STATEMENT}</p>
  <div style="margin-top: 60px; padding: 20px; border-top: 1px solid #E0D5C3; display: inline-block;">
    <p style="font-size: 14px; color: #888; margin: 4px 0;">Prepared for: {CLIENT_NAME}</p>
    <p style="font-size: 14px; color: #888; margin: 4px 0;">Date: {YYYY-MM-DD}</p>
    <p style="font-size: 14px; color: #888; margin: 4px 0;">Campaign Version: {VERSION}</p>
  </div>
</div>
```

#### Color Palette Visual Swatches

Render color swatches as inline HTML/CSS color squares:

```html
<div style="display: flex; gap: 16px; flex-wrap: wrap; margin: 24px 0; justify-content: center;">
  <div style="text-align: center;">
    <div style="width: 80px; height: 80px; background: #HEXCODE; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.2); border: 2px solid #eee;"></div>
    <div style="font-size: 12px; font-weight: 700; margin-top: 6px;">#HEXCODE</div>
    <div style="font-size: 11px; color: #666;">Color Name</div>
    <div style="font-size: 10px; color: #999;">Role Description</div>
  </div>
</div>
```

#### Render Card Template

For each visual asset, use this template:

```html
<div style="border: 1px solid #D49A36; border-radius: 16px; padding: 24px; margin: 24px 0; background: #FFFFFF; box-shadow: 0 4px 16px rgba(0,0,0,0.08); page-break-inside: avoid;">
  <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 16px;">
    <h3 style="margin: 0; color: #3D2314;">Render Title</h3>
    <span style="background: #D49A36; color: #F9F6F0; padding: 4px 12px; border-radius: 20px; font-size: 12px; font-weight: 600;">Category</span>
  </div>
  <img src="./assets/filename.svg" alt="Description" style="max-width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); display: block; margin: 16px auto;" />
  <table>
    <tr><th style="width: 140px;">Specification</th><th>Detail</th></tr>
    <tr><td>Format</td><td>SVG / PNG</td></tr>
    <tr><td>Dimensions</td><td>1920 × 1080 px</td></tr>
    <tr><td>Purpose</td><td>Primary key visual</td></tr>
    <tr><td>Usage</td><td>Digital, print, OOH</td></tr>
  </table>
  <p style="color: #555; line-height: 1.6; margin-top: 12px;"><strong>Visual Narrative:</strong> [Composition, color psychology, and intended emotional response].</p>
</div>
```

### 4. PDF Generation

Run the following command:

```bash
npx --yes md-to-pdf results/final_strategy.md
```

**CRITICAL RULES for PDF Quality:**
- The resulting PDF MUST be a properly rendered, publication-quality document.
- **Do NOT** use complex raw HTML that causes the PDF to display raw HTML source code. Use standard Markdown with minimal, safe inline HTML/CSS.
- All `<style>` blocks MUST use only simple CSS (colors, margins, padding, borders, fonts). Avoid animations, transforms, or complex layouts.
- Every generated visual asset MUST appear as an embedded image in the final document, not just a text link.
- Images should be displayed at `max-width: 80%` with `border-radius` and `box-shadow` for a polished look.
- Use `page-break-before: always` or `page-break-after: always` on major sections for proper chapter breaks.
- The cover page MUST be on its own page.

### 5. Completion

Tell the user the process is finished and point them to `results/final_strategy.pdf`.

---

## Detailed Implementation Plan

### 1. Data Flow Architecture
- **Phase 0:** Reads `client_brief/`. Creates `inspiration_board.md` from web search results.
- **State Management:** Each phase reads the output of the previous phase(s) to maintain context.
  - `Strategist` reads `client_brief/` + `inspiration_board.md`.
  - `Creative Director` reads `results/benchmark_report.md` + `client_brief/` + `inspiration_board.md`.
  - `Designer` reads `results/visual_direction.md` + `inspiration_board.md`.
- **Persistence:** All intermediate files are saved in `results/` for inspection and debugging.

### 2. Research-First Image Pipeline

The Designer MUST follow the 3-step image generation pipeline:
1. **Research:** Re-read `visual_direction.md` and `inspiration_board.md` for the asset's specific requirements.
2. **Draft Prompt:** Use prompt templates from `.agents/.strategy.md`, customized with color hex codes, mood keywords, and composition details from research.
3. **Generate → Save → Document:** Use `generate_image`, save to `results/assets/`, update `manifest.json`, embed in `media_kit.md`.

### 3. Asset Management Strategy
- **Storage:** All generated assets go in `results/assets/`.
- **Manifest:** `Designer` creates `results/assets/manifest.json` with: filename, alt_text, purpose, format, dimensions, palette, moodboard_theme.
- **Naming:** `{product}_{context}_{variant}.{format}` (e.g., `cookie_product_hero.svg`).
- **Referencing:** All markdown uses relative paths (`![Alt](./assets/filename.svg)`).

### 4. Workflow Orchestration (`.agents/.strategy.md`)
- Sequential chain with error recovery: if a phase fails, fix based on tool output before proceeding.
- **Validation:** After each phase, verify the expected output file exists and is not empty.
- **Visual Validation:** After Phase 3, verify all assets in `manifest.json` exist in `results/assets/`.
- **Phase 0 Skip Rule:** If `results/inspiration_board.md` exists and is not empty, skip Phase 0.

### 5. Consolidation Logic
- **Header Injection:** Each section in `final_strategy.md` gets a `# Section Name` header.
- **Path Correction:** Ensure asset paths remain valid relative to `final_strategy.md`.
- **Metadata:** Cover page with Brand Name, Date, Campaign Version.
- **Visual Continuity:** Consistent CSS styling across merged sections. The `<style>` block goes at the end for `md-to-pdf` compatibility.
- **Render Galleries:** After merging, verify all render cards are properly formatted and images display correctly.

### 6. PDF Export Visual Standards

#### Layout Hierarchy
```
┌─────────────────────────────────┐
│          COVER PAGE             │  ← page-break-after: always
├─────────────────────────────────┤
│     TABLE OF CONTENTS           │  ← page-break-after: always
├─────────────────────────────────┤
│     EXECUTIVE SUMMARY           │  ← page-break-after: always
├─────────────────────────────────┤
│  PHASE 0: INSPIRATION BOARD     │  ← page-break-before: always
│  (Research findings reference)   │
├─────────────────────────────────┤
│  PHASE 1: BENCHMARK REPORT      │  ← page-break-before: always
│  ├─ Brand Audit                 │
│  ├─ Competitive Landscape       │
│  ├─ Positioning                 │
│  ├─ SWOT Analysis               │
│  ├─ Gaps & Opportunities        │
│  └─ Target Persona              │
├─────────────────────────────────┤
│  PHASE 2: VISUAL DIRECTION      │  ← page-break-before: always
│  ├─ Moodboards                  │
│  ├─ Color Palette (swatches)    │
│  ├─ Typography Specimen          │
│  └─ Style Guidelines            │
├─────────────────────────────────┤
│  PHASE 3: MEDIA KIT             │  ← page-break-before: always
│  ├─ Overview                    │
│  ├─ Design Render Showcase      │
│  │  ├─ Render 1 (full card)     │
│  │  ├─ Render 2 (full card)     │
│  │  └─ Render 3 (full card)     │
│  └─ Usage Guidelines             │
├─────────────────────────────────┤
│        APPENDIX                 │  ← page-break-before: always
│  ├─ Color Palette Reference     │
│  ├─ Typography Reference        │
│  ├─ Asset Manifest              │
│  └─ Technical Notes             │
├─────────────────────────────────┤
│        CSS <style> BLOCK        │  ← at document end
└─────────────────────────────────┘
```

#### Color Palette (Visual Identity)
| Swatch | Hex | Role |
|--------|-----|------|
| {COLOR_1_NAME} | `{COLOR_1_HEX}` | {COLOR_1_ROLE} |
| {COLOR_2_NAME} | `{COLOR_2_HEX}` | {COLOR_2_ROLE} |
| {COLOR_3_NAME} | `{COLOR_3_HEX}` | {COLOR_3_ROLE} |
| {COLOR_4_NAME} | `{COLOR_4_HEX}` | {COLOR_4_ROLE} |
| {COLOR_5_NAME} | `{COLOR_5_HEX}` | {COLOR_5_ROLE} |

#### Typography (Visual Identity)
| Usage | Font | Weight |
|-------|------|--------|
| Headlines / Billboard Copy | **{HEADLINE_FONT}** | {HEADLINE_WEIGHT} |
| Subheadings | **{SUBHEAD_FONT}** | {SUBHEAD_WEIGHT} |
| Body Copy | **{BODY_FONT}** | {BODY_WEIGHT} |
| Accent / Data | **{ACCENT_FONT}** | {ACCENT_WEIGHT} |

---

## Standards
- All outputs MUST be placed in the `results/` folder.
- Maintain consistency in brand identity (colors, fonts, tone) across all phases.
- Use explicit composition and delegation for complex tasks.
- **Research-First:** Always run web searches before generating visual content.
- **Visual Priority:** Design renders MUST be displayed prominently with detailed specifications.
- **PDF Readiness:** Every markdown file is formatted for clean PDF conversion.
- **Universal:** No hardcoded brand names — use `{PLACEHOLDER}` variables throughout.
- **Dry Runs:** Use the `results/` folder to verify outputs without overwriting previous successful runs unless intended.
- **Visual Quality Gate:** Before completion, review the generated PDF for:
  - Correct image rendering (no broken paths)
  - Proper page breaks (clean chapter separation)
  - Color palette consistency across phases
  - Typography hierarchy (headings, body, accent)
  - Render showcase completeness (every asset displayed)
  - No raw HTML source code visible