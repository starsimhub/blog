---
name: post-from-powerpoint
description: Use when adding a post to the Starsim blog from a PowerPoint deck (an internal presentation, conference talk, working-group slides). Trigger phrases include "let's do <deck.pptx>", "do the X talk", "draft a post from this presentation", or when the user points to a .pptx file. Extracts slide text and embedded images, identifies the storyline, drafts the post following voice rules, iterates, previews the site, and commits.
---

# Creating a post from a PowerPoint deck

Use this skill when the source artifact is a PowerPoint presentation. The deck typically represents work that has been shared internally or at a conference but is not yet (or may never be) published as a paper.

## Pre-flight

1. Confirm the .pptx path with the user.
2. Ensure `python-pptx` and `Pillow` are available. If not: `pip install python-pptx Pillow`.
3. Read the voice rules in `CONTRIBUTING.md` ("Voice and style") if not already loaded.
4. Read one existing post to absorb voice and structure.

## Workflow

### 1. Extract slide text and images

```python
from pptx import Presentation
from pptx.util import Emu

prs = Presentation("<path>")
for i, slide in enumerate(prs.slides, 1):
    title = ""
    body_texts = []
    for shape in slide.shapes:
        if shape.has_text_frame:
            for para in shape.text_frame.paragraphs:
                txt = "".join(run.text for run in para.runs)
                if txt.strip():
                    body_texts.append(txt)
        if shape.shape_type == 13:  # picture
            image = shape.image
            # save image with slide index for inspection
    print(f"--- Slide {i} ---")
    print("\n".join(body_texts))
```

For each slide, capture title (usually the first paragraph or the title placeholder), bullet body text, and references to embedded images. Save images to a temp directory for inspection.

### 2. Identify the storyline

Slide-deck storylines typically follow: title / authors → problem framing → methods → results (multiple slides) → conclusions / next steps. Identify the slides that carry each beat. Note any speaker-notes (`slide.notes_slide.notes_text_frame.text`) since those often contain the prose that didn't make it onto the slide itself.

### 3. Decide authorship voice

- Check authorship from the title slide. If the person you are working with is among the authors, use first-person plural. Otherwise third-person.

### 4. Choose a headline figure

If the deck contains a clean single-takeaway figure (one chart, one panel), extract and save it as `headline-figure.png` in the post folder. Skip if every figure is annotated, multi-panel, or only legible with the speaker's commentary.

### 5. Create the post folder

`posts/YYYY.MM.DD_slug/`. The date should match when the deck was presented (often in the filename, e.g. `2025-07-14_FPsim.pptx`).

### 6. Draft the post

Same structure as `post-from-pdf`. Differences for deck-sourced posts:

- The TL;DR should make clear that the source is an unpublished presentation. Phrase: "This post summarizes a [conference / internal] talk from [event], YYYY."
- The Source line should cite presenter, year, title, occasion (if known). No DOI to link; the artifact itself usually isn't shareable, so omit a public URL unless one exists.
- Caveats section should flag that the work is from a presentation and may not represent the final analysis.

### 7. Iterate, preview, commit

```bash
quarto preview   # check the post renders and appears on the index page
git add posts/<folder>/
git commit -m "Add post: <short title>"
```

## Constraints

- The voice rules in `CONTRIBUTING.md` apply in full. Common slide-deck habits to translate out of: bullet-heavy phrasing (rewrite as full sentences), capitalized headings (use sentence case in markdown), single-word labels next to numbers (expand into a clause).
- Acronyms on slides are extremely common and often undefined. Apply the acronym-on-first-use rule strictly when prose-ifying.
- Speaker-notes content is often more useful than slide text and should be mined before drafting.
