# Updating This Website

This guide is for Neil Malhotra (or anyone helping him) to keep the site current.
No software needs to be installed — everything can be edited directly on GitHub.com,
and the site republishes itself automatically a minute or two after every change.

## How the site works

- The site is built from plain-text files in this repository. Each page is a
  Markdown (`.md`) file.
- When you save ("commit") a change on GitHub, a robot (GitHub Actions) rebuilds
  and republishes the site automatically. You never need to "deploy" anything.
- To edit any file on GitHub.com: navigate to the file, click the pencil icon,
  make your change, and click **Commit changes**.

## Add a new article

1. Go to `content/publication/` and note how an existing article folder looks
   (e.g. `political-homophily/index.md`).
2. Click **Add file → Create new file**.
3. Name it `content/publication/short-name-of-paper/index.md` (lowercase,
   hyphens, no spaces — this becomes the page's web address, so keep it short
   and never rename it later).
4. Paste this template and fill it in:

   ```yaml
   ---
   title: "Full Title of the Paper"
   date: 2026-01-01            # year of publication (month/day don't matter)
   authors:
     - "First Author"
     - "Neil Malhotra"
   publication_types: ["journal_article"]
   journal: "Journal Name"
   volume: "10"
   number: "2"
   pages: "100-120"
   doi: "https://doi.org/10.xxxx/xxxxx"
   links:
     - name: "Publisher's Version"
       url: "https://doi.org/10.xxxx/xxxxx"
   tags: ["Political Polarization"]   # one of the five research-area names
   ---
   ```

5. To feature it in a research-area accordion on the homepage, add its folder
   name to that area's `publications` list in `data/research_areas.json`.

The Writings page, homepage accordions, People page, and "See Also" links all
update themselves from these files — there is nothing else to edit.

## Add a new book

Same as an article, but use `publication_types: ["book"]` and a `publisher:`
line instead of `journal`/`volume`/`pages`. To show a cover, upload the cover
image into the same folder named `featured.jpg`. Books automatically appear in
the Books section of the homepage.

## Update the bio or contact info

- Bio: edit `content/bio/index.md`
- Homepage intro blurb: edit `content/_index.md` (keep it short — the full bio
  lives on the Bio page; the two texts should never be identical)
- Contact: edit `content/contact/index.md`

## Replace the C.V.

Upload the new PDF over `static/files/cv.pdf` (same name).

## Replace the photo

Upload a square photo over `static/images/neil-malhotra.jpg` (same name).

## The People page — please review it

The People page is **auto-populated from the co-authors listed on the
publications**, and each person's link was **guessed by web search** during the
initial build. Some links may point to the wrong person or go stale. Please
review `data/coauthors.json` and fix or remove any wrong URL. Names without an
entry in that file appear unlinked, which is always safe. Three co-authors
currently have no link (no page could be verified): Gregory Ferenstein,
Christopher McConnell, and Annie Franco.

## Footer credit and directory listing

Both are currently **off** (per the information form). To turn either on, edit
`hugo.yaml`:

```yaml
  mysite:
    credit: true      # shows "Created using GaryKing.org/mysite" in the footer
    discovery: true   # invisible metadata so the GaryKing.org directory can find the site
```

## If something breaks

Every change is versioned. On GitHub, open the file's **History**, find the
last good version, and revert. If the site stops publishing, check the
**Actions** tab for a red ✗ and open the log — the error message usually names
the file with the problem (most often a typo in the `---` front matter of a
recently edited page).
