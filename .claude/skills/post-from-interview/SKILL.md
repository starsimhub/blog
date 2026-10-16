---
name: post-from-interview
description: Use when adding a post to the Starsim blog where the source is a researcher's lived knowledge of a piece of work, not a fully-written artifact. Trigger phrases include "interview me on X", "do the journalist thing", "let's do <project> as an interview", "the post should span multiple papers", or when the user wants context, backstory, or framing that isn't in any paper or repo. Conducts a one-question-at-a-time interview, then drafts from the answers plus any supporting artifacts the user provides. Iterates, previews the site, and commits.
---

# Creating a post from an interview

Use this skill when the source for a post is the researcher's own knowledge and the post will rely on a structured interview to surface the substantive content. Common cases:

- Work that spans more than one paper, where a single-artifact summary would not capture the through-line.
- Foundational work where the backstory (motivation, false starts, unpublished bits) is the most interesting part.
- Unpublished work where there is no parallel paper, dashboard, or repo to read.
- A piece of work where the researcher wants a more narrative, contextual treatment than a paper summary would produce.

## Pre-flight

1. Read the voice rules in `CONTRIBUTING.md` ("Voice and style") if not already loaded.
2. Read one existing post (e.g. `posts/2024.08.21_fpsim-empowerment-kenya/index.md`, which used the interview pattern) to absorb voice and structure.
3. Ask the user for any supporting artifacts (PDFs, repo paths, decks) that should inform the interview questions. Read them briefly before starting so questions can probe what is and isn't in the artifacts.

## Interview ground rules (state these up front)

Tell the user:
> "Journalist mode on. Loose ground rules:
> - I'll ask one question at a time, open-ended (no multiple choice)
> - Ramble as much as you want; I'll synthesize and ask follow-ups
> - Some questions will be ones I already half-know from the artifacts; the answer I'm fishing for is what *isn't* in them (motivation, surprises, dead ends, what got cut)
> - Tell me when you've had enough and I'll write the draft"

## Interview flow

Typical question arc (adapt to the work):

1. **Origin.** How did this work come together? Who initiated, who joined, what motivated it, when did it start?
2. **Methods novelty or methodological challenge.** What's hard or new about how it was done? What's the unlock — a data source, a tool, a theoretical move?
3. **Findings and surprises.** What did the analysis show? Was there a moment where a result landed differently than expected?
4. **Audience and impact.** Who is this for? Has it been used, cited, picked up by other researchers, mentioned in media, informed policy?
5. **What's next.** Forward-looking. What's the line of work that grows from this?
6. **One open question to close.** "Anything that didn't make the paper / artifact that you want in the post? A finding that got cut, a credit you want to give, a frustration that's useful to flag?"

Ask one question per message. Use the previous answer to shape the next question. Do not stack multiple questions in one message.

## After the interview

### 1. Decide voice

- Authorship voice: first-person plural ("we") if the person you are working with is among the work's authors, third-person otherwise.

### 2. Draft the post

Same structure as the other skills (frontmatter, TL;DR, key numbers, source, sections). Differences for interview-sourced posts:

- The Motivation section often benefits from the interview's origin story, distilled into context rather than narrated as "we wrote this paper because…" framing.
- Interview material informs the prose. Do not quote it directly as callouts. The voice rule on "no personal reflection quotes" applies.
- For multi-paper or programme-of-work posts, the Source block can list multiple papers; treat the most-recent or most-canonical one as the primary citation and reference the others in the body.

### 3. Iterate, preview, commit

```bash
quarto preview   # check the post renders and appears on the index page
git add posts/<folder>/
git commit -m "Add post: <short title>"
```

## Constraints

- The voice rules in `CONTRIBUTING.md` apply in full.
- Resist the temptation to overuse interview material. Lots of fascinating backstory gets cut in a good post; the bar is "is this load-bearing for the reader's understanding?"
- The output is not a transcript. Synthesize, don't paraphrase line by line.
