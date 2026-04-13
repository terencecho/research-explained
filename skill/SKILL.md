---
name: research-explainer
description: |
  Generate interactive HTML explainer pages for AI/ML research papers. Use when the user provides a URL to a research paper PDF and wants it explained at an accessible level, or says "explain this paper", "break down this research", "make an explainer for this paper", or wants to create educational content from a technical paper. Also triggers on "research explainer", "/research-explainer", or requests to make a paper beginner-friendly.
---

# Research Explainer

Generate a single-file interactive HTML page that explains an AI/ML research paper at a college-freshman level. Every technical term gets defined. Every concept gets grounded in analogy. The output is deployable to GitHub Pages.

## Inputs

- **Required**: URL to a PDF research paper (or the paper content if already available)
- **Optional**: URL to the paper's project/demo page (for embedding demo videos)
- **Optional**: GoatCounter script tag for analytics
- **Optional**: Author name and publish date

## Process

### 1. Fetch and Parse the Paper

- Use WebFetch to download the PDF
- Use `pdftotext` (install `poppler` via brew if needed) to extract text
- Read the full text to understand the paper's structure, contributions, and assumed knowledge

### 2. Identify What to Explain

Scan the paper for:
- **AI/ML terminology** the paper assumes readers know (e.g., "diffusion model", "transformer", "attention", "VAE", "embedding", "fine-tuning", "RLHF")
- **Paper-specific concepts** that are novel contributions
- **Architecture components** that can be diagrammed
- **Training/inference pipelines** with multiple stages
- **Quantitative results** that need context to understand
- **Concepts that are hard to explain with text alone** — candidates for HyperFrames videos

### 3. Find Demo Content

If a project page URL is provided:
- Fetch the page and extract video URLs (demo comparisons, Turing tests, side-by-side outputs)
- Identify which demos would be most impactful to embed (reference vs output pairs, comparison grids)

### 4. Generate the HTML Page

Create a single `index.html` file with all CSS and JS inline. Follow this structure exactly:

#### Page Layout
- **Sticky sidebar TOC** (left, 240px) with active section tracking on scroll
- **Main content** (860px max-width) to the right
- **Mobile responsive**: sidebar becomes slide-out drawer under 900px
- **Progress bar** fixed at top of page
- **Glossary button** fixed bottom-right, opens searchable panel

#### Top Section
- `<h1>` title: "[Paper Name]: The Paper, Explained"
- Subtitle: "A beginner-friendly guide to [paper]. Every AI term is defined. Every concept is grounded in analogy."
- Published date and author
- **Research banner**: teal-bordered callout linking to the project page and PDF, explaining this is a companion explainer

#### Content Sections (in order)
1. **The Big Picture** — What the paper does in plain language, the problems it solves (numbered list), key insight callout
2. **See It In Action** (if demo videos available) — Embedded demo video pairs (reference vs output with controls for audio), comparison grids, interactive Turing test pairs with explicit "This one is real" buttons separated from video playback
3. **Background Concepts** — Each prerequisite AI concept the paper assumes, with subsections. Each gets: a definition with hover tooltip, a real-world analogy box, collapsible deep-dive sections
4. **How It Works** — Architecture diagram as styled HTML (vertical card flow with arrows, not ASCII), then subsections for each component with HyperFrames videos where the concept is hard to explain with text
5. **Training/Learning** — Pipeline as styled horizontal card row with colored number circles and arrows, then collapsible details for each stage
6. **Inference** — How it generates output, with HyperFrames video for any spatial/temporal concept (e.g., chunk-based generation)
7. **Data** — Data pipeline as vertical stepper with connected dots, key data innovations
8. **Infrastructure** (if covered) — Brief section on scale
9. **Results** — Styled table with proper CSS classes (not inline styles), human eval highlights, link back to Turing test in section 2
10. **Final Quiz** — 5-6 multiple choice questions covering key concepts

#### Interactive Elements
- **Term tooltips**: `<span class="term">term<span class="tooltip">definition</span></span>` — yellow dashed underline, hover to show
- **Analogy boxes**: blue left-border callout with "Real-world analogy:" prefix
- **Insight boxes**: green left-border callout with "Key Insight:" prefix
- **Collapsible deep-dives**: `<details>` with styled `<summary>`
- **Quizzes**: radio buttons + check button + correct/wrong feedback div
- **Searchable glossary**: fixed panel with text filter, every AI term from the paper alphabetically sorted
- **Turing test** (if applicable): side-by-side videos with separate "This one is real" buttons below each video (NOT on the video itself — clicking video should only play it)

### 5. Create HyperFrames Videos

Only create videos for concepts that are **genuinely hard to explain with text alone**. Good candidates:
- Process visualizations (noise → clean image for diffusion)
- Structural comparisons (dense vs sparse attention grids)
- Feedback loops (closed-loop systems)
- Temporal/spatial mechanisms (chunk stitching, sliding windows)

Bad candidates (don't make videos for these):
- Lists of stages/steps (use styled HTML cards instead)
- Pipelines that are just sequential boxes (use HTML flow diagrams)
- Anything that restates what the text already says clearly

For each HyperFrames video:
- Canvas: 1920x540 (wide format)
- Use Space Mono for all text
- Dark background: #011627
- Keep text LARGE — minimum 28px for labels, 48px for titles
- No description paragraphs in the video — labels and tags only
- The page text provides the detail; the video provides the visual
- Lint with `npx hyperframes lint` before rendering
- Render with `npx hyperframes render --output <path>.mp4 --fps 30`
- Verify with ffmpeg screenshot: `ffmpeg -y -i video.mp4 -ss 00:00:08 -frames:v 1 -update 1 /tmp/check.png` then Read the image
- Video caption should include HyperFrames badge: `<a href="https://github.com/heygen-com/hyperframes" target="_blank" class="badge" title="Made with HyperFrames — HeyGen's HTML-to-video composition framework"><span class="badge-label">made with</span><span class="badge-name">HyperFrames</span></a>`

### 6. Deploy to GitHub Pages

If the user wants to deploy:
- Create or use existing repo (suggest `research-explained` for multiple papers)
- Structure: `<paper-slug>/index.html` + `<paper-slug>/avatar-v-videos/*.mp4`
- Push to main branch, enable Pages with legacy build from root
- If first deploy doesn't trigger, push an empty commit to kick it off

## Design System

### Color Palette (CSS variables)
```css
:root {
  --bg: #0d1117;
  --surface: #161b22;
  --border: #30363d;
  --text: #e6edf3;
  --text-muted: #8b949e;
  --accent: #58a6ff;     /* links, active sidebar, section headers */
  --green: #3fb950;      /* correct answers, insight boxes, best metrics */
  --yellow: #d29922;     /* term highlights, glossary entries */
  --red: #f85149;        /* wrong answers */
  --purple: #bc8cff;     /* h3 headings, quiz questions */
}
```

### HyperFrames Video Palette
```
Background: #011627
Text: #FDFFFC
Accent colors: #2EC4B6 (teal), #FF9F1C (orange), #bc8cff (purple), #E71D36 (red), #415A77 (slate)
```

### Typography
- Body: system font stack (-apple-system, BlinkMacSystemFont, etc.)
- Code: default monospace
- Line height: 1.7 for body text

### Component Patterns

**Architecture diagrams**: Vertical card flow — input tags (colored pills) → arrow-down connectors → component cards (icon + name + description) → output badge. Highlight the core component with a teal border.

**Pipeline stages**: Horizontal card row — colored number circle + bold name + italic description, connected by arrow divs.

**Data pipelines**: Vertical stepper — blue dots with connecting line, bold action + description per step.

**Metrics tables**: CSS class `.metrics-table` with hover rows, uppercase headers, `.val-best` for green highlighted winners.

## What NOT to Do

- Don't add difficulty meters — they don't change reader behavior
- Don't add a "How to Use This Page" section — the features are self-explanatory
- Don't use ASCII art diagrams — use styled HTML components
- Don't use inline styles on tables — use CSS classes
- Don't put description paragraphs in HyperFrames videos — text at that scale is unreadable
- Don't make HyperFrames videos that just duplicate what the text says
- Don't use section number `~` or other placeholder characters
- Don't autoplay demo videos with sound — use `controls` attribute so users can play manually
- Don't put answer selection on video elements in Turing tests — use separate buttons

## Output Checklist

- [ ] Single HTML file with all CSS/JS inline
- [ ] Sidebar TOC with active section tracking
- [ ] All AI terms have hover tooltips
- [ ] Searchable glossary with every term
- [ ] Real-world analogy for each major concept
- [ ] Architecture diagram as styled HTML (not ASCII)
- [ ] Pipeline as styled horizontal cards (not ASCII)
- [ ] HyperFrames videos only for visually-hard-to-explain concepts
- [ ] All HyperFrames videos verified with screenshot before embedding
- [ ] Demo videos (if available) with playback controls and audio
- [ ] Interactive Turing test (if applicable) with separate answer buttons
- [ ] Comprehension quizzes
- [ ] Published date shown
- [ ] GoatCounter script (if provided)
- [ ] Research banner linking to project page and PDF
- [ ] Mobile responsive (sidebar drawer, no horizontal overflow)
