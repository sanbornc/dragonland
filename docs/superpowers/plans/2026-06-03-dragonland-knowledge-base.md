# Dragonland Knowledge Base Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Scaffold a shared, Claude-Code-driven markdown knowledge base for the Dragonland D&D campaign and seed it with the six recovered "Ringmaster's Ledger" documents.

**Architecture:** A plain git repo of markdown. Per-session logs feed a curated, one-file-per-entity wiki. Claude Code is the only interface members need; `CLAUDE.md` encodes the workflow (pull-before-answer, distill-into-wiki, commit-and-push). In-world document images are transcribed to markdown and stored as compressed JPEGs; full-resolution originals are git-ignored.

**Tech Stack:** Markdown, git, macOS `sips` (built-in) for image compression.

**Note on method:** This is a content/structure project, not testable code. "Verification" steps confirm files exist, git is clean, and images are under target size — there are no unit tests. Every file's full content is given inline; execution is mechanical.

---

## File structure (created by this plan)

```
dragonland/
├── README.md                      Task 2  — human-facing intro
├── CLAUDE.md                      Task 3  — Claude workflow contract (the engine)
├── OVERVIEW.md                    Task 4  — campaign at a glance
├── .gitignore                     Task 1
├── sessions/
│   ├── README.md                  Task 1
│   └── TEMPLATE.md                Task 1
├── wiki/
│   ├── README.md                  Task 1
│   ├── characters/README.md       Task 1
│   ├── quests/README.md           Task 1
│   ├── npcs/                       Task 7  — the-ringmaster, the-old-knight, the-buyer, the-queen
│   ├── locations/                  Task 7  — haven, solace, x-t, the-old-road
│   ├── factions/                   Task 7  — black-banners
│   ├── items/                      Task 7  — the-staff, the-ringmasters-ledger
│   ├── lore/                       Task 7  — miracle-bearers, the-gathering-war
│   └── documents/                  Task 6  — six ledger transcriptions
└── assets/
    └── documents/                  Task 5  — six compressed JPEGs
```

Document slugs (used for both `wiki/documents/<slug>.md` and `assets/documents/<slug>.jpg`):

| Source | Slug |
|---|---|
| IMG_6014.png | `ringmasters-ledger-special-cargo` |
| IMG_6015.png | `ringmasters-ledger-roads-payments` |
| IMG_6016.png | `ringmasters-ledger-back-cover-list` |
| IMG_6017.png | `ringmasters-ledger-map-scrap` |
| IMG_6018.png | `arcane-schematic-wheel` |
| IMG_6020.png | `regional-map-burned` |

---

## Task 1: Scaffold directories, .gitignore, and folder guides

**Files:**
- Create: `.gitignore`
- Create: `sessions/README.md`, `sessions/TEMPLATE.md`
- Create: `wiki/README.md`, `wiki/characters/README.md`, `wiki/quests/README.md`
- Create (empty dirs via the files above + the seeded files in later tasks): `wiki/npcs/`, `wiki/locations/`, `wiki/factions/`, `wiki/items/`, `wiki/lore/`, `wiki/documents/`, `assets/documents/`

- [ ] **Step 1: Create the directory tree**

```bash
mkdir -p sessions wiki/characters wiki/npcs wiki/locations wiki/factions \
  wiki/items wiki/quests wiki/lore wiki/documents assets/documents
```

- [ ] **Step 2: Write `.gitignore`**

```
# Full-resolution source images — keep compressed copies in assets/ instead
IMG_*.png

# OS cruft
.DS_Store
```

- [ ] **Step 3: Write `sessions/README.md`**

```markdown
# Sessions

One file per game session, in chronological order.

**Filename:** `NN-YYYY-MM-DD-short-title.md` — `NN` is a zero-padded sequence
number (`01`, `02`, …) so files sort in play order.

A session log is the raw "what happened." After writing one, the new facts get
distilled into the `wiki/`. Use `TEMPLATE.md` as a starting point — or just tell
Claude what happened and it will write the log for you.
```

- [ ] **Step 4: Write `sessions/TEMPLATE.md`**

```markdown
---
session: NN
date: YYYY-MM-DD
players-present: []
---

# Session NN — <title>

## Recap

<What happened, in narrative order.>

## New facts learned

- <fact> — link the wiki entry it updates

## Loose threads

- <unresolved question or hook>
```

- [ ] **Step 5: Write `wiki/README.md`**

```markdown
# Wiki

Curated, canonical knowledge about Dragonland — distilled from session logs and
recovered documents. **One file per entity**, kebab-case filename
(e.g. `npcs/the-ringmaster.md`).

## Folders

- `characters/` — the party's own player characters (PCs)
- `npcs/` — non-party characters
- `locations/` — towns, roads, sites, regions
- `factions/` — groups, armies, organizations
- `items/` — notable objects and relics
- `quests/` — active goals and open threads
- `lore/` — world history, religion, rumors
- `documents/` — transcriptions of recovered in-world documents (images live in
  `../assets/documents/`)

## Entry format

```
---
name: Black Banners
aliases: []
type: faction
tags: [war, military]
first-learned: recovered documents
---

## What we know
- A fact — with a link to the session or document it came from.

## Open questions
- Something we don't know yet.

## See also
- `wiki/...` links to related entries.
```

Everything here is knowledge our characters have actually learned in play.
There are no secrets or DM-only material.
```

- [ ] **Step 6: Write `wiki/characters/README.md`**

```markdown
# Player Characters

Add each PC as `wiki/characters/<name>.md`. Suggested contents: name, player,
ancestry/class, a short description, goals, and notable relationships. List the
party in `OVERVIEW.md` too.
```

- [ ] **Step 7: Write `wiki/quests/README.md`**

```markdown
# Quests & Open Threads

Track active goals and unresolved threads here, one file per quest
(`wiki/quests/<slug>.md`), or keep a running `open-threads.md`. Note where each
thread came from and its current status (active / resolved / abandoned).
```

- [ ] **Step 8: Verify the structure**

Run: `find sessions wiki assets -type f | sort`
Expected: the seven files above, and the directories all exist.

- [ ] **Step 9: Verify the originals are ignored**

Run: `git status --porcelain | grep IMG_ || echo "IMG files correctly ignored"`
Expected: prints `IMG files correctly ignored` (the PNGs do not appear as untracked).

- [ ] **Step 10: Commit**

```bash
git add .gitignore sessions wiki
git commit -m "Scaffold knowledge base structure and folder guides"
```

---

## Task 2: Human-facing README

**Files:**
- Create: `README.md`

- [ ] **Step 1: Write `README.md`**

```markdown
# Dragonland

The shared knowledge base for our **Dragonland** D&D campaign — a record of what
we've learned, session by session, and everything we know about the world.

## How to use it

You don't need to know git or markdown. Just:

1. Clone the repo once: `git clone git@github.com:sanbornc/dragonland.git`
2. Open the folder in **Claude Code** and talk to it in plain language:
   - *"What do we know about the black banners?"*
   - *"Here's what happened last session: …"* — Claude writes it up.
   - *"Transcribe this document"* — drop in a photo of a handout.
3. Claude pulls the latest, finds or records the answer, and pushes any changes
   back so everyone stays in sync.

Anyone in the group can both ask questions and contribute.

## What's inside

- **`sessions/`** — recaps of each game session.
- **`wiki/`** — what we know: characters, NPCs, locations, factions, items,
  quests, lore, and recovered documents.
- **`OVERVIEW.md`** — the campaign so far at a glance.
- **`assets/`** — images of documents we've recovered.

Everything here is knowledge our characters have actually learned in play —
no secrets, no spoilers.
```

- [ ] **Step 2: Verify and commit**

Run: `test -f README.md && echo OK`
Expected: `OK`

```bash
git add README.md
git commit -m "Add human-facing README"
```

---

## Task 3: CLAUDE.md — the workflow contract

**Files:**
- Create: `CLAUDE.md`

- [ ] **Step 1: Write `CLAUDE.md`**

````markdown
# Dragonland — Knowledge Base Guide (for Claude)

This repository is the shared knowledge base for the **Dragonland** D&D
campaign. Group members use Claude Code to record what they learn each session
and to ask questions about the world. Everyone can both contribute and ask.

**Everything here is player-known knowledge — there are no secrets, no DM-only
material, and no spoilers to protect.** Answer freely from anything in the repo.

## Golden rule: always work from the latest

Before answering any question or making any edit, run `git pull` to make sure
you have everyone's latest contributions. If the pull reports conflicts, stop
and surface them to the user rather than guessing.

## How knowledge is organized

- `sessions/` — one file per game session, chronological. The raw "what
  happened." Filename `NN-YYYY-MM-DD-short-title.md`.
- `wiki/` — curated, canonical knowledge distilled from sessions and documents.
  One file per entity: `characters/` (party PCs), `npcs/`, `locations/`,
  `factions/`, `items/`, `quests/` (open threads), `lore/`, and `documents/`
  (transcriptions of recovered in-world documents).
- `assets/documents/` — compressed images of recovered documents, paired with
  their `wiki/documents/` transcription.
- `OVERVIEW.md` — campaign premise, party roster, and current situation.

## Answering questions

1. Pull latest first.
2. Search `sessions/` and `wiki/` for relevant material.
3. Answer using only what the repo records. Cite the source file(s) you drew
   from (e.g. `wiki/factions/black-banners.md`).
4. If something isn't recorded yet, say so plainly — never invent campaign facts.

## Recording a session

When a member describes what happened:
1. Pull latest.
2. Create or append the next session file `sessions/NN-YYYY-MM-DD-short-title.md`
   using `sessions/TEMPLATE.md`.
3. Distill the new facts into the relevant `wiki/` entries — update existing
   ones or create new ones. Each fact should note where it was learned (session
   number or source document).
4. Cross-link related entries under a "See also" section.
5. Commit with a clear message and push.

## Transcribing a recovered document (image)

1. Read the image.
2. Create `wiki/documents/<slug>.md`: a faithful transcription of the legible
   text, a note on what is burned/illegible/redacted in-fiction, and a link to
   the compressed image.
3. Compress the source image into `assets/documents/<slug>.jpg`:
   `sips -s format jpeg -s formatOptions 72 -Z 2400 <source> --out assets/documents/<slug>.jpg`
   Do not commit the full-resolution original (the repo `.gitignore`s `IMG_*.png`).
4. Surface any clues into the relevant `wiki/` entries.
5. Commit and push.

## Wiki entry conventions

- One file per entity, kebab-case filename.
- Light frontmatter:
  ```
  ---
  name: Black Banners
  aliases: []
  type: faction
  tags: [war, military]
  first-learned: recovered documents
  ---
  ```
- Body: a `## What we know` list (each point traceable to a session or
  document), an optional `## Open questions`, and a `## See also` with links.

## Committing & syncing

There are no git hooks — syncing is your responsibility on every contribution.
After any change, commit with a clear, specific message and `git push`. Keep
commits focused (one session or one topic each, where practical). Always
`git pull` again before pushing if time has passed.

## Tone

Preserve the in-fiction flavor of recovered documents. Keep wiki entries factual
and concise; distinguish what the party knows from what they suspect.
````

- [ ] **Step 2: Verify and commit**

Run: `test -f CLAUDE.md && echo OK`
Expected: `OK`

```bash
git add CLAUDE.md
git commit -m "Add CLAUDE.md workflow contract"
```

---

## Task 4: OVERVIEW.md — campaign at a glance

**Files:**
- Create: `OVERVIEW.md`

- [ ] **Step 1: Write `OVERVIEW.md`**

```markdown
# Dragonland — Campaign Overview

> The campaign so far, at a glance. A living document — updated as we learn more.

## Premise

The party moves through a land on the edge of war. Armies are gathering in the
east under unmarked **black banners**; a **Queen**'s forces are expected to
march; and a traveling circus run by **the Ringmaster** has been smuggling
"special cargo" along the old roads. Recovered pages from the Ringmaster's
locked wagon hint at trafficked "miracle-bearers," buried relics, and a
sought-after **Staff** that "must not reach X.T."

## The Party

Add your characters in `wiki/characters/` and list them here as they're created.

## Where we are now

No sessions logged yet. This section will summarize the current situation once
the first session is recorded.

## Major threads

- **The gathering war** — unmarked black-banner armies massing eastward.
  See `wiki/factions/black-banners.md`.
- **The Ringmaster's cargo** — smuggled "miracle-bearers" and hidden relics.
  See `wiki/npcs/the-ringmaster.md`.
- **The Staff and X.T.** — a Staff that must not reach the unspoken site "X.T."
  See `wiki/items/the-staff.md` and `wiki/locations/x-t.md`.

## Key places

- **Haven** — `wiki/locations/haven.md`
- **Solace** — `wiki/locations/solace.md`
- **X.T.** (unspoken site) — `wiki/locations/x-t.md`
```

- [ ] **Step 2: Verify and commit**

Run: `test -f OVERVIEW.md && echo OK`
Expected: `OK`

```bash
git add OVERVIEW.md
git commit -m "Add campaign overview"
```

---

## Task 5: Compress the six source images

**Files:**
- Create: `assets/documents/ringmasters-ledger-special-cargo.jpg` (from `IMG_6014.png`)
- Create: `assets/documents/ringmasters-ledger-roads-payments.jpg` (from `IMG_6015.png`)
- Create: `assets/documents/ringmasters-ledger-back-cover-list.jpg` (from `IMG_6016.png`)
- Create: `assets/documents/ringmasters-ledger-map-scrap.jpg` (from `IMG_6017.png`)
- Create: `assets/documents/arcane-schematic-wheel.jpg` (from `IMG_6018.png`)
- Create: `assets/documents/regional-map-burned.jpg` (from `IMG_6020.png`)

- [ ] **Step 1: Confirm `sips` is available**

Run: `which sips && echo OK`
Expected: a path (e.g. `/usr/bin/sips`) then `OK`. (`sips` ships with macOS.)

- [ ] **Step 2: Compress each image (resize to max 2400px, JPEG quality 72)**

```bash
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6014.png --out assets/documents/ringmasters-ledger-special-cargo.jpg
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6015.png --out assets/documents/ringmasters-ledger-roads-payments.jpg
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6016.png --out assets/documents/ringmasters-ledger-back-cover-list.jpg
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6017.png --out assets/documents/ringmasters-ledger-map-scrap.jpg
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6018.png --out assets/documents/arcane-schematic-wheel.jpg
sips -s format jpeg -s formatOptions 72 -Z 2400 IMG_6020.png --out assets/documents/regional-map-burned.jpg
```

- [ ] **Step 3: Verify sizes are reasonable**

Run: `ls -lh assets/documents/ && du -sh assets/documents/`
Expected: six `.jpg` files, each well under ~2MB; total folder a few MB (down from ~96MB of originals). If any file exceeds ~2MB, re-run that one with `-Z 2000` and/or `-s formatOptions 65`.

- [ ] **Step 4: Commit**

```bash
git add assets/documents
git commit -m "Add compressed images of the six recovered documents"
```

---

## Task 6: Transcribe the six documents

**Files:**
- Create: `wiki/documents/ringmasters-ledger-special-cargo.md`
- Create: `wiki/documents/ringmasters-ledger-roads-payments.md`
- Create: `wiki/documents/ringmasters-ledger-back-cover-list.md`
- Create: `wiki/documents/ringmasters-ledger-map-scrap.md`
- Create: `wiki/documents/arcane-schematic-wheel.md`
- Create: `wiki/documents/regional-map-burned.md`

> Transcriptions below reflect the legible text in each image. Square brackets
> mark text that is burned, struck through, or otherwise uncertain in-fiction —
> preserve them. If, when reading the actual image, more text is legible than
> noted here, add it; do not remove the uncertainty markers.

- [ ] **Step 1: Write `wiki/documents/ringmasters-ledger-special-cargo.md`**

```markdown
---
name: "Ringmaster's Ledger — Special Cargo / Things Not To Say"
type: document
source-image: ../../assets/documents/ringmasters-ledger-special-cargo.jpg
condition: burned, ink lost in places
first-learned: recovered documents
---

# Ringmaster's Ledger — Special Cargo / Things Not To Say

> *Loose page recovered from the locked wagon.*

**Page marked: SPECIAL CARGO / THINGS NOT TO SAY**

- Cargo [travels] best before war. After marching starts, every mother watches the road.
- Miracle-bearers draw eyes. Old gods, new lies, same price if delivered breathing.
- If questioned in Haven, no [—], no blue light, no singing under the floorboards.
- If the old knight returns, say the wagons went west. He believed me once.
- Name of [patron — ink burned through]

*[ink burned through here]*

*do not let the performers read this page*

> *In-fiction note: the "performers" are the Ringmaster's own circus performers,
> not anyone at our table.*

## Clues surfaced to the wiki
- `wiki/lore/miracle-bearers.md` · `wiki/npcs/the-old-knight.md`
- `wiki/locations/haven.md` · `wiki/npcs/the-ringmaster.md`
```

- [ ] **Step 2: Write `wiki/documents/ringmasters-ledger-roads-payments.md`**

```markdown
---
name: "Ringmaster's Ledger — Roads / Payments / Witnesses"
type: document
source-image: ../../assets/documents/ringmasters-ledger-roads-payments.jpg
condition: burned at edges, ink spotting
first-learned: recovered documents
---

# Ringmaster's Ledger — Roads / Payments / Witnesses

> *Loose page recovered from the locked wagon.*

**Page marked: ROADS / PAYMENTS / WITNESSES**

- Road [unwatched]. Not safe. Not by king's men. Not by bandits.
- North road follows the river toward Selace. Too visible by daylight.
- Haven listens for coin, prayer, and fear. Keep wagons quiet there.
- Armies gathering eastward. Black banners. No heraldry I know. Too many boots for a border dispute.
- Do not sell to soldiers unless paid in silver first. They promise protection like wolves promise mercy.

*[sketch of a flag, labelled] black banners*

*black banners seen after dusk*

## Clues surfaced to the wiki
- `wiki/factions/black-banners.md` · `wiki/locations/haven.md`
- `wiki/locations/the-old-road.md` · `wiki/locations/solace.md`
- `wiki/lore/the-gathering-war.md`
```

- [ ] **Step 3: Write `wiki/documents/ringmasters-ledger-back-cover-list.md`**

```markdown
---
name: "Ringmaster's Ledger — Back-Cover List"
type: document
source-image: ../../assets/documents/ringmasters-ledger-back-cover-list.jpg
condition: stained, edges burned
first-learned: recovered documents
---

# Ringmaster's Ledger — Back-Cover List

> *Loose page recovered from the locked wagon.*

**Small handwritten list in the back cover**

- Watch Solace. Too many paths meet under innocent roofs.
- Knights seen here. Not all relics stay buried.
- The Queen does not need my circus once the armies march.
- If the wagon burns, save the ledger before the masks.
- The world is a stage, but even stages have masters in the dark.

*— The Ringmaster*

## Clues surfaced to the wiki
- `wiki/locations/solace.md` · `wiki/npcs/the-queen.md`
- `wiki/items/the-ringmasters-ledger.md` · `wiki/npcs/the-ringmaster.md`
```

- [ ] **Step 4: Write `wiki/documents/ringmasters-ledger-map-scrap.md`**

```markdown
---
name: "Ringmaster's Ledger — Torn Map Scrap / Old Road"
type: document
source-image: ../../assets/documents/ringmasters-ledger-map-scrap.jpg
condition: torn, ink ruined by rain
first-learned: recovered documents
---

# Ringmaster's Ledger — Torn Map Scrap / Old Road

> *Loose page recovered from the locked wagon.*

**Torn map scrap / old road**

- Solace [struck-out word] -> ?
- [circled:] X.T.
- [Side] of Solace? swamp road? old stones?
- Old [site] below the green. Do not speak the full name.
- The Staff must not reach X.T. The buyer said it twice.

*[rough sketch: lines connecting* **Solace** *and* **Haven** *to a marked point* **X.T.***]*

*route uncertain — ink ruined by rain*

## Clues surfaced to the wiki
- `wiki/items/the-staff.md` · `wiki/locations/x-t.md`
- `wiki/locations/solace.md` · `wiki/locations/haven.md`
- `wiki/npcs/the-buyer.md`
```

- [ ] **Step 5: Write `wiki/documents/arcane-schematic-wheel.md`**

```markdown
---
name: "Arcane Schematic — Eight-Armed Wheel"
type: document
source-image: ../../assets/documents/arcane-schematic-wheel.jpg
condition: aged vellum, cracked; marginal notes mostly illegible
first-learned: recovered documents
---

# Arcane Schematic — Eight-Armed Wheel

> *A detailed ink drawing on aged vellum.*

A finely drawn, radial **eight-armed wheel** or mechanism dominates the page —
a central hub with eight ornate spokes/arms radiating outward, in the style of a
technical or alchemical diagram. Surrounding it are smaller component studies
(individual arms, fittings, and devices) and dense marginal annotations.

The annotations are **largely illegible** at this resolution / condition. No
title or caption is clearly readable.

## Open questions
- What is this device? Is it connected to **the Staff**, to "relics" said to be
  buried near Solace, or to something else entirely?

## See also
- `wiki/items/the-staff.md`
```

- [ ] **Step 6: Write `wiki/documents/regional-map-burned.md`**

```markdown
---
name: "Burned Regional Map"
type: document
source-image: ../../assets/documents/regional-map-burned.jpg
condition: heavily burned/charred around the edges; some labels illegible
first-learned: recovered documents
---

# Burned Regional Map

> *A large, heavily burned regional map.*

A regional map showing mountains, forest, and water, with a compass rose toward
the lower-left. Several place-names are written in a faint hand; legible among
them are **Haven** (toward the center-left), **Solace** (center-right), and
**X.T.** (toward the lower-right). Other labels are illegible due to charring.

The relative positions match the torn map scrap: Haven and Solace lie to the
west/north of the marked **X.T.** site.

## See also
- `wiki/locations/haven.md` · `wiki/locations/solace.md` · `wiki/locations/x-t.md`
- `wiki/documents/ringmasters-ledger-map-scrap.md`
```

- [ ] **Step 7: Verify and commit**

Run: `ls wiki/documents/ | wc -l`
Expected: `6`

```bash
git add wiki/documents
git commit -m "Transcribe the six recovered Ringmaster's Ledger documents"
```

---

## Task 7: Seed the wiki from the documents' clues

**Files:**
- Create: `wiki/npcs/the-ringmaster.md`, `wiki/npcs/the-old-knight.md`, `wiki/npcs/the-buyer.md`, `wiki/npcs/the-queen.md`
- Create: `wiki/factions/black-banners.md`
- Create: `wiki/locations/haven.md`, `wiki/locations/solace.md`, `wiki/locations/x-t.md`, `wiki/locations/the-old-road.md`
- Create: `wiki/items/the-staff.md`, `wiki/items/the-ringmasters-ledger.md`
- Create: `wiki/lore/miracle-bearers.md`, `wiki/lore/the-gathering-war.md`

> Record only what the documents state. Phrase inferences as "Open questions,"
> not facts. Every "What we know" bullet cites its source document.

- [ ] **Step 1: Write `wiki/npcs/the-ringmaster.md`**

```markdown
---
name: The Ringmaster
aliases: []
type: npc
tags: [circus, smuggling]
first-learned: recovered documents
---

## What we know
- Runs a traveling circus and keeps a detailed ledger; signs notes "— The Ringmaster". (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- Smuggles "special cargo" in a locked wagon, timed to move "best before war." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Traffics in "miracle-bearers," sold "breathing," and warns against revealing them — "no blue light, no singing under the floorboards." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Keeps his own performers ignorant of the ledger's contents. (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Has lied to the old knight, telling him the wagons "went west." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Expects "the Queen does not need my circus once the armies march." (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- Wary of hidden powers: "even stages have masters in the dark." (`wiki/documents/ringmasters-ledger-back-cover-list.md`)

## Open questions
- Who is his patron/buyer? (A name was burned out of the ledger.)
- What exactly is the "special cargo," and where is it now?

## See also
- `wiki/items/the-ringmasters-ledger.md` · `wiki/lore/miracle-bearers.md`
- `wiki/npcs/the-old-knight.md` · `wiki/factions/black-banners.md`
```

- [ ] **Step 2: Write `wiki/npcs/the-old-knight.md`**

```markdown
---
name: The Old Knight
aliases: []
type: npc
tags: []
first-learned: recovered documents
---

## What we know
- An older knight who has dealt with the Ringmaster before and "believed [him] once." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- The Ringmaster plans to tell him "the wagons went west" if he returns — suggesting the knight may be searching for the cargo. (`wiki/documents/ringmasters-ledger-special-cargo.md`)

## Open questions
- Who is he, and what is he looking for? Is he connected to the "knights seen" near Solace? (`wiki/documents/ringmasters-ledger-back-cover-list.md`)

## See also
- `wiki/npcs/the-ringmaster.md` · `wiki/locations/solace.md`
```

- [ ] **Step 3: Write `wiki/npcs/the-buyer.md`**

```markdown
---
name: The Buyer
aliases: []
type: npc
tags: [unknown]
first-learned: recovered documents
---

## What we know
- An unnamed buyer connected to the Staff; insisted — "said it twice" — that "the Staff must not reach X.T." (`wiki/documents/ringmasters-ledger-map-scrap.md`)

## Open questions
- Who is the buyer, and why must the Staff be kept from X.T.?
- Is the buyer the same as the Ringmaster's burned-out patron?

## See also
- `wiki/items/the-staff.md` · `wiki/locations/x-t.md`
```

- [ ] **Step 4: Write `wiki/npcs/the-queen.md`**

```markdown
---
name: The Queen
aliases: []
type: npc
tags: [politics, war]
first-learned: recovered documents
---

## What we know
- A ruling monarch whose forces are expected to march; "the Queen does not need my circus once the armies march." (`wiki/documents/ringmasters-ledger-back-cover-list.md`)

## Open questions
- Is the Queen behind the black-banner armies, opposed to them, or unrelated?
- What realm does she rule?

## See also
- `wiki/factions/black-banners.md` · `wiki/lore/the-gathering-war.md`
```

- [ ] **Step 5: Write `wiki/factions/black-banners.md`**

```markdown
---
name: Black Banners
aliases: []
type: faction
tags: [war, military]
first-learned: recovered documents
---

## What we know
- Armies "gathering eastward" under plain black banners with "no heraldry [the Ringmaster] knows." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- Too large to be a "border dispute" — "too many boots." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- "Seen after dusk." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- Their soldiers are not to be trusted — "they promise protection like wolves promise mercy"; sell only for silver paid up front. (`wiki/documents/ringmasters-ledger-roads-payments.md`)

## Open questions
- Who commands them? Are they tied to the Queen?
- What is their objective in the east?

## See also
- `wiki/npcs/the-queen.md` · `wiki/lore/the-gathering-war.md`
```

- [ ] **Step 6: Write `wiki/locations/haven.md`**

```markdown
---
name: Haven
aliases: []
type: location
tags: [town]
first-learned: recovered documents
---

## What we know
- A settlement where travelers may be questioned; the Ringmaster warns to keep cargo hidden there. (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- "Haven listens for coin, prayer, and fear. Keep wagons quiet there." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- Appears on the regional map and the torn map scrap, linked by road to Solace and X.T. (`wiki/documents/regional-map-burned.md`, `wiki/documents/ringmasters-ledger-map-scrap.md`)

## See also
- `wiki/locations/solace.md` · `wiki/locations/the-old-road.md`
```

- [ ] **Step 7: Write `wiki/locations/solace.md`**

```markdown
---
name: Solace
aliases: [Selace]
type: location
tags: [town]
first-learned: recovered documents
---

## What we know
- A place to "watch" — "too many paths meet under innocent roofs." (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- The north road follows the river toward Solace; "too visible by daylight." Spelled "Selace" on one page — likely the same place. (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- Knights have been seen here, and "not all relics stay buried." (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- The torn map scrap questions a "swamp road? old stones?" near Solace. (`wiki/documents/ringmasters-ledger-map-scrap.md`)
- Appears on the regional map. (`wiki/documents/regional-map-burned.md`)

## Open questions
- Is "Selace" a variant spelling or a different place?

## See also
- `wiki/locations/haven.md` · `wiki/locations/x-t.md` · `wiki/npcs/the-old-knight.md`
```

- [ ] **Step 8: Write `wiki/locations/x-t.md`**

```markdown
---
name: "X.T."
aliases: []
type: location
tags: [mystery, relic]
first-learned: recovered documents
---

## What we know
- A site referred to only by the initials "X.T." — "do not speak the full name." (`wiki/documents/ringmasters-ledger-map-scrap.md`)
- Possibly an "old [site] below the green." (`wiki/documents/ringmasters-ledger-map-scrap.md`)
- The buyer insists "the Staff must not reach X.T." (`wiki/documents/ringmasters-ledger-map-scrap.md`)
- Marked on both the torn map scrap and the regional map, east of Haven and Solace. (`wiki/documents/ringmasters-ledger-map-scrap.md`, `wiki/documents/regional-map-burned.md`)

## Open questions
- What is X.T.'s full name, and why must it not be spoken?
- Why must the Staff be kept from it?

## See also
- `wiki/items/the-staff.md` · `wiki/npcs/the-buyer.md`
```

- [ ] **Step 9: Write `wiki/locations/the-old-road.md`**

```markdown
---
name: The Old Road
aliases: [North Road, River Road]
type: location
tags: [travel]
first-learned: recovered documents
---

## What we know
- The north road follows the river toward Solace and is "too visible by daylight." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- "Not safe — not by king's men, not by bandits." (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- The torn map scrap labels an "old road" and questions a "swamp road? old stones?" route; "route uncertain — ink ruined by rain." (`wiki/documents/ringmasters-ledger-map-scrap.md`)

## See also
- `wiki/locations/solace.md` · `wiki/documents/ringmasters-ledger-map-scrap.md`
```

- [ ] **Step 10: Write `wiki/items/the-staff.md`**

```markdown
---
name: The Staff
aliases: []
type: item
tags: [relic, mystery]
first-learned: recovered documents
---

## What we know
- An item the buyer is fixated on: "the Staff must not reach X.T. The buyer said it twice." (`wiki/documents/ringmasters-ledger-map-scrap.md`)

## Open questions
- What does the Staff do? Where is it now, and who holds it?
- Is it connected to the "relics" said to be buried near Solace, or to the recovered arcane schematic?

## See also
- `wiki/locations/x-t.md` · `wiki/npcs/the-buyer.md` · `wiki/documents/arcane-schematic-wheel.md`
```

- [ ] **Step 11: Write `wiki/items/the-ringmasters-ledger.md`**

```markdown
---
name: The Ringmaster's Ledger
aliases: [The Ledger]
type: item
tags: [document]
first-learned: recovered documents
---

## What we know
- The Ringmaster's personal ledger, recovered as loose pages from a locked wagon. (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Records "special cargo," roads, payments, witnesses, and warnings; the Ringmaster would save it "before the masks" if the wagon burned. (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- The pages we hold are transcribed under `wiki/documents/`.

## See also
- `wiki/npcs/the-ringmaster.md`
- `wiki/documents/ringmasters-ledger-special-cargo.md`, `...-roads-payments.md`, `...-back-cover-list.md`, `...-map-scrap.md`
```

- [ ] **Step 12: Write `wiki/lore/miracle-bearers.md`**

```markdown
---
name: Miracle-bearers
aliases: []
type: lore
tags: [magic, religion, trafficking]
first-learned: recovered documents
---

## What we know
- People (or beings) the Ringmaster traffics as "special cargo": "Miracle-bearers draw eyes. Old gods, new lies, same price if delivered breathing." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Tell-tale signs to hide them: "no blue light, no singing under the floorboards." (`wiki/documents/ringmasters-ledger-special-cargo.md`)
- Linked to a tension between "old gods" and "new lies." (`wiki/documents/ringmasters-ledger-special-cargo.md`)

## Open questions
- What powers do miracle-bearers have? Who buys them, and for what?

## See also
- `wiki/npcs/the-ringmaster.md` · `wiki/lore/the-gathering-war.md`
```

- [ ] **Step 13: Write `wiki/lore/the-gathering-war.md`**

```markdown
---
name: The Gathering War
aliases: []
type: lore
tags: [war, politics]
first-learned: recovered documents
---

## What we know
- War appears imminent; armies are massing eastward under black banners. (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- A Queen's forces are expected to march. (`wiki/documents/ringmasters-ledger-back-cover-list.md`)
- Roads are unsafe — made so by neither king's men nor bandits. (`wiki/documents/ringmasters-ledger-roads-payments.md`)
- The Ringmaster times his smuggling to "before war," knowing scrutiny rises once "marching starts." (`wiki/documents/ringmasters-ledger-special-cargo.md`)

## See also
- `wiki/factions/black-banners.md` · `wiki/npcs/the-queen.md`
```

- [ ] **Step 14: Verify and commit**

Run: `find wiki/npcs wiki/factions wiki/locations wiki/items wiki/lore -name '*.md' | wc -l`
Expected: `13`

```bash
git add wiki/npcs wiki/factions wiki/locations wiki/items wiki/lore
git commit -m "Seed wiki entries from the recovered documents"
```

---

## Task 8: Final review and sync

- [ ] **Step 1: Confirm the whole tree looks right**

Run: `git status -sb && find . -path ./.git -prune -o -name '*.md' -print | sort`
Expected: working tree clean (aside from the ignored `IMG_*.png`); all README/CLAUDE/OVERVIEW, six documents, and thirteen wiki entries present.

- [ ] **Step 2: Confirm originals are still uncommitted/ignored**

Run: `git ls-files | grep -c 'IMG_.*png' || true`
Expected: `0` (no full-resolution PNGs tracked).

- [ ] **Step 3: Pull then push to origin**

```bash
git pull --no-edit
git push
```
Expected: pushes all commits to `origin/main`; working tree clean afterward.

---

## Self-review notes (author)

- **Spec coverage:** content model (Tasks 1,6,7), contribution-via-Claude + git sync (Task 3 CLAUDE.md), players-only/no-secrets (stated in README, CLAUDE.md, wiki/README), transcribe + compressed images (Tasks 5,6), repo structure & conventions (Tasks 1–4), seed from existing documents (Tasks 6,7), final sync (Task 8). All spec sections covered.
- **Placeholders:** living-document notes in OVERVIEW.md ("no sessions logged yet") are intentional and complete instructions, not plan gaps. No TBD/TODO remain.
- **Type/name consistency:** document slugs are identical across Task 5 (jpg), Task 6 (md `source-image` paths), and the `See also`/citation links in Tasks 6–7. Folder names match `CLAUDE.md` and `wiki/README.md`.
```
