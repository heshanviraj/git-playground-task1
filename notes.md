# Notes

**Prediction (from memory):** I wrapped the `readFileSync` in `lib/store.js` with a file-exists check, added a `formatNote` helper and renamed `ok` to `removed` in `notes.js`, and added an `if` to `scripts/check.js` that prints `notes.md`.

## Claude's summary of changes (`read-repo` vs `main`)

**Changed files:** 4, namely `lib/store.js`, `notes.js` and `scripts/check.js` (modified) and `notes.md` (new).

- **`lib/store.js` (line change):** `load()` now runs `fs.existsSync(FILE)` before `readFileSync`. A missing `notes.json` returns `{ nextId: 1, notes: [] }` directly. The `try/catch` around `JSON.parse` is kept, so a broken file still returns the empty store.
- **`notes.js` (new function):** adds `formatNote(note)`, which returns `#<id>  <text>`. The `list` command now uses it. Output is unchanged.
- **`notes.js` (renamed variable):** in the `delete` command, `ok` is renamed to `removed`. Behaviour is unchanged.
- **`scripts/check.js`:** reads `notes.md` once into `content` and uses it for the empty-file check. A new `if` prints the file after the PASS line when the script runs with `--print`. Plain runs behave as before.
- **`notes.md`:** new file with the prediction and this summary.

**Flagged as possibly unintended:**

1. **`scripts/check.js` is the lesson's grading script.** This is the stray edit in a file unrelated to the app. CI (`check.yml`) runs this script to grade the PR, so editing it in a submission is usually not wanted, even though the edit doesn't change pass/fail.
2. **`formatNote` is only half used.** `list` uses it, but `add` still builds its own `Added note #<id>: <text>` string, so the two formats can drift apart.
3. **A broken `notes.json` can quietly lose data.** If `notes.json` holds invalid JSON, `load()` returns an empty store, and the next `add` or `delete` overwrites the file with it. This was already the behaviour before the change.

**Did it catch the stray change?** Yes. The summary flagged the edit to `scripts/check.js` as possibly unintended because it changes the lesson's grading script.
