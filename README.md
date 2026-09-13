# Grix's Stud Book

An ARK: Survival Ascended breeding register. Tracks studs by species, level, sex,
the six colour regions, which colorway line they belong to, and where they're
parked (Cryo / OnlyMans / OnlyMans Aquatic / OnlyMans Drachennest).

Static page, no build step. `index.html` is the whole app.

## How the data works

Rows live in Supabase (project **Grix Stud Book**), in two tables:

| Table | Holds |
|---|---|
| `studs` | one row per dino |
| `colorways` | one row per named line, with its six target region colours |

Every row carries a `book_key`. The page reads that key from the URL fragment
(`#k=...`) and sends it to Supabase as an `x-book-key` header. Row-level
security compares the header to the row, so **without the key the API returns an
empty table and rejects every write.** The fragment never reaches the web host,
only the browser and Supabase.

That makes the link itself the credential. Anyone holding the full link can read
and edit the book; anyone with only the bare URL sees nothing.

### Rotating the key

If the link ever leaks, in the Supabase SQL editor:

```sql
update public.studs     set book_key = 'NEW_KEY' where book_key = 'OLD_KEY';
update public.colorways set book_key = 'NEW_KEY' where book_key = 'OLD_KEY';
```

Then hand out `.../#k=NEW_KEY`. The old link goes dark immediately.

## Colour data

Colour IDs 1–100 with the game's own names and sRGB values, from the
[ARK Official Community Wiki](https://ark.wiki.gg/wiki/Color_IDs). IDs 128+ are
dye shades that never appear on a creature, so they're left out. A region accepts
the ID (`27`), the name (`Dino Dark Green`), or a hex value, and canonicalises to
the name on blur.

## Species list

~175 tameable ASA creatures. Genesis and Fjordur creatures aren't included —
those maps hadn't shipped in ASA when this was built.

## Local development

Open `index.html` in a browser with `#k=<the book key>` on the end. It talks to
the live Supabase project, so edits are real edits.
