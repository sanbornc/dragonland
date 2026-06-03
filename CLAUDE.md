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
