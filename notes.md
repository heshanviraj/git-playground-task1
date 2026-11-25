# Notes

## What changed

**`lib/store.js` — `load()`**

- `load()` now checks that `notes.json` exists with `fs.existsSync(FILE)` before it calls `fs.readFileSync`.
- If the file is missing, it returns the empty store `{ nextId: 1, notes: [] }` without trying to read it.
- The `try/catch` around `JSON.parse` stays, so a `notes.json` that exists but holds broken JSON also falls back to the empty store instead of crashing.
- Behaviour for a valid `notes.json` is unchanged.

`scripts/check.js` was left as it was, because its `readFileSync` call already comes after an `existsSync` check.

**Verified:** with no `notes.json` present, `require('./lib/store.js').all()` returns `[]`.

## Claude's summary of changes (`read-repo` vs `main`)

**Changed files:** 2 (`lib/store.js` modified, `notes.md` added). No uncommitted changes.

- **`lib/store.js`:** `load()` now runs `fs.existsSync(FILE)` before `readFileSync`. A missing `notes.json` returns `{ nextId: 1, notes: [] }` directly. The `try/catch` around `JSON.parse` is kept, with a comment, so a broken file still returns the empty store. Nothing else in the file changed.
- **`notes.md`:** new file that describes the change above.

**Flagged as possibly unintended:**

1. **No other edits found.** Lesson step 1 asks for a few small edits across a couple of files, including a stray one. The branch has only the `store.js` change and `notes.md`, nothing in `notes.js`, `lib/config.js` or elsewhere. If you made other edits, they were never saved or committed.
2. **A broken `notes.json` can quietly lose data.** If `notes.json` holds invalid JSON, `load()` returns an empty store, and the next `add` or `delete` overwrites the file with it. This was already the behaviour before the change; the new `if` doesn't fix it.
3. **Line endings.** Git warned that `notes.md` will be converted from LF to CRLF. That's harmless, but it may show up as a whole-file diff later.
