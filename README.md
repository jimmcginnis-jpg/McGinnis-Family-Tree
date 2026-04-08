# McGinnis Family Tree

A living genealogical record of the McGinnis family, descended from William Erasmus McGinnis and Cora Pauline English.

Based on the McGinnis Clan fan chart (July 2024), which documented 155 descendants across seven generations.

## What this is

A simple, human-maintained family tree in JSON, rendered as a navigable web page. Click any name — parent, spouse, or child — to jump to that person's profile, and walk the tree from there.

The goal is a record that is:

- **Owned** — lives in a Git repo, not locked inside a subscription service
- **Portable** — the JSON can be exported to GEDCOM anytime to hand off to Ancestry, FamilySearch, or a cousin
- **Extensible** — add a field, add a person, add a photo, add a story; nothing is frozen
- **Durable** — plain text, version-controlled, readable by any tool in any decade

## Structure

```
mcginnis-tree/
├── index.html       ← the viewer
├── tree.json        ← all the data
├── README.md        ← you are here
└── docs/
    └── conventions.md   ← how the JSON is structured
```

## Running it locally

Because `index.html` loads `tree.json` via `fetch()`, most browsers won't let you just double-click the HTML file — they block local file access for security. You need a small local server. From the repo directory:

```bash
python3 -m http.server
```

Then open <http://localhost:8000> in your browser.

Alternatively, deploy to Netlify or GitHub Pages (see below) and skip the local server entirely.

## Deploying

**Netlify (recommended):**
1. Push this repo to GitHub
2. Log into Netlify, click "Add new site" → "Import an existing project"
3. Connect to the GitHub repo
4. Build command: *(none)* — leave blank
5. Publish directory: *(none)* — leave blank
6. Every `git push` auto-deploys

**GitHub Pages:**
1. Push this repo to GitHub
2. Settings → Pages → Source: `main` branch, `/` (root)
3. Site goes live at `https://<username>.github.io/mcginnis-tree/`

## Adding people

See `docs/conventions.md` for the JSON format. The short version:

- Add a new object to the `people` array with a unique `id`
- If they have parents already in the tree, set `parents` to the family ID
- If they got married, create a new family in the `families` array and reference it in `spouse_families`
- Save, commit, push — it goes live within a minute

## Data sources

- **McGinnis Clan fan chart**, July 2024 (family reunion chart, original in PowerPoint format)
- Personal knowledge and research
- Future: FamilySearch, Ancestry, obituaries, census records, etc.

## License

Personal family history. Not for redistribution without permission.
