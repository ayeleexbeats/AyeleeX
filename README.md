# Release Board

A private web page that holds Alexandre's (AyeleeX) creative projects: what's in flight,
what's the next move, and what's written but not yet placed in the work.

Opened on a phone, usually early. The top of the page offers two doors -- a **prompt**
(the next concrete thing to write) and a **question** (one open question meant to start him
writing). Both default to his own material; Claude only writes either one when he taps
**Two more**, and it's labelled when it does.

## Where it lives

| Thing | Where |
|---|---|
| Page source | `board/index.html` |
| Published page | https://claude.ai/code/artifact/27279921-9ef3-49df-9cc9-bb02e8f404d4 |
| His writing | Mem, note `Release Board -- state`, id `0d3602c2-7df2-50e0-8165-1149175ddec4`, in the `AyeleeX Persona` collection. **This is the one folder.** |
| Board data | the artifact's own database (`projects`, `songs`, `queue`, `questions`, `notes`, `logs`, `meta`) -- the app's working storage, not a second home |

## How it's put together

The page is published as a Claude Artifact and declares four runtime capabilities:

- **db** — board state, so it's identical on phone and laptop and readable by Claude between sessions
- **sample** — generates a fresh pair of doors when he asks for two more
- **downloads** — the Export button writes the whole board to a JSON file he keeps
- **mcp** (Mem) — notes push themselves to Mem. Each push reads the note's current version with
  `get_note`, then replaces it with `update_note`.

  **Whether Mem is behind is derived, never remembered.** `memPendingNow()` compares the newest
  note's `updatedAt` against `meta/board.lastMemSaveAt`, and `catchUpMem()` runs after every
  `loadAll()`. An in-memory "needs pushing" flag does not survive iOS killing the page, and a
  flush on page-hide usually cannot finish two connector round trips before the phone suspends —
  both failed in practice on 2026-09-12. The timestamp comparison self-heals on the next open.
  The 25s debounce is a convenience, not the mechanism.

  The button forces an immediate checkpoint and reports state: *Saved to Mem · <when>*, or
  *Not in Mem yet* in amber. A note is always in the board's store before any push is attempted,
  so a Mem outage never costs writing.

`update_note` echoes the **pre-update** body in its response. Confirm a write with a fresh
`get_note`, never by reading the write's own reply.

Provenance is tracked on every queued item and question: `typed` (his words) or `generated`.
His words render in the serif at full weight and always sort first; generated text renders
quieter with a visible tag. This is deliberate and shouldn't be softened.

**Saved writing must be visible.** The notes box starts empty every session by his choice, so the
**Written at the door** section is what proves the work survived. Without it an empty box reads as
data loss — which is exactly what happened. Do not remove that section without replacing it.

**Unsaved text on screen is sacred.** `renderNote()` refuses to rebuild the box while its textarea
is dirty or focused, and `setDoors()` will not swap the door under an unfinished note.

## To change it

Edit `board/index.html` and republish to the **same URL** above. Publishing without that URL
creates a separate artifact instead of updating this one.

## Not built yet

- Copilot history import (export -> Mem -> the board's queue)
- Full draft storage in the board
- The rest of the dialogue bank: `PLAYBACK 3D — Dialogue Lines to Incorporate` holds roughly
  90 lines; only his own ranked top ten plus four scenes are seeded so far.
