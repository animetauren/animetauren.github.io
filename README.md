# anmtrn

The personal page of Henry Robalino. Static HTML and CSS only — no JavaScript,
no framework, no build step.

```
.
├── index.html                  landing page (wordmark + four links)
├── about/index.html
├── runes/index.html            Elder Futhark reference
├── blog/
│   ├── index.html              post list
│   ├── small-things/
│   ├── a-static-site/
│   └── notes-on-the-futhark/
├── style.css                   the only stylesheet
└── vercel.json
```

## Local preview

Root-relative links mean you need to serve the folder, not open the file:

```sh
python3 -m http.server 4321
# then visit http://localhost:4321
```

## Editing

**Links.** The GitHub and LinkedIn URLs appear on `index.html` (the four
options) and again in the last paragraph of `about/index.html`. Both point at
`github.com/henryrobalino` and `linkedin.com/in/henryrobalino` — change them in
those two files if the handles are different.

**The wordmark.** `index.html` contains an 18-row `<pre>` block. Letters `a`,
`n`, `m` and `r` sit at the x-height of 14 rows; the `t` rises four rows above
them, so the shortest character is exactly 14 rows tall. Each glyph is drawn
out of its own lowercase letter. It is plain text — edit it directly, but keep
the right-hand padding on every row so the block stays aligned.

**Adding a blog post.**

1. Copy any folder under `blog/` to `blog/your-slug/`.
2. Rewrite the `<title>`, the `.meta` date, the `<h1>`, and the `.prose`
   paragraphs.
3. Add a matching entry to the `.posts` list in `blog/index.html`.

Superfluous posts can be removed the same way — there is no index to rebuild.

## Deploy

Deployed on Vercel as a static site; there is nothing to compile. Any push to
the production branch deploys, or run `vercel --prod` from this directory.
