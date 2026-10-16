---
name: post-from-pdf
description: Use when adding a post to the Starsim blog from a PDF (a published paper or preprint). Trigger phrases include "let's do <paper.pdf>", "do the X paper", "next paper is...", "draft a post from this PDF", or when the user points to a PDF. Reads the PDF, extracts citation metadata and a candidate headline figure, drafts the post following the voice rules, iterates with the user, previews the site, and commits.
---

# Creating a post from a PDF

Use this skill when the source artifact is a research paper PDF (published article or preprint).

## Pre-flight

1. Confirm the PDF path with the user if not already given. Typical location: a `papers/` folder in the repo (gitignored) or anywhere outside it.
2. Read the voice rules in `CONTRIBUTING.md` ("Voice and style") if not already loaded into the session.
3. Read one existing post (e.g. `posts/2026.01.22_hpv-single-dose-gavi/index.md`) to absorb voice and structure in context.

## Workflow

### 1. Extract metadata

Run `pdfinfo <path>` to read title, subject (often contains DOI and journal), author, page count. Then use the Read tool with `pages: "1-3"` to read the abstract, introduction, and any methods snippet visible on early pages. Read additional pages if results need surfacing.

### 2. Decide authorship voice

- Check the author list. If the person you are working with is among the authors, use first-person plural ("we") throughout the post. If not, use third-person ("the team", "the authors", "they").

### 3. Extract a candidate headline figure

Use PyMuPDF (`fitz`) to inspect embedded images on the results pages. Embedded JPEGs that span most of a page are usually whole-figure exports. Save as `headline-figure.png` in the post folder. **Prefer skipping over a busy or multi-panel figure** — see the voice rule on headline figures.

```python
import fitz
doc = fitz.open("<path>")
for i, page in enumerate(doc):
    for img in page.get_images():
        info = doc.extract_image(img[0])
        # save the highest-quality embedded image from the figure page
```

### 4. Create the post folder

`posts/YYYY.MM.DD_slug/` where the date matches the paper's publication date (back-fill convention). The slug is short kebab-case capturing the topic.

### 5. Draft the post

Standard structure:

- Frontmatter: `title`, `author` (the person writing the post), `date`, `description` (one-sentence summary for the index page), `categories` (keywords; reuse the spellings listed in `CONTRIBUTING.md`), and `image: headline-figure.png` if there is a headline figure. See `templates/post-template.md`. No H1: Quarto renders the title.
- Blockquoted TL;DR (3-5 sentences). Spell out acronyms on first use here.
- Headline figure (skip if no clean candidate).
- Bulleted Key numbers.
- Source citation line with DOI link.
- Sections: `Motivation`, `What we did` (or `What the team did`), `What we found` (or `What they found`), optional substantive section (`A lower bound, not an upper bound` / `Translation to clinical trial design` / `What this set in motion` etc.), `Caveats`, optional `Where this fits`.

### 6. Iterate

Show the draft path and call out two or three judgment calls (title framing, headline figure choice, any claim you're not sure about). Wait for the user's edits or feedback. Apply them and re-show if substantive.

### 7. Preview and commit

```bash
quarto preview   # check the post renders and appears on the index page
git add posts/<folder>/
git commit -m "Add post: <short title>"
```

## Constraints to apply

- Strict adherence to the voice rules in `CONTRIBUTING.md`. Most-violated rules to double-check before showing the draft: no em-dashes (replace with periods, commas, semicolons, parentheses); US spelling (modeling not modelling, etc.); spell out acronyms on first use; plain section headers (no flavored editorial titles); no "Takeaways for modelers" section; no editorial commentary on methods/validation.
- The companion skill for the journalist-interview pattern is `post-from-interview`. Use that if the paper is yours and you have backstory worth surfacing that isn't in the manuscript.
