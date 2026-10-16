# Contributing a post

Each post is a folder under `posts/` named `YYYY.MM.DD_short-slug/`, containing an `index.md` (or `index.qmd` if it runs code) and any figures. The folder name becomes the post's URL, so don't rename a folder once the post is published.

## Frontmatter

Keep it short. Copy [`templates/post-template.md`](templates/post-template.md) to start:

```yaml
---
title: "Single-dose HPV vaccination, two years in"
author: "Robyn Stuart"
date: "2026-01-22"
description: "Optional one-sentence summary for the index page."
categories: [HPV, HPVsim, Vaccination]
image: headline-figure.png   # optional
---
```

- `date` is the post date and sets the order on the index page. For a post about older work, use a date close to when that work was published.
- `categories` are free-form keywords and become filters on the index page. Reuse an existing spelling where one fits (see below).
- Don't repeat the title as an `# H1` in the body; Quarto renders it from the frontmatter.
- Put citations (authors, title, journal, DOI, code links) in the body, e.g. a **Source.** line near the top.

## Keywords

Preferred spellings for common categories, so the filters don't split:

- **Models:** Starsim, Covasim, FPsim, HPVsim, HIVsim, STIsim
- **Diseases:** COVID-19, HIV, HPV, Polio, Bacterial vaginosis, Cervical cancer
- **Methods:** Agent based models, Networks, Dose response, Vaccination, Contact tracing, Calibration
- **Places:** country names in full, e.g. Kenya, South Africa
- **Other:** News, AI, Workflows, Compute, Dashboards, History

Add to this list when you introduce a keyword others are likely to reuse.

## Figures

Save figures in the post folder and reference them with relative paths: `![Caption](headline-figure.png)`. No absolute paths.

## Previewing

Install [Quarto](https://quarto.org), then run `quarto preview` from the repo root. `./publish` builds and publishes the site to GitHub Pages.

## Writing posts with Claude

Posts summarizing research can be drafted from four kinds of source, each with a skill in `.claude/skills/`:

| Source | Skill | Trigger phrases |
|---|---|---|
| Paper PDF (published or preprint) | `post-from-pdf` | "let's do `<paper.pdf>`", "do the X paper" |
| Code repo or deployed dashboard | `post-from-repo` | "let's do `<repo URL or path>`", "draft a post from `<dashboard URL>`" |
| PowerPoint deck | `post-from-powerpoint` | "let's do `<deck.pptx>`", "draft a post from this presentation" |
| Researcher's own knowledge | `post-from-interview` | "interview me on X", "do the journalist thing" |

For PDFs, put the file in a `papers/` folder in the repo (gitignored) or anywhere outside the repo.

## Voice and style

These rules were developed for research-summary posts, and the skills apply them:

- Plain section headers (Motivation / What we did / What we found / Caveats), not editorial titles.
- No editorial commentary on methods or validation, and no "Takeaways for modelers" section.
- US spelling (modeling, not modelling).
- No em-dashes; use periods, commas, semicolons or parentheses.
- Spell out acronyms on first use, including in the TL;DR.
- First-person plural ("we") when the post's author is an author of the work; third person ("the team", "the authors") otherwise.
- Interview material informs the prose; don't quote it as callouts.
- Skip the headline figure rather than use a busy or multi-panel one.
- Say "Gates Foundation" rather than internal acronyms (IDM, IPM, etc.).
