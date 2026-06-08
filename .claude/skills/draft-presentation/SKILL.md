---
name: draft-presentation
description: Interactively create a structured presentation draft, then generate slides via marp-slides. Triggers on 'draft presentation', 'presentation draft', 'create presentation'.
---

# draft-presentation

Create a structured presentation draft together with the user, then hand it off to marp-slides for generation.

## Phase 1 — Upfront Context (ask all at once, one message)

Ask the user these questions in a single message before touching any slide content:

1. **Topic & thesis** — What is the topic, and what is the single most important thing the audience should leave with?
2. **Audience** — Who are they, and what do they already know? (e.g. "CS students who know crypto basics but not kernel internals")
3. **Duration** — How many minutes? (determines slide count: ~1 slide/min as baseline)
4. **Language** — Which language should the slides be in?
5. **Assessment criteria** — Are there grading rubrics, mandatory slide elements (name, date, institution), or formatting rules?
6. **Research material** — Is there a research document, deep-research output, or set of sources to base the draft on? If yes, ask the user to share it or describe the key facts. **Recommend** that if they haven't done research yet, they run `/deep-research` first and ask Claude to save the full report to a `.md` file (e.g. `research.md`) — that file becomes the knowledge base for this skill.
7. **Theme** — Dark background with light text, or light background with dark text? (If assessment criteria specify a style, skip this question and follow the criteria.)
8. **Speaker notes** — What level of detail for speaker notes? Options: *None* (omit speaker notes entirely), *Bullet points* (short cues per talking point), *Full text* (complete spoken sentences as if speaking), or *Other* (describe what you need).
9. **Existing structure** — Does the user have a rough outline, or should you propose one?

Do not proceed to Phase 2 until you have answers to at least 1–5. Question 8 (speaker notes format) must also be answered before drafting any slide.

---

## Phase 2 — Propose Slide Overview (one message)

Based on the answers, propose a numbered slide list with:
- Slide number
- One-line topic description (what this slide covers, not a title yet)
- Estimated time allocation

Keep it to a single message. Ask the user to approve, reorder, add, or remove slides before proceeding. Do not write any slide content yet.

---

## Phase 3 — Slide-by-Slide Drafting (iterative)

Work through each slide one at a time:

1. Draft the slide spec (see **Draft Format** below)
2. Present it to the user
3. Incorporate feedback before moving to the next slide
4. Only move to the next slide when the user explicitly approves the current one

After every 3–4 slides, offer a brief "so far" recap so the user can catch structural drift early.

---

## Draft Format

Each slide must be written in exactly this format (matching `presentation_draft.md`):

```
## Folie N — [Slide Title]

**Kernaussage der Folie:** One sentence: what should the audience understand after this slide?

**Inhalt (was auf der Folie steht):**
[Describe visual layout: diagrams, code blocks, two-column splits, tables. No prose paragraphs.]

**Präsentationsformat:** [One line describing the visual structure, e.g. "Sequenzdiagramm, drei Akteure, farbkodiert"]

**Sprechernotizen:**
> "[Full spoken text the presenter would say — complete sentences, no bullet points, as if speaking]"

**Übergang:** "[One sentence the presenter says to bridge to the next slide]"
```

The final draft file must also include:
- A header block with: Vortragsdauer, Sprache, Zielgruppe, Aufbau, Folienanzahl
- A Zeitplan table (slide ranges → topic → time)
- A Design-Hinweise section for the MARP generator

---

## Slide Title Rules (critical)

The `## Folie N —` title is the **only navigation anchor** the audience sees. It must be:

**✓ Correct patterns:**
- Describes the specific content of this slide
- Stands alone — readable without presenter explanation
- Uses noun phrases or short declarative statements
- Example: `## Folie 6 — Write-before-Verify: Exploit-Sequenz über algif_aead und Page Cache`
- Example: `## Folie 3 — AEAD: Verschlüsselungsgarantie und Seiteneffekt-Problem`

**✗ Forbidden patterns:**
- Opener/teaser titles that leave a question open: `## Folie 1 — Hook: 732 Bytes → root`
- Wordplay or puns used as the title: `## Folie 2 — Name ist Programm`
- Titles that sound like a section header in an essay, not a slide: `## Folie 11 — Analyse`
- Titles that are too long and all follow the same `Topic: Subtitle elaborating but still not specific` pattern
- Bad example: `Was CVE-2026-31431 für Systemdesign bedeutet`. Better: `Bedeutung für Systemdesign`

The title should answer "what does this slide show?" — not "what topic area does it belong to?"

---

## Content Quality Rules

**Scientific, not journalistic:**
- Titles and labels are noun phrases or factual statements, never hooks, rhetorical questions, or slogans
- Avoid: "Fast entwaffnend einfach", "Was lernen wir?", "Die entscheidende Erkenntnis"
- Prefer: "Patch a664bf3d603d: Trennung von Quell- und Ziel-Scatterlist", "Erkenntnisse aus CVE-2026-31431"

**Self-contained slides:**
- A viewer looking at the slide without the presenter's spoken text must understand what the slide is showing
- Visual elements (diagrams, code, tables) carry the content — not decorative
- Every diagram label, axis, and color must be explained on the slide itself

**Coherence across slides:**
- Each slide must answer a question raised by the previous one, or introduce a question the next slide answers
- The logical thread should be traceable from slide titles alone — read them in sequence; if the progression is unclear, restructure

**Avoid AI-generated presentation patterns:**
- No "Let's explore...", "As we can see...", "In summary..." in slide text
- No bullet lists of 5+ identical-weight items — collapse into a diagram or table
- No slide that is purely a repetition of the previous slide's content as "takeaways"

**Avoid AI writing giveaways in all text (slide content, titles, speaker notes):**
- No em dashes (—) — use a comma, period, or restructure the sentence
- No "It's not X, but Y" contrasts — state the positive claim directly
- No snappy one-liner sentences standing alone for effect — integrate the point into context
- No emojis. Icons are allowed if they serve a clear communicative purpose, but avoid decorative or "cute" icons that don't add information.
- No buzzword inflation: avoid "leverage", "delve into", "pivotal", "game-changing", "robust", "holistic", "nuanced", "transformative" — use the plain word that names the actual thing
- No symmetrical bullet points where every item has the same syntactic structure and length — real observations vary
- No rhetorical question hooks: "But what does this really mean?", "So how do we solve this?" — state the answer, not the question
- No throat-clearing phrases: "It's worth noting that...", "It should be mentioned...", "Let me walk you through..." — start with the substance
- No artificial enthusiasm: "fascinating", "powerful", "remarkable", "exciting" without specific evidence — let the facts carry the weight

**Speaker notes follow the format chosen in Phase 1:**
- *None*: omit the `**Sprechernotizen:**` block entirely
- *Bullet points*: short keyword cues per talking point, no full sentences
- *Full text*: written as if the presenter is speaking, in the target language; must contain every fact, nuance, and connection not visible on the slide; must include the transition sentence to the next slide
- *Other*: follow the user's custom description

---

## Phase 4 — Finalize and Hand Off

Once all slides are approved:

1. Write the complete draft to a file named `presentation_draft.md` in the current working directory (overwrite if it exists)
2. Confirm to the user what was written
3. Invoke the `marp-slides` skill to generate the actual slides from the draft

When invoking marp-slides, tell it explicitly:
- The draft file is `presentation_draft.md`
- The output file should be `presentation.md`
- The design uses the theme the user specified in Phase 1 (dark or light); if unspecified, ask before invoking
- Slide titles use the `### Gliederungspunkt` / `# Folientitel` two-level pattern where `###` names the section and `#` states the slide's specific content as a noun phrase — both derived directly from the `## Folie N — Title` in the draft

---

## Phase 5 — Visual QA

After `marp-slides` finishes generating `presentation.md`, tell the user to run this command to export each slide as a PNG image:

```
npx @marp-team/marp-cli --html --images png slides.md 2>&1
```

Then tell them to open a **new chat** and paste the following prompt:

> The images slides.x.png all correspond to a slide of a presentation. Check for visual flaws:
> - Text being too small
> - Anything overlapping
> - An element being too close to another element, or too close to the sides / bottom
> - Text wrapping to next lines incorrectly
