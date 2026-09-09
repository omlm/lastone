# lastone__krs

The four pages of **last one standing kristiansand** as a plain HTML/CSS site.
No build step, no dependencies, no JavaScript — open `index.html` and it works.

Recreated from Figma: **KRSUltra**, page `Lastone`
([file](https://www.figma.com/design/8Y7FLiUMunOCblWRxTMZoU/KRSUltra?node-id=373-562)),
frames `Index`, `Info`, `Course` and `Results`.

| Page | File | Figma node |
|---|---|---|
| Index | `index.html` | `1562:1292` |
| info | `info.html` | `1562:1293` |
| løype | `loype.html` | `1562:1294` |
| resultater | `resultater.html` | `1562:1570` |

## Run it

Any static server will do:

```bash
python3 -m http.server 4321
```

Then open <http://localhost:4321>.

## Layout

```
index.html info.html loype.html resultater.html
css/tokens.css      every value that comes from Figma, as a custom property
css/base.css        reset + element defaults — the only place tag selectors live
css/styles.css      the block layer: BEM blocks for this site
assets/images/      hero.jpg, course-map.jpg — exported from the Figma nodes
assets/logos/       sponsor-1..6.svg, download.svg — exported as vector
```

## The font matters more than it looks

The design is set in **Inter Variable** and depends on its `opsz` (optical
size) axis. Google Fonts' default request (`family=Inter:wght@500;900`) serves
*static* instances cut at `opsz 14`, which render the display type about **3%
wider** than the Figma frames — enough to throw off the hand-placed hero lines.

The pages therefore request the variable font with the axis exposed:

```
family=Inter:opsz,wght@14..32,500;14..32,900
```

and leave `font-optical-sizing` at its default `auto`. That reproduces Figma to
within a pixel. Do not "simplify" the font URL back to the static form.

## Components

`css/styles.css` implements Figma's component sets, keeping their variant
names as BEM modifiers so the two can be read side by side.

### Button — `.pill` (node `1562:1046`)

Two variant axes: **Property 1** (Default / Primary / Invert) becomes the
modifier, **Property 2** (Default / Hover) becomes `:hover`.

| Figma variant | class | resting | hover |
|---|---|---|---|
| Default | `.pill` | white bg, black text | mint `#82f682`, black text |
| Invert | `.pill--invert` | black bg, white text | white bg, black text |
| Primary | `.pill--primary` | pink bg, black text, 56px | black bg, pink text |

Figma swaps variants with no transition, so the CSS does not animate either.
`.pill--static` is the one addition: it holds a Primary button at its resting
state for the index tagline, which has no target to be interactive for.

Everything that looks like a button is this one block, mixed onto its slot —
`site-nav__brand`, `site-nav__link`, `filter__label`.

### Row — `.results-table__row` (node `1566:2430`)

Carries an `ON_HOVER` reaction to `#fff0fa`. Only the data rows
(`--data`) take it; the header row (`--head`) does not.

### Toggle and Nav (nodes `1566:2386`, `1562:1206`)

Both are variant sets over *which item is selected*, with no states of their
own — the buttons inside carry the states. So the nav is one `.pill--invert`
plus `aria-current="page"`, and the toggle is three radios whose checked label
is the Invert variant.

## CSS: classic BEM

Three shapes, and nothing else:

```css
.block            /* an independent, reusable thing        */
.block__element   /* a part that only exists in that block */
.block--modifier  /* a variant of a block or element       */
```

- **One class per selector.** No descendant, child or tag selectors, so
  changing an element in the markup can never move the styling.
- **Flat elements.** `.card__title`, never `.card__body__title`.
- **No ids, no `!important`, no nesting.** Equal weight everywhere, so source
  order is the only thing that resolves a conflict.
- **Mix blocks instead of wrapping them.** `class="site-nav__link pill"` puts a
  block in a slot without adding a div.
- **Values come from tokens.** A raw hex or px in `styles.css` is a bug.

`base.css` is the exception and the reason it is its own file: it styles raw
elements so unclassed markup behaves, and it is the only file allowed to.

There are **two** deliberate departures, both marked at the site and both of
the same kind — a state on one named class deciding the appearance of another,
which cannot be expressed without JavaScript. Neither uses an id or a tag
selector, so the rule that actually matters still holds:

```css
.filter__input:checked + .filter__label            /* the toggle's own state */
.results:has(.filter__input--women:checked) .results-table__row--men
```

## HTML: real markup

- **Landmarks, once each.** One `<header>`, one `<main>`, one `<footer>`,
  `<nav>` for navigation. Never a `<header>` inside `<main>`.
- **One `<section>` per Figma frame section**, each named with
  `aria-labelledby` pointing at its own heading.
- **Lists are lists.** Nav, sponsors and the hero actions are `<ul>`. The info
  page is a `<dl>` — a bold term followed by its text is a description list.
  The results are a real `<table>` with `<caption>`, `<colgroup>` and
  `<th scope="col">`.
- **`<time datetime>`** for the event date, `<article>` for anything
  self-contained, `<figure>` for the map.
- **Decoration comes from CSS.** The `__` before a page title and the `___`
  before an info term are `::before` content, so the accessible name is
  `info`, not `underscore underscore info`.
- **The only div per block is its content-column wrapper.** Four pages, one
  `page__body` each, plus one scroll box around the results table.

## Values live in tokens.css

Colours, sizes and spacing all resolve to a custom property named after its
Figma counterpart. When a frame needs a value that has no token, add the token
first — a hardcoded hex in `styles.css` is how a site and its Figma file drift
apart without anyone noticing.

The design is `#000000`, `#ffffff` and the accent `#ff67cf`, plus two colours
that appear only on hover: `#82f682` (Button, Default variant) and `#fff0fa`
(Row). It is light-only; the Figma collection has a Light mode and nothing
else, so there is no `prefers-color-scheme` block.

## Gaps

Where the design could not be reproduced exactly, or where the Figma file is
missing something, it is recorded here rather than quietly approximated. Each
one is also commented at the site in the markup.

| # | Gap | Where | What it needs |
|---|---|---|---|
| 1 | The **GPX file does not exist**. The download link is wired to `assets/lastone-krs-course.gpx`, which is not in the repo — the link 404s today. | `loype.html` | Drop the course export in at that path. |
| 2 | **The class each runner is filtered into is inferred from their name.** The results data has no class field, so Damer / Herrer is decided by given-name convention (and, for one Lithuanian name, by the surname's masculine form). It is a heuristic: it will be wrong for anyone whose name does not follow the convention, and nobody chose it for themselves. 18 are listed as Damer, 31 as Herrer. | `resultater.html` | A real class field from the race registration, which is the only authority on this. |
| 2b | Under a filter the **rank column keeps the overall placement**, so the numbers have gaps (1, 3, 4, 7…). Whether a class view should renumber from 1 is not specified in the design. | `resultater.html` | A decision, then either renumbering in the data or per-class pages. |
| 3 | The index **"med deg på" is a Primary Button in Figma with no target**. Rendered as a static tagline via `.pill--static`, so the Primary hover — black bg, pink text — is implemented but has no live instance on the site. | `index.html` | A påmelding URL. Drop `.pill--static`, make it an `<a>`, and the hover comes back on its own. |
| 4 | **No mobile design exists.** The two 402px `small` frames on the Figma page still contain 1512px children, so they are explorations, not a spec. Everything under the 768px breakpoint is a decision made in code. | `css/styles.css` | Small-screen frames, if the layout below 768px matters. |
| 5 | **Typo corrected, not reproduced.** The Premier entry reads "Det b lir også uttrekkspremier" in Figma; the page says "Det blir". | `info.html` | Fix it in Figma so the two agree. |
| 6 | The file's **variable collection is unused**. "Collection 1" is built around a purple accent (`#4C3267`); these four frames use flat black, white and `#ff67cf` with no variables bound. | `css/tokens.css` | Decide whether the pages should adopt the collection or the collection should be replaced. |
| 7 | **Four spacing values sit off the SP scale**: 10, 36, 80 and 160px, against a scale of 4/8/12/16/20/24/32/40/48/64. They are tokenised as `--space-off-*` so they stay countable. | `css/tokens.css` | Either add them to the Figma scale or move the frames onto it. |
