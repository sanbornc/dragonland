# Dragonland Knowledge Base — Design

**Date:** 2026-06-03
**Status:** Approved (design); pending implementation plan
**Campaign:** Dragonland

## Purpose

A shared git repository that acts as a queryable knowledge base for the
Dragonland D&D group. Members record what they learn in sessions and ask
questions against the accumulated knowledge. Every member can both contribute
and ask. Claude Code is the primary interface for everyone.

## Decisions (from brainstorming)

1. **Content model:** Per-session logs feed a curated wiki. Session files are
   the raw "what happened"; the wiki is the distilled "what we know about X,"
   with each fact noting which session it came from (provenance).
2. **Contribution flow:** Everyone interacts via Claude Code in plain language.
   Claude performs all git operations and file edits. Members need not know git
   or markdown.
3. **Audience & secrets:** Players-only. There is **no** DM-only area and **no**
   spoiler handling — everything stored is already known to the players. The DM
   may contribute lore if he chooses, but it goes into the same shared,
   fully-readable knowledge base.
4. **Source images:** Transcribe each in-world document into searchable markdown
   AND keep a compressed copy of the image in `assets/`. Full-resolution
   originals stay out of git.
5. **Git sync:** Instruction-only via `CLAUDE.md` (no hooks). Claude pulls
   latest before answering/editing and commits + pushes after contributions.
   This is the most portable choice — it works identically for every member who
   clones the repo, with no per-machine setup.

## Repository structure

```
dragonland/
├── README.md            For humans: what this is + "open Claude Code and talk to it"
├── CLAUDE.md            For Claude: the workflow rules (the engine)
├── OVERVIEW.md          Campaign premise, party roster, "where we are now"
├── sessions/            One file per session, chronological
│   └── NN-YYYY-MM-DD-short-title.md
├── wiki/                Curated, canonical knowledge (distilled from sessions)
│   ├── characters/      The party's own PCs
│   ├── npcs/            Non-party characters (e.g. the Ringmaster)
│   ├── locations/       Haven, Solace, the river road, etc.
│   ├── factions/        Black banners, armies gathering eastward, etc.
│   ├── items/
│   ├── quests/          Open threads & objectives
│   ├── lore/            World lore, history, rumors
│   └── documents/       Transcriptions of recovered in-world documents
└── assets/
    └── documents/       Compressed copies of the source document images
```

## File conventions

- **Session files:** `NN-YYYY-MM-DD-short-title.md`, zero-padded number for
  sort order. Light frontmatter (session number, date, players present) plus a
  recap body.
- **Wiki entries:** one file per entity, kebab-case filename
  (`wiki/npcs/the-ringmaster.md`). One-file-per-entity keeps each topic focused
  and nearly eliminates merge conflicts when two members contribute at once.
- **Frontmatter (wiki):** light and hand-editable — `name`, `aliases`, `type`,
  `tags`, `first-learned` (session reference).
- **Cross-links:** relative markdown links between related entries, plus a
  "See also" line where useful.
- **Provenance:** facts in the wiki cite the session they were learned in.

## In-world documents

Each recovered document (the "Ringmaster's Ledger" pages, and future finds)
becomes a file under `wiki/documents/`, e.g.
`wiki/documents/ringmasters-ledger-roads-payments-witnesses.md`. Each contains:

- A faithful transcription of the legible text.
- A note on what is burned/illegible/redacted in-fiction.
- A link to the compressed image in `assets/documents/`.

Clues within these documents are also surfaced into the relevant wiki entries
(e.g. the "black banners" faction, the "old knight," Haven, Solace). All such
content is player-known; transcriptions preserve the in-fiction flavor but carry
no spoiler restrictions.

## Claude behavior (CLAUDE.md contract)

Every session, Claude will:

1. **Pull first.** Run `git pull` at session start and before answering any
   question, so the latest contributions are always present. Surface merge
   conflicts rather than guessing.
2. **Answer from the repo.** Search `sessions/` and `wiki/`, answer with
   citations to the source files, and state plainly when something is not yet
   recorded.
3. **Record contributions.** When a member describes a session, write or append
   the session log, then distill new facts into the relevant wiki entries
   (creating entries as needed), each with session provenance.
4. **Transcribe documents.** When given an in-world document image, transcribe
   it into `wiki/documents/`, compress and store the image in
   `assets/documents/`, and surface its clues into the relevant wiki entries.
5. **Commit & push** after any contribution, with a clear message
   (instruction-driven, no hooks).
6. **No secrets.** All content is shared player knowledge; there is no spoiler
   handling and no protected area.

## Initial scaffolding (first implementation)

- Create the directory structure with brief `README.md` files where helpful.
- Write `README.md`, `CLAUDE.md`, and `OVERVIEW.md` (seeded with the campaign
  name Dragonland and known facts).
- Add a `.gitignore` that excludes full-resolution source images
  (`IMG_*.png`) while allowing compressed `assets/`.
- Transcribe the six existing "Ringmaster's Ledger" images into
  `wiki/documents/`, compress copies into `assets/documents/`, and seed the
  wiki entries the clues imply.
- Make the initial real commit and push.

## Out of scope (YAGNI)

- No hooks, CI, or automation beyond `git pull`/`commit`/`push` by Claude.
- No DM-only area, access control, or spoiler gating.
- No custom search tooling — markdown + Claude's search is sufficient.
- No Git LFS (originals are not versioned).
```
