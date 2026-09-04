# stan.life

Personal one-page site for Stan Chollet (Stanislas Chollet, "tsunammis"),
served at stan.life. Static HTML, no build step, no framework.

## Structure

```
src/
  index.html        # the entire site — one self-contained file
  robots.txt         # explicit crawler allow-all
  images/
    stan-chollet.jpg # avatar, 500x500 JPEG, ~38KB
```

There is no CSS file and no JS file. All styling is inline `style="..."`
attributes directly on elements — a deliberate choice (see "Conventions"
below), not an oversight.

`src/` is the web root. Nothing outside `src/` is served.

## Deployment

Hosted on Cloudflare Pages (GitHub Pages was explicitly declined — see
below). `wrangler.toml` at the repo root declares
`pages_build_output_dir = "src"` with no build command, since this is
plain static HTML. That covers repo-side configuration; connecting
this GitHub repo to a Cloudflare Pages project is a one-time manual
step in the Cloudflare dashboard (Claude has no access to Cloudflare
credentials or the dashboard). When connecting it, the production
branch should be `main`.

## History

This repo used to be a multi-section Medium-style profile page (bio +
social icons + a long list of past conference talks and meetups going
back to 2015). In September 2026 the whole thing was replaced:

1. The default branch was renamed from `master` to `main`.
2. All old content was deleted (`git log`: "Remove all repository
   content").
3. A new minimal one-page design was built from scratch in Claude
   Design (the canvas editor), then hand-adapted into this repo as a
   single static `index.html` (Claude Design's own runtime/tooling
   is not part of this repo — only the resulting plain HTML is).

GitHub Pages was explicitly declined as a hosting mechanism — don't
add a `.github/workflows/*pages*` setup or a `CNAME` file without
checking first.

## Design system

- **Type**: `Domine` (serif, headline + body) paired with `JetBrains
  Mono` (small caps kicker line, and previously used for links —
  loaded via Google Fonts `<link>` in `<head>`).
- **Color**: near-white background and near-black text defined in
  `oklch()`, plus a single accent color (`#C2410C`, a rust/amber)
  used sparingly — the small status dot on the avatar and the two
  horizontal accent bars (one under the name, one closing the page).
  This is a deliberate "minimal mono" direction: mostly black/white,
  one accent, no gradients, no rounded-card-with-left-border clichés.
- **Layout**: a single centered column, `max-width: 640px` outer /
  `600px` for body paragraphs, fluid via `clamp()` so it scales
  smoothly from a ~390px phone to desktop without separate
  breakpoints. Verified by screenshotting the real file with
  Playwright at 390×844 and 1440×900.
- **Avatar**: circular (`border-radius: 50%`), not square. The
  current photo is the user's real photo, unfiltered — no grayscale,
  no blur, no darkening layer (explicitly requested). If you crop a
  new photo, remember: content near the exact center of each edge
  survives a circular clip; content in the corners does not. Preview
  the actual circular clip (not just the square crop) before
  finalizing — a script that composites the crop through an ellipse
  mask and saves a PNG is the fast way to check this without a full
  publish cycle.

## Content

The bio text is final, user-authored copy (iterated with the user
directly, not generated fresh) — don't rewrite its voice or content
without being asked. Structure: name/location header, then five short
paragraphs (career at Datadog / Orléans Tech community, other
projects + an aside about personal finance reading, health context
—ulcerative colitis — and sport, closing with social links), no
"About" sections beyond that, no talks/history list (deliberately cut
from the old site).

Notable content decisions, in case they come up again:
- No job title (e.g. "Engineering Director") appears anywhere
  user-visible on the page — the user does not want to be publicly
  associated with a title. This does **not** extend to the
  structured data's `worksFor` (Datadog), which stayed.
- The ulcerative colitis / health disclosure is intentional and
  user-approved for public display — don't soften or remove it
  reflexively.
- Closing line links to X, LinkedIn, and GitHub, in that order.

## SEO / structured data

`index.html` carries a `schema.org` `Person` JSON-LD block in
`<head>` with `alternateName: ["Stanislas Chollet", "tsunammis"]` and
`sameAs` links to X, LinkedIn, and GitHub — this is the mechanism for
tying the three name/handle variants to one entity for search engines
and AI crawlers. There's also screen-reader-only text right after the
`<h1>` repeating the alternate names (visually hidden via clip-rect,
not `display:none`, so it stays accessible/crawlable) — if you touch
the `<h1>`, keep that span.

`robots.txt` explicitly allows all crawlers (`User-agent: *` /
`Allow: /`). No `sitemap.xml` — single page, not worth it.

## Conventions to preserve

- **Single-file, inline-styled HTML.** Don't introduce a build step,
  a CSS framework, or an external stylesheet for this page. If it
  grows past one page, reconsider — but don't preemptively abstract.
- **`width:100%` on every body `<p>`.** This page centers its column
  with `align-items:center` on a flex parent while each paragraph is
  `text-align:left`. Without an explicit `width:100%` on the `<p>`,
  a *short* paragraph's box shrinks to fit its own content and gets
  centered by the flex parent — the text ends up looking centered
  even though `text-align:left` is set. This bit us once (the
  closing "I'm on X, LinkedIn and GitHub." line). Keep `width:100%`
  on any paragraph you add or edit in that block.
- **`min-height:100vh` on the root wrapper, not `min-height:100%`.**
  This page's background needs to fill the whole viewport regardless
  of content height; percentage heights depend on an unbroken chain
  of ancestor heights and silently fail in some embedding contexts.
  `vh`/`dvh` units don't have that problem.
- Don't add Docker or npm dependencies back — a prior commit
  ("Remove dependency to Docker + NPM") deliberately removed them.
