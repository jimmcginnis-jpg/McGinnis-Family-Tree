# JSON Conventions

This file documents the shape of `tree.json` so that future-Jim (and any collaborator, human or AI) can add to it without breaking things.

## Top-level shape

```json
{
  "people":   [ /* array of person objects */ ],
  "families": [ /* array of family objects */ ]
}
```

Two arrays, two record types. Every person and every family has a unique `id`. Relationships are expressed by ID references — identical in spirit to GEDCOM's `@I#@` and `@F#@` pointers, but with meaningful string IDs instead of opaque counters.

## Person objects

Minimum viable person — everything except `id` and `name` is optional:

```json
{
  "id": "jim",
  "name": { "given": "James Paul", "surname": "McGinnis" }
}
```

Full example with every supported field:

```json
{
  "id": "jim",
  "name": {
    "given": "James Paul",
    "surname": "McGinnis",
    "suffix": "II"
  },
  "sex": "M",
  "birth": {
    "date": "1965-02-21",
    "place": "Asmara, Eritrea, Ethiopia"
  },
  "death": {
    "date": "",
    "place": ""
  },
  "residence": "Dallas, Texas, USA",
  "parents": "f_charles_shirley",
  "spouse_families": ["f_jim_koren"],
  "notes": "Mayflower descendant via John Howland."
}
```

### Field notes

- **`id`** — a short, lowercase, underscore-separated string. Use something meaningful (`paul_sherman`, not `p47`). Must be unique across all people.
- **`name.given`** — first + middle, e.g. `"James Paul"`.
- **`name.surname`** — family name. For women, use the **maiden name**; the marriage family record carries the married-name relationship.
- **`name.suffix`** — optional, for `Jr.`, `II`, `III`, etc.
- **`sex`** — `"M"` or `"F"`. Used only for display ordering (father/mother labels).
- **`birth.date`** / **`death.date`** — ISO format `YYYY-MM-DD`, or `YYYY-MM`, or just `YYYY`. Empty string or omit the field if unknown.
- **`birth.place`** / **`death.place`** — free text, but convention is `City, County, State, Country` (or period-appropriate equivalents, e.g. `Asmara, Eritrea, Ethiopia` for 1965 when Eritrea was part of Ethiopia).
- **`residence`** — where they currently live or primarily lived. Optional.
- **`parents`** — the `id` of the family record where this person is a child. This is how you connect up to previous generations. Exactly one value (a person has one set of parents in this model; adoptions and step-parents aren't yet supported).
- **`spouse_families`** — an *array* of family IDs where this person is a spouse. Array because someone may have multiple marriages over a lifetime.
- **`notes`** — free-text biographical details, anecdotes, source citations, whatever.

## Family objects

```json
{
  "id": "f_paul_sidney",
  "husband": "paul_sherman",
  "wife": "sidney_bacon",
  "children": ["nancy_ann", "john_franklin", "charles_irving"]
}
```

### Field notes

- **`id`** — by convention, starts with `f_` (to visually distinguish family IDs from person IDs in the data file) and is built from the two spouses: `f_<husband>_<wife>`, using the distinctive parts of each name.
- **`husband`** / **`wife`** — person IDs. Either can be omitted if unknown.
- **`children`** — array of person IDs, ideally in birth order. Empty array `[]` or omit entirely if no known children.
- **`marriage`** (optional, not yet used) — can later hold `{ "date": "...", "place": "..." }` for the wedding itself.

## The relationship invariant

Every parent/child link is expressed **twice** — once on the person (`parents`) and once on the family (`children`). They must agree. The same is true for spouses (`spouse_families` on the person, `husband`/`wife` on the family).

This redundancy is deliberate: it lets code walk the tree in either direction without reconstructing relationships, and it makes validation easy (if the two sides disagree, the file has a bug).

## What's *not* in this schema (yet)

Intentionally left out until we actually need them — adding fields later is cheap, pruning unused ones is annoying:

- Sources and citations (who said Paul Sherman was born in 1902?)
- Photos and media attachments
- Multiple marriages for the same couple (divorce and remarriage)
- Adoption, step-parents, half-siblings
- Events beyond birth/death/marriage (baptism, immigration, military service, occupation)
- FamilySearch PID or Ancestry tree ID for cross-referencing

When we add any of these, update this doc at the same time.

## The guiding principle

Fill in what you know. Leave the rest blank. The viewer gracefully handles missing data — a person with just a name and a parent link still renders and is still navigable. Don't let unknown dates block you from capturing a name.
