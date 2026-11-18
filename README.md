# anmtrn

The personal page of Henry Robalino. Static HTML and CSS only — no JavaScript,
no framework, no build step.

```
.
├── index.html                  landing page (wordmark + five links)
├── about/index.html
├── sites/index.html            things built and still run
├── runes/
│   ├── index.html              post list
│   ├── the-draft-we-never-ran/
│   ├── how-ollama-gets-measured/
│   ├── small-things/
│   ├── a-static-site/
│   └── notes-on-the-futhark/
├── style.css                   the only stylesheet
└── vercel.json
```

`runes` is the writing section: a list of posts at `/runes/`, each post in its
own folder.

## Local preview

Root-relative links mean you need to serve the folder, not open the file:

```sh
python3 -m http.server 4321
# then visit http://localhost:4321
```

## Editing

**Links.** The GitHub and LinkedIn URLs appear on `index.html` (the four
options) and again in the last paragraph of `about/index.html`: they point at
`github.com/animetauren` and `linkedin.com/in/hrobalino`.

**The wordmark.** `index.html` contains an 18-row `<pre>` block. Letters `a`,
`n`, `m` and `r` sit at the x-height of 14 rows; the `t` rises four rows above
them, so the shortest character is exactly 14 rows tall. Each glyph is drawn
out of its own lowercase letter. It is plain text — edit it directly, but keep
the right-hand padding on every row so the block stays aligned.

**The sites list.** `sites/index.html` holds one `<li>` per site: a name, a
`.host` label, and a short paragraph. Copy the block to add another.

**Adding a post.**

1. Copy any folder under `runes/` to `runes/your-slug/`.
2. Rewrite the `<title>`, the `.meta` date, the `<h1>`, and the `.prose`
   paragraphs.
3. Add a matching entry to the `.posts` list in `runes/index.html`.

Removing a post is the same in reverse — there is no index to rebuild.

## Deploy

Deployed on Vercel as a static site; there is nothing to compile. Any push to
the production branch deploys, or run `vercel --prod` from this directory.
