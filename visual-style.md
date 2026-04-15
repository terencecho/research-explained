# Visual Style — Research Explained

Project-specific overrides for the compose-video skill. These take precedence over house-style.md defaults.

## Palette

Use the dark premium palette consistently across all explainer pages:

| Role       | Color     | Usage                              |
|------------|-----------|------------------------------------|
| Background | `#011627` | Canvas bg                          |
| Foreground | `#FDFFFC` | Primary text, borders              |
| Teal       | `#2EC4B6` | Positive/good, primary accent      |
| Red        | `#E71D36` | Negative/bad, warnings             |
| Orange     | `#FF9F1C` | Secondary accent, highlights       |
| Purple     | `#bc8cff` | Tertiary accent, special elements  |
| Green      | `#3fb950` | Success, results, callouts         |
| Muted      | `#778DA9` | Secondary text, labels             |
| Dark muted | `#415A77` | Borders, inactive elements         |

## Typography

- **Font:** Space Mono from Google Fonts (weights 400 + 700)
- **Minimum font size: 16px** — nothing smaller, ever. Test at rendered resolution.
- Title: 34-40px
- Key labels/stats: 22-30px
- Body labels: 16-20px

## Dimensions

- **Standard composition:** 1920 x 540 (wide landscape for inline embedding)
- Duration: 10-16s depending on concept complexity

## Content Principles

**Factual accuracy is non-negotiable.** Before animating any concept:
- Verify all claims, numbers, and comparisons against the source paper
- Don't invent dramatic statistics for visual impact (e.g., "10,000x" when the real number is 25%)
- If a concept has nuance (like scaling behavior vs absolute reduction), represent it accurately

**Concrete over abstract.** Viewers should immediately understand what they're looking at:
- Use recognizable shapes (hearts, smiley faces, grids) not abstract particle swirls
- Use labeled bars, matrices, and diagrams not unlabeled geometric orbits
- If a text analogy is clearer than an animation, don't force a video

**Side-by-side for comparisons.** When showing A vs B:
- Put them next to each other, not in sequential phases
- The difference should be obvious at a glance without reading labels
- Dim the "bad" side, glow the "good" side to direct attention

**Only make videos that add value.** Not every section needs a video:
- Processes (denoising, training stages, data flowing through pipeline) benefit from animation
- Comparisons (standard vs sparse, generic vs identity-aware) benefit from side-by-side visuals
- Simple concepts well-served by a text analogy (Q/K/V, voice cloning = "ChatGPT for sound") don't need videos

## Layout Rules

**Verify spacing mathematically before rendering.** For every positioned element, calculate:
- Element top = container offset + element offset
- Element bottom = element top + element height (including font size)
- Confirm no overlaps between adjacent elements

**Text must never overlap visuals.** Place labels in dedicated zones (top bar, bottom bar, side labels) — not floating over animated content.

**Leave breathing room.** Minimum 20px gap between any text and any visual element.

## Animation

Follow house-style.md for easing variety, build/breathe/resolve pacing, and entrance patterns. Additional rules:

- **Discrete steps for discrete processes.** If something happens in steps (denoising), show visible discrete jumps, not smooth interpolation.
- **The whole image, not a sweep.** Processes that operate on all data simultaneously (like denoising) should animate the whole canvas at once, not sweep left-to-right.
- **Stepped counters match the visual.** If a step counter says "Step 5/24", the visual should reflect exactly 5 steps of progress.
