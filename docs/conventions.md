# JSON Conventions

This file documents the shape of `tree.json` so that future-Jim (and any collaborator, human or AI) can add to it without breaking things.

## Top-level shape

```json
{
  "people":   [ /* array of person objects */ ],
  "families": [ /* array of family objects */ ]
}
```

Two arrays, two record types. Every person and every family has a unique `id`. Relationships are expressed by ID references — same idea as GEDCOM's `@I#@` and `@F#@` pointers, but with meaningful, human-readable IDs.

## Person IDs

**Format:** `Given_Middle_Surname` with optional `_Suffix`. Capitalized as proper names.

Examples:
- `James_Paul_McGinnis`
- `James_Paul_McGinnis_II`
- `Koren_Jean_Sill`
- `Sidney_Bacon` (no middle name — just skip it, no double underscore)
- `Cora_Pauline_English`

**Women always use their maiden surname.** The marriage to a Hillyer or a Barker is captured in the family record, not in the woman's ID. So Ann is `Ann_Kathryn_McGinnis`, not `Ann_Kathryn_Hillyer`. This keeps her permanently findable in the McGinnis lineage.

## Family IDs

**Format:** `husbandSurname_husbandFirst_wifeSurname_wifeFirst`, all lowercase.

Examples:
- `mcginnis_james_sill_koren` — Jim & Koren
- `mcginnis_charles_meyer_shirley` — Jim's parents
- `mcginnis_paul_bacon_sidney` — Jim's paternal grandparents
- `hillyer_saunders_mcginnis_ann` — Ann & Saunders Hillyer
- `mcginnis_william_english_cora` — William Erasmus & Cora Pauline (top of tree)

**Why lowercase for families and capitalized for people?** Visual distinction. When you're scanning a JSON file, lowercase IDs read as "internal codes" and capitalized IDs read as "people."

## Person fields

Minimum viable person — everything except `id`, `given`, and `surname` is optional:

```json
{
  "id": "James_Paul_McGinnis",
  "given": "James Paul",
  "surname": "McGinnis"
}
```

### Field reference

| Field | Meaning | Example |
|---|---|---|
| `id` | Unique person ID | `"James_Paul_McGinnis"` |
| `nickname` | What they go by, if different from formal name | `"Jim"` |
| `given` | First + middle name | `"James Paul"` |
| `surname` | Family name (maiden name for women) | `"McGinnis"` |
| `suffix` | Jr., II, III, etc. | `"II"` |
| `sex` | `"M"` or `"F"` | `"M"` |
| `dob` | Date of birth, ISO format `YYYY-MM-DD` | `"1965-02-21"` |
| `pob` | Place of birth, free text | `"Dallas, Texas, USA"` |
| `dod` | Date of death | `"2020-03-15"` |
| `pod` | Place of death | `"Boston, Massachusetts, USA"` |
| `residence` | Where they live or primarily lived | `"Dallas, Texas, USA"` |
| `hs` | High school | `"St. Mark's School of Texas"` |
| `hsd` | High school graduation date | `"1983"` or `"1983-05-30"` |
| `univ` | University | `"Duke University"` |
| `univd` | University graduation date | `"1987"` or `"1987-05-15"` |
| `occupation` | Career, role, profession | `"CEO, Vanco"` |
| `military` | Military service | `"U.S. Army, 1965-1968, Vietnam"` |
| `parents` | Family ID where this person is a child | `"mcginnis_charles_meyer_shirley"` |
| `spouse_families` | Array of family IDs where this person is a spouse | `["mcginnis_james_sill_koren"]` |
| `notes` | Free-text biographical details, anecdotes, sources | `"Mayflower descendant..."` |

### Date format

Dates are ISO `YYYY-MM-DD`. If only the year is known, use just `YYYY`. If year and month, `YYYY-MM`. The viewer formats them as "March 15, 1920" automatically.

### Place format

Convention: `City, County, State, Country` — most specific first. Use period-appropriate names (`Asmara, Eritrea, Ethiopia` for 1965, when Eritrea was part of Ethiopia).

## Family fields

```json
{
  "id": "mcginnis_paul_bacon_sidney",
  "husband": "Paul_Sherman_McGinnis",
  "wife": "Sidney_Bacon",
  "children": ["Nancy_Ann_McGinnis", "John_Franklin_McGinnis", "Charles_Irving_McGinnis"]
}
```

| Field | Meaning |
|---|---|
| `id` | Unique family ID, lowercase |
| `husband` | Person ID of husband (omit or empty if unknown) |
| `wife` | Person ID of wife (omit or empty if unknown) |
| `children` | Array of person IDs in birth order, oldest first |

## The relationship invariant

Every parent/child link is expressed **twice** — once on the person (`parents`) and once on the family (`children`). They must agree. Same for spouses (`spouse_families` on the person, `husband`/`wife` on the family).

## What's *not* in this schema (yet)

Add when needed, not before:

- Sources and citations
- Photos and media attachments
- Multiple marriages (divorce + remarriage)
- Adoption, step-parents, half-siblings
- Marriage events (date, place)
- FamilySearch PID or Ancestry tree ID for cross-referencing

## Guiding principle

Fill in what you know. Leave the rest blank. The viewer gracefully handles missing data — a person with just a name and a parent link still renders and is still navigable.
