# Thesis Talk Slides (Slidev)

This folder contains the thesis presentation deck built with Slidev.

## Files

- `slides.md`: main deck content
- CSS is embedded directly inside `slides.md` in a top-level `<style>` block
- `global-bottom.vue`: global slide number in `X of Y` format (bottom-right)
- `package.json`: Slidev scripts and dependencies

## Run locally

```bash
cd /Users/simar/LLM_Hallucination_Measure/slides/thesis-talk
npm install
npm run dev
```

## Build static slides

```bash
npm run build
```

## Export PDF

```bash
npm run export
```

Notes:
- The deck follows the thesis-talk guideline flow (Introduction, TOC, Methodology, Results, What's New, Impact, Limitations, Validation, Conclusions, Future Work, Extra Material).
- Numbers in slides were taken from the latest `thesis.tex` tables in this repo.
- Theme styling uses Western University brand colours from the official brand standards guide (e.g., Western Purple `#4F2683`, Orchid `#8F55E0`, Deep Focus `#201436`).
