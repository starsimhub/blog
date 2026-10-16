---
name: post-from-repo
description: Use when adding a post to the Starsim blog from a code repository (an analysis repo, a dashboard, an interactive tool, or an open-source model). Trigger phrases include "let's do <repo URL>", "do the X repo", "next is <repo path>", "draft a post from <dashboard URL>". The post describes a piece of work whose canonical artifact is the repository or its deployed instance. Reads the README, source files, configs, and any included figures; drafts the post following voice rules; iterates; previews the site, and commits.
---

# Creating a post from a repo or dashboard

Use this skill when the source artifact is a code repository, a deployed dashboard, or a research tool, with no accompanying paper as the primary venue. The post may still reference a parallel preprint or manuscript-in-preparation, but the repo is what people are pointed to.

## Pre-flight

1. Confirm the repo location with the user: a local clone (preferred, ask the user to clone if needed) or a deployed URL.
2. If it's a deployed URL with no local clone, ask the user to clone the repo so source files can be read. WebFetch alone is insufficient for client-rendered dashboards.
3. Read the voice rules in `CONTRIBUTING.md` ("Voice and style") if not already loaded.
4. Read one existing post (e.g. `posts/2026.03.15_vmb-hiv-ptb-south-africa/index.md` for a dashboard example) to absorb voice and structure.

## Workflow

### 1. Map the repo

- Read the `README.md` first. This is usually the most direct source of the project narrative.
- For dashboards: read the substantive UI components (e.g. an `Overview.jsx`, `KeyFindings.jsx`, `Methods.jsx`). The prose in these is often closer to publication-ready than the README.
- For analysis repos: read the top-level run script(s), the methods documentation, and any plotting scripts that suggest figures.
- For Starsim-based projects: identify which framework component (Starsim, HIVsim, HPVsim, FPsim, STIsim) and which modules are used. Note any custom modules.
- Identify authors from the README, package.json / pyproject.toml / setup.py, or any About section.

### 2. Decide authorship voice

- Check authorship. If the person you are working with is among the authors, use first-person plural ("we"). Otherwise third-person.

### 3. Choose a headline figure

If the repo includes a clean, single-takeaway figure (e.g. one panel from a methods diagram, a calibration plot), copy it to the post folder as `headline-figure.png`. Skip if the only options are busy multi-panel diagrams or screenshots that need a paragraph to interpret. See the voice rule on headline figures.

### 4. Create the post folder

`posts/YYYY.MM.DD_slug/`. Date defaults:
- For a deployed dashboard, use a date around when it went live.
- For a repo, use a date around the most recent meaningful release or, for unreleased work, today.

### 5. Draft the post

Same structure as `post-from-pdf` (frontmatter, TL;DR, key numbers, source, sections). Differences:

- The Source citation line should foreground the repo or dashboard URL, not a DOI.
- A `What this enables` or `Explore the dashboard` section near the end is appropriate for tool-style artifacts. The reader's call-to-action is to go interact with the artifact.
- If a parallel preprint or manuscript-in-preparation exists, mention it in the Source line but do not use it as the cite-able anchor.

### 6. Iterate

Show the draft path and flag two or three judgment calls. Apply edits. Re-show if substantive.

### 7. Preview and commit

```bash
quarto preview   # check the post renders and appears on the index page
git add posts/<folder>/
git commit -m "Add post: <short title>"
```

## Constraints

- The voice rules in `CONTRIBUTING.md` apply in full.
- For projects from inside the Gates Foundation, spell out as "Gates Foundation" rather than internal acronyms (IDM, IPM, etc.).
- The acronym-on-first-use rule applies. Common offenders in tool-style posts: API, UI, JSON, plus domain-specific acronyms baked into the artifact.
