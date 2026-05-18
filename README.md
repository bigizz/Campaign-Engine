# Campaign Engine

This is an AI-driven campaign creation engine that uses a unified strategy workflow to produce a comprehensive, visually rich PDF report with an emphasis on design renders and visual assets.

## How to Use

1. **Prepare the Client Brief:**
   Place any text, URLs, or image inputs in the `client_brief/` directory.

2. **Run the Strategy Workflow:**
   Ask the AI assistant to `/strategy` or "run the strategy/campaign workflow". If no inputs are found, you will be prompted for a brand or project description.

3. **Workflow Execution:**
   The engine will automatically execute 4 phases:
   - **Phase 0 (Inspiration Research):** Collects visuals, trends, and references (`results/inspiration_board.md`).
   - **Phase 1 (Strategist):** Generates `results/benchmark_report.md` with brand audit, SWOT, etc.
   - **Phase 2 (Creative Director):** Generates `results/visual_direction.md` with moodboards, color palettes, and typography.
   - **Phase 3 (Designer):** Generates `results/media_kit.md` and visual assets in `results/assets/`.

4. **Consolidation and PDF Export:**
   The engine merges the strategic reports into a visually stunning Markdown document (`results/final_strategy.md`), and exports it as a PDF at `results/final_strategy.pdf`.

5. **View Results:**
   Once finished, you can view the `results/final_strategy.pdf` for the full campaign strategy document.

## Requirements

- Node.js / `npx` (for generating the PDF via `md-to-pdf`)
