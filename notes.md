# Notes

**Prediction (from memory):** I wrapped the `readFileSync` in `lib/store.js` with a file-exists check and added an `if` to `scripts/check.js` that prints `notes.md`.

## Claude's summary of changes (`read-repo` vs `main`)

**Changed files:** 3, namely `lib/store.js` and `scripts/check.js` (modified) and `notes.md` (new).

- **`lib/store.js`:** `load()` now runs `fs.existsSync(FILE)` before `readFileSync`. A missing `notes.json` returns `{ nextId: 1, notes: [] }` directly. The `try/catch` around `JSON.parse` is kept, so a broken file still returns the empty store.
- **`scripts/check.js`:** reads `notes.md` once into `content` and uses it for the empty-file check. A new `if` prints the file after the PASS line when the script runs with `--print`. Plain runs behave as before.
- **`notes.md`:** new file with the prediction and this summary.

**Flagged as possibly unintended:**

1. **`scripts/check.js` is the lesson's grading script.** It's the stray change in a second file. CI (`check.yml`) runs it to grade the PR, and editing the grader in a submission is usually not wanted. The edit doesn't change pass/fail.
2. **A broken `notes.json` can quietly lose data.** If `notes.json` holds invalid JSON, `load()` returns an empty store, and the next `add` or `delete` overwrites the file with it. This was already the behaviour before the change.
3. **Line endings.** Git warns that `notes.md` will be converted from LF to CRLF. This is cosmetic.

**Did it catch the stray change?** Yes. The summary flagged the edit to `scripts/check.js`, the second file, as possibly unintended because it changes the lesson's grading script.
