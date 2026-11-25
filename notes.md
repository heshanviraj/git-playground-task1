# Notes

## What changed

**`lib/store.js` — `load()`**

- `load()` now checks that `notes.json` exists with `fs.existsSync(FILE)` before it calls `fs.readFileSync`.
- If the file is missing, it returns the empty store `{ nextId: 1, notes: [] }` without trying to read it.
- The `try/catch` around `JSON.parse` stays, so a `notes.json` that exists but holds broken JSON also falls back to the empty store instead of crashing.
- Behaviour for a valid `notes.json` is unchanged.

`scripts/check.js` was left as it was, because its `readFileSync` call already comes after an `existsSync` check.

**Verified:** with no `notes.json` present, `require('./lib/store.js').all()` returns `[]`.
