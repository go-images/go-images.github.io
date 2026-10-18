# go-images landing

Hugo source for **<https://go-images.github.io/>** — the landing page of the
[`go-images`](https://github.com/go-images) org.

Preview locally:

```sh
hugo server
```

Build:

```sh
hugo --gc --minify
```

The rendered site lives under `public/`, which is gitignored: GitHub Pages
builds it from this source (see `.github/workflows/pages.yml`).

## ⛔ The repository list lives in `hugo.toml`, and that is not a presentation choice

The fleet's drift check ([`docscan`](https://github.com/go-fleettools/fleettools))
reads a landing's `hugo.toml` and `data/*.yaml`. This landing used to write its
repositories into `layouts/index.html` as hand-made cards, so the check could
see **no repository list at all** and reported the organisation as advertising
**nothing**, with five repositories invisible — while the page itself was
served perfectly well and named four of them.

A list a tool cannot read is a list that cannot be checked. `[[params.repos]]`
is where it belongs, and the page derives its counts from it.

## The design is not here

It comes from the shared Hugo module
[`go-fleettools/landingkit`](https://github.com/go-fleettools/landingkit),
pinned in `go.mod`. The previous layout was this organisation's own — a
`baseof.html`, a `nav.html`, a `footer.html` and a `main.css` that shared
nothing with the rest of the fleet. All four are gone; a file of the same name
under `layouts/partials/` would silently override the module's.

⛔ **`kit.go` is not optional.** A Hugo site has no Go code, so `go mod tidy`
finds nothing using the module and deletes the requirement — and the fleet's
Renovate presets run `gomodTidy` after every bump. The file exists only to be
seen by tidy; its build tag keeps it out of every build.

## The colours are read, not chosen

`landingkit brand <logo>` derives `[params.brand]` from the organisation mark's
own gradient: `#FB923C → #C2410C`, with the contrast measured (base on white
5.18:1, bright on the dark background 8.53:1). Backgrounds, surfaces, greys and
borders belong to the shared sheet and cannot be set here.

Verified against `go-fileshare.github.io`, the reference:

```text
PASS the <style> block equals the reference's once brand values are masked
PASS light: 60/60 background pixels identical (#ffffff)
PASS dark: 60/60 background pixels identical (#0b0e14)
```

## ⛔ A green deploy is not a served page

A sibling organisation's Pages source was a **branch**, so the live site was
Jekyll's rendering of `README.md` while the workflow built the Hugo site,
uploaded it and reported success — three green runs that never looked at the
URL. The last step of the deploy here fetches the live page and fails unless
Hugo generated it; `{{ hugo.Generator }}` in the layout is the marker, so
deleting that line breaks the deploy rather than the page.
