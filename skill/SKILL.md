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
- **Flex body** (`display: flex`, `max-width: 1200px`, centered) with sticky sidebar + `.main-content` div. NOT fixed-position sidebar with margin-left.
- **Sticky sidebar TOC** (`.sidebar`, left, 240px, `border-right` divider, no background fill) with active section tracking on scroll. Links use **border-left indicator** for active state (not rounded background). Include `<a href="../" class="sidebar-home">&larr; All Papers</a>` at the top before the TOC title.
- **Main content** (`.main-content`, 860px max-width) to the right
- **No section numbering** — sidebar TOC and headings use plain text labels, not "1. The Big Picture"
- **Mobile responsive**: sidebar becomes slide-out drawer (`.mobile-open`) with dark overlay under 900px. Toggle button is round accent-colored button at bottom-left.
- **Progress bar** (`.progress-bar`) fixed at top of page
- **Glossary button** (`.glossary-toggle`) fixed bottom-right, accent blue with book emoji (&#x1F4D6;), opens searchable panel

#### Top Section
- `<h1>` title: "[Paper Name]: The Paper, Explained"
- Subtitle: `<p class="subtitle">` "A beginner-friendly guide to [paper]. Every AI term is defined. Every concept is grounded in analogy."
- Meta line: `<p class="meta">` with paper authors, affiliation, **paper publication year**, explainer publish date, and author name. Format: "Paper by [Authors] ([Affiliation], [Year])"
- **Research banner**: gradient background (`linear-gradient(135deg, #011627, #0d2137, #1a1a3a)`), teal border, icon on left (`.banner-icon`), text + pill-style link buttons (`.banner-link.primary` / `.banner-link.secondary`) linking to the paper PDF, project page, and any related posts

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
11. **Why This Paper Matters** — Three subsections: (a) practical impact for builders/practitioners, (b) significance for the research community (key findings, what it changes), (c) the bigger picture (where this fits in the trajectory of the field). This section should connect the paper's contributions to real-world applications and explain why someone should care.

#### Interactive Elements
- **Term tooltips**: `<span class="term">term<span class="tooltip">definition</span></span>` — `color: var(--yellow)` on the term text itself, `1px dashed` underline. Tooltip uses `background: var(--surface)` with `border: 1px solid var(--border)` (NOT yellow border).
- **Analogy boxes**: `background: #1a2332` (solid dark blue-gray, not rgba), blue left-border, "Real-world analogy:" prefix
- **Insight boxes**: `background: #1a2a1a` (solid dark green-gray, not rgba), green left-border, "Key Insight:" prefix
- **Collapsible deep-dives**: `<details>` with `background: var(--surface)`, border, rounded corners, `overflow: visible` (NOT `overflow: hidden` — that clips tooltips inside). Summary uses `+ / -` monospace prefix (not triangle ▶). Hover uses `var(--accent-subtle)`. Use `padding: 0.75rem 1.5rem` on summary and `padding: 0 1.5rem 1.25rem` on `.content` (NOT `1rem` — too tight). Lists inside `.content` need `padding-left: 2.5rem` so bullet markers (which render outside the list's padding box) don't sit flush against the container border.
- **Quizzes**: radio buttons + check button + feedback. Correct = `background: #1a2a1a`, wrong = `background: #2a1a1a`.
- **Searchable glossary**: fixed panel, entries use `<strong>` for term + `<p>` for definition, separated by `border-bottom`
- **Turing test** (if applicable): side-by-side videos with separate "This one is real" buttons below each video (NOT on the video itself — clicking video should only play it)

### 5. Create HyperFrames Videos

Create videos proactively for every concept that genuinely benefits from visual animation. **Don't ask whether to add more videos — identify all good candidates upfront and build them all in the first pass.** Aim for 2-4 videos per paper. If a concept is spatial, structural, or involves information flow that's hard to picture from text, make a video for it.

Good candidates:
- Process visualizations (noise → clean image for diffusion)
- Structural comparisons (dense vs sparse attention grids)
- Information flow / routing (tokens moving through a system, retrieval paths)
- Network/graph traversals (following relationships between entities)
- Feedback loops (closed-loop systems)
- Temporal/spatial mechanisms (chunk stitching, sliding windows)

Bad candidates (don't make videos for these):
- Lists of stages/steps (use styled HTML cards instead)
- Pipelines that are just sequential boxes (use HTML flow diagrams)
- Anything that restates what the text already says clearly

For each HyperFrames video:

**Composition HTML format** (required structure):
```html
<div data-composition-id="my-video" data-width="1920" data-height="540" data-start="0" data-duration="10">
  <!-- Content here -->
  <style>/* Scoped styles */</style>
  <script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js"></script>
  <script>
    window.__timelines = window.__timelines || {};
    var root = document.currentScript.parentElement;
    var tl = gsap.timeline({ paused: true });
    // Build animations with tl.from() / tl.to()...
    window.__timelines["my-video"] = tl;
  </script>
</div>
```

**Design rules:**
- Canvas: 1920x540 (wide format)
- Use Space Mono for all text
- Dark background: #011627
- Keep text LARGE — minimum 28px for labels, 48px for titles. Common mistake: using 12-16px text that's unreadable when the video is embedded. When in doubt, go bigger.
- Watch for bottom clipping — the 540px canvas height is tight. Leave 30px+ margin at the bottom for text below bar charts or diagrams.
- **Use flexbox for layout, not absolute positioning with hardcoded pixels.** Absolute positioning leads to uneven spacing and dead whitespace. Use `display: flex; align-items: center; justify-content: center;` on the scene container and flex layouts for columns/rows. The only element that should be `position: absolute` is the title (centered at top). Everything else should flow with flex.
- No description paragraphs in the video — labels and tags only
- The page text provides the detail; the video provides the visual

**Build process:**
- Create the HTML in a directory (e.g., `/tmp/my-vid/index.html`)
- Lint with `npx hyperframes lint .` from that directory
- Render with `npx hyperframes render --output <path>.mp4 --fps 60`
- **Save the source**: Copy the source HTML to `<paper-slug>/videos/src/<video-name>.html` so it can be re-rendered or tweaked later

**Verification (required before embedding):**
- Take screenshots at **multiple timestamps** (early, midpoint, near-end) since content animates in
- `ffmpeg -y -i video.mp4 -ss 00:00:03 -frames:v 1 -update 1 /tmp/check-early.png` then Read image
- `ffmpeg -y -i video.mp4 -ss 00:00:06 -frames:v 1 -update 1 /tmp/check-mid.png` then Read image
- `ffmpeg -y -i video.mp4 -ss 00:00:09 -frames:v 1 -update 1 /tmp/check-end.png` then Read image
- **Technical check**: all text visible (not clipped), labels legible, animations completed. **Check spacing**: no large dead whitespace areas, content should be evenly distributed across the 1920x540 canvas. If elements are bunched in the center with empty top/bottom, fix the layout.
- **Content check** (critical): Look at each screenshot and ask "does this actually explain the concept visually, or does it just restate text in boxes?" If the video is just labeled boxes with no visual insight (no connections shown, no spatial relationships, no animation that reveals something text can't), redesign it before embedding. Videos should show structure, flow, or relationships — not just repeat labels.
- If anything fails either check, fix and re-render before embedding

**Embedding in the page** (use `autoplay muted loop playsinline`, NOT `controls`):
```html
<div class="video-embed">
  <video src="videos/my-video.mp4" autoplay muted loop playsinline></video>
  <div class="video-caption"><a href="https://github.com/heygen-com/hyperframes" target="_blank" class="badge" title="Made with HyperFrames — HeyGen's HTML-to-video composition framework"><span class="badge-label">made with</span><span class="badge-name">HyperFrames</span></a> Short description of what the video shows</div>
</div>
```
Badge is a stacked teal pill (small "made with" on top, bold "HyperFrames" below), always first child in `.video-caption`.

### 6. Self-Review Before Presenting to User

Before showing the page to the user or saying it's done, run this review checklist:

1. **Jargon audit**: Search the page for acronyms and technical terms that appear in body text without a `class="term"` tooltip OR an inline explanation. Common offenders: VRAM, MLP, GLUE, FLOPs, NLU/NLG, NNLS, OMP, GQA, SFT, RLHF. Replace bare acronyms with plain language or add tooltips.
2. **CSS consistency**: Verify the page has all accumulated CSS fixes: `overflow-x: hidden` on mobile body, `overflow: visible` on details, `padding-left: 2rem` on lists inside `.content`, scroll-to-bottom TOC fix in JS.
3. **Spacing check on videos**: Review every HyperFrames video screenshot for dead whitespace. If elements are bunched in a narrow band with empty top/bottom, fix the layout.
4. **Content check on videos**: Does each video explain a concept visually that text can't, or does it just restate labels in boxes?
5. **Mobile check**: Are there any fixed-width elements without mobile overrides (tooltips, glossary, tables, grids)?
6. **Cross-reference**: Do all sidebar TOC links match actual h2 IDs? Are there any dead internal links?

Fix issues found before presenting. Do not ask the user to review issues you can catch yourself.

### 7. Deploy to GitHub Pages

If the user wants to deploy:
- Create or use existing repo (suggest `research-explained` for multiple papers)
- Structure: `<paper-slug>/index.html` + `<paper-slug>/videos/*.mp4`
- Update the homepage `index.html` to add a paper card for the new explainer
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
  --accent: #58a6ff;        /* links, active sidebar, section headers */
  --accent-subtle: #1f6feb22; /* hover backgrounds on interactive elements */
  --green: #3fb950;         /* correct answers, insight boxes, best metrics */
  --yellow: #d29922;        /* term highlights, glossary entries */
  --red: #f85149;           /* wrong answers */
  --purple: #bc8cff;        /* h3 headings, quiz questions */
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
- Don't number sections — no "1. The Big Picture" or "3a. Sparse Attention". The sidebar provides structure.
- Don't autoplay demo videos with sound — use `controls` attribute so users can play manually
- Don't put answer selection on video elements in Turing tests — use separate buttons
- Don't use `overflow: hidden` on `<details>` elements — it clips tooltips. Use `overflow: visible`.
- Don't use fixed pixel widths on content elements without mobile overrides — tooltips, glossary panel, and grids must work on 375px screens
- Don't use a fixed-position sidebar with `margin-left` on main content — use flex layout with sticky sidebar

## Mobile Responsiveness

Every page must work on 375px screens. The `@media (max-width: 900px)` block must handle:
- Sidebar: slides from left with `.mobile-open` + dark overlay
- Glossary panel: `width: calc(100vw - 3rem)` (never fixed width exceeding viewport)
- Term tooltips: `width: calc(100vw - 4rem); max-width: 320px; left: 0; transform: none;`
- Two-column grids: collapse to `grid-template-columns: 1fr`
- Pipeline stages: `overflow-x: auto` with `-webkit-overflow-scrolling: touch`
- Architecture components: `max-width: 100%`
- Metrics tables: `display: block; overflow-x: auto;`

## Output Checklist

- [ ] Single HTML file with all CSS/JS inline
- [ ] Flex body layout with sticky sidebar (not fixed sidebar)
- [ ] "← All Papers" home link at top of sidebar
- [ ] Sidebar TOC with active section tracking (no section numbers). The scroll handler must force the last section active when the user scrolls to the bottom of the page: `if ((window.innerHeight + window.scrollY) >= document.documentElement.scrollHeight - 50) current = sections[sections.length - 1].id;`
- [ ] All AI terms have yellow-colored hover tooltips
- [ ] Searchable glossary (accent blue button with 📖 emoji)
- [ ] Real-world analogy for each major concept (solid dark bg, not rgba)
- [ ] Architecture diagram as styled HTML (not ASCII)
- [ ] Pipeline as styled horizontal cards (not ASCII)
- [ ] Collapsible sections with +/- prefix, `overflow: visible`
- [ ] HyperFrames videos only for visually-hard-to-explain concepts
- [ ] All HyperFrames videos verified at multiple timestamps before embedding
- [ ] HyperFrames videos embedded with `autoplay muted loop playsinline` + teal badge
- [ ] Demo videos (if available) with playback controls and audio
- [ ] Interactive Turing test (if applicable) with separate answer buttons
- [ ] Comprehension quizzes (no numbered questions)
- [ ] "Why This Paper Matters" section with practical impact, research significance, and bigger picture
- [ ] Meta line with paper authors, publish date
- [ ] Research banner with gradient bg, icon, pill-style link buttons
- [ ] Mobile responsive (sidebar drawer, no horizontal overflow on 375px)
- [ ] Homepage updated with new paper card
