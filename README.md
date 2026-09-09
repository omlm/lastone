# lastone

A plain HTML/CSS site built from Figma sketches. No build step, no dependencies —
open `index.html` and it works.

## Run it

Any static server will do. The repo ships a launch config for the Claude Code
browser pane, or run one by hand:

```bash
python3 -m http.server 4321
```

Then open <http://localhost:4321>.

## Layout

```
index.html          the page
css/tokens.css      every value that comes from Figma, as a custom property
css/base.css        reset + element defaults, nothing page-specific
css/styles.css      layout and components for this site
assets/             images, icons, fonts
```

## The one rule

**Values live in `css/tokens.css`.** Colours, sizes, radii and type all resolve
to a custom property declared there, named to match its Figma variable. When a
sketch needs a value that has no token, add the token first and then use it — a
hardcoded hex in `styles.css` is how a site and its Figma file drift apart
without anyone noticing.

`tokens.css` also carries the dark values in a `prefers-color-scheme` block.
Delete that block if the Figma file has no dark variant.

## Working from a sketch

One `<section>` per Figma frame, each with an accessible name (`aria-labelledby`
pointing at its heading). Keep the landmark structure that is already in
`index.html`: one `header`, one `main`, one `footer`, and no nested `header`
inside `main`.

Where the sketch asks for something these styles cannot express, leave a comment
at the site rather than a one-off local value — the comment is the record of what
is missing.
