# Research Explained

Interactive explainer pages for AI research papers. Every technical term defined, every concept grounded in analogy.

## Pages

| Paper | Link | Published |
|-------|------|-----------|
| [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685) | [Read the explainer](https://terencecho.github.io/research-explained/lora/) | June 2021 |
| [Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory](https://arxiv.org/abs/2504.19413) | [Read the explainer](https://terencecho.github.io/research-explained/mem0/) | April 14, 2026 |
| [Fast KV Compaction via Attention Matching](https://arxiv.org/abs/2602.16284) | [Read the explainer](https://terencecho.github.io/research-explained/kv-compaction/) | April 13, 2026 |
| [Avatar V](https://dynamic.heygen.ai/www/Paper%20Links/avatarv_tech_report.pdf) | [Read the explainer](https://terencecho.github.io/research-explained/avatar-v/) | April 13, 2026 |

## Claude Code Skill

The `skill/` directory contains a [Claude Code](https://claude.ai/code) skill that generates these pages from a paper URL.

### Install

```bash
npx skills add https://github.com/terencecho/research-explained --skill skill
```

### Use

In Claude Code, provide a paper URL:

```
/research-explainer https://arxiv.org/pdf/some-paper.pdf
```

Or just say "explain this paper" with a PDF link.

### What it produces

- Single-file HTML page with all CSS/JS inline
- Sticky sidebar table of contents with scroll tracking
- Hover tooltips on every technical term
- Searchable glossary panel
- Real-world analogies for each concept
- Styled architecture diagrams and pipeline visualizations
- [HyperFrames](https://github.com/heygen-com/hyperframes) motion graphics for concepts hard to explain with text
- Embedded demo videos from the paper's project page
- Interactive Turing test (if applicable)
- Comprehension quizzes
- Deployable to GitHub Pages
