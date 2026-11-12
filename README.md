# @kazamitte/design-token

A design token layer for Tailwind CSS v4 — color, typography, and style themes. Requires Tailwind v4.

Every color token clears WCAG AA for normal text. No single scale can do that across every hue, so the steps differ from color to color.

## Setup

Install the package alongside Tailwind CSS v4, which it expects as a peer dependency.

```sh
npm install @kazamitte/design-token tailwindcss
```

### CSS

Import Tailwind first, then the package. The package does not import Tailwind itself, so it is loaded once and you keep control over how it is imported.

```css
@import "tailwindcss";

@import "@kazamitte/design-token/index.css";
@import "@kazamitte/design-token/theme/color/default.css";
@import "@kazamitte/design-token/theme/style/soft.css";

@source '../node_modules/@kazamitte/design-token';

body {
  background-color: var(--r-base-bg);
  color: var(--r-base-fg);
}
```

The `@source` directive is required because Tailwind v4 excludes `node_modules` from scanning by default. See [Detecting classes in source files](https://tailwindcss.com/docs/detecting-classes-in-source-files) in the Tailwind docs.

### JavaScript

The JavaScript API exposes one function, for generating a color theme.

```ts
import { generateColorTheme } from "@kazamitte/design-token";
```

Everything the package does is delivered as CSS, so the JavaScript API is optional. Reach for it to generate several themes at once, or to swap themes at runtime from the app. See [Color theme generation](./docs/generate-color-theme.md) for usage.

### Further reading

- [Color theme generation](./docs/generate-color-theme.md) — writing a theme file with `generateColorTheme`
- [Color pairings](./docs/color-pairings.md) — which secondary, base and link colors suit each primary (Japanese)
- [Fixing class conflicts with tailwind-merge](./docs/tailwind-merge.md) — registering the package's utilities with tailwind-merge

---

## Layout

```text
tailwind/
├─ base/
│  ├─ initialize.css       clears the Tailwind color / font / text namespaces
│  ├─ color/
│  │  ├─ palette.css       the raw palette (every --color-*)
│  │  ├─ semantic.css      --c-<hue>-*, 26 hues x 15 tokens, light/dark
│  │  ├─ status.css        error / warning / success / info
│  │  └─ utility.css       role utilities (primary-bg, base-fg, …)
│  ├─ typography/
│  │  ├─ font.css
│  │  └─ utility.css
│  └─ style/
│     ├─ animation.css     the keyframes and the --animate-* steps on them
│     ├─ radius.css        the radius scale
│     ├─ shadow.css        the shadow scale and the colors it is built from
│     └─ z-index.css       the layering scale
├─ theme/
│  ├─ color/               base / primary / secondary / link
│  └─ style/               neutral / editorial / soft / dense
└─ index.css               loads the base, the dark variant, duration utilities
```

## Color

### Roles

- Theme colors
  - primary
  - secondary
  - base
  - link
- Status colors
  - error — red
  - warning — amber
  - success — green
  - info — blue

Each role carries 15 tokens.

| Group      | Tokens                                                                       |
| ---------- | ---------------------------------------------------------------------------- |
| Text       | `fg-strong`, `fg`, `fg-muted`, `fg-subtle`                                   |
| Background | `bg`, `bg-subtle`, `bg-muted`, `bg-selected`, `bg-solid`                     |
| Border     | `border-subtle`, `border-muted`, `border-selected`, `border`, `border-solid` |
| Contrast   | `fg-contrast`                                                                |
| Focus ring | `focus-ring`                                                                 |

### Pairs that clear the contrast requirement

These four clear the WCAG AA threshold for normal text (4.5:1) across all 26 hues, in both light and dark.

- `fg-strong` on `bg-selected` — emphasized text on a hover surface
- `fg` on `bg-muted` — body text on a selected surface
- `fg-muted` on `bg` — secondary text on a tinted surface
- `fg-contrast` on `bg-solid` — the label on a filled control

`fg-subtle` is exempt from the requirement: it is meant for decoration and disabled states.

Roles can take different hues, so text from one role often sits on a surface from another — base text on a primary surface, or a link on a base surface. These clear 4.5:1 for text from any hue on a surface from any other hue, in both light and dark.

- `fg-strong` on `bg`, `bg-subtle` or `bg-muted`
- `fg` on `bg` or `bg-subtle`
- `fg-muted` on `bg`
- `fg-contrast` on `bg-solid`

For links, `link-fg` is the `fg-muted` of the link hue and `link-fg-strong` is its `fg`, so a link on any `bg` clears, and `link-fg-strong` also clears on `bg-subtle`.

One exception: in dark, indigo `fg` on a yellow or lime `bg-subtle` falls to about 4.3:1.

For non-text contrast (WCAG 1.4.11, 3:1), `border-solid`, `bg-solid` and `focus-ring` share a step and stand out from `bg` and `bg-subtle` of any hue, and from `bg-muted` of the same hue or of a neutral. Use them for input borders, checkboxes, progress bars, focus indicators and the like. A focus ring drawn against a solid control matches its fill, so separate the two with an offset in a `bg` color.

In dark, `bg-solid` is a light fill and `fg-contrast` a dark label. No fill dark enough for a white label also stands out 3:1 from a dark surface.

`fg-subtle` is not held to 3:1.

### Choosing a border

- `border` is the default frame for containers: cards, lists, tables, code blocks, popovers, menus and toasts. It is decorative, not held to 3:1, and its step is chosen per hue so that it lands near 2.5:1 against that hue's `bg` (see the table below).
- `border-solid` is for controls that need 3:1 under WCAG 1.4.11, such as inputs and checkboxes.
- `border-subtle` and `border-muted` are for faint dividers.
- `border-selected` marks a selected state.

The steps run `border-subtle` < `border-muted` < `border-selected` < `border` < `border-solid` in strength, in every hue and both modes.

The `border` step differs by hue because the same palette step contrasts unevenly with each hue's `bg`. In light mode it is picked from 400 to 600 to land in about 2.1 to 3.0:1; orange through sky need a darker step than the rest. In dark mode it is 600 for every hue: the step above it is `border-selected`, and no mid step exists between them, so the warm and green hues stay above 3:1 there. Contrast is against the hue's `bg`.

| Hue | Light step | Light ratio | Dark step | Dark ratio |
| --- | --- | --- | --- | --- |
| red | 400 | 2.65 | 600 | 3.39 |
| orange | 500 | 2.73 | 600 | 4.36 |
| amber | 500 | 2.07 | 600 | 4.70 |
| yellow | 600 | 2.83 | 600 | 4.97 |
| lime | 600 | 2.96 | 600 | 4.78 |
| green | 500 | 2.12 | 600 | 4.64 |
| emerald | 500 | 2.34 | 600 | 4.13 |
| teal | 500 | 2.33 | 600 | 3.96 |
| cyan | 500 | 2.27 | 600 | 3.72 |
| sky | 500 | 2.54 | 600 | 3.46 |
| blue | 400 | 2.42 | 600 | 2.80 |
| indigo | 400 | 2.79 | 600 | 2.49 |
| violet | 400 | 2.60 | 600 | 2.60 |
| purple | 400 | 2.60 | 600 | 2.72 |
| fuchsia | 400 | 2.40 | 600 | 3.19 |
| pink | 400 | 2.52 | 600 | 3.32 |
| rose | 400 | 2.60 | 600 | 3.47 |
| slate | 400 | 2.51 | 600 | 2.67 |
| gray | 400 | 2.49 | 600 | 2.66 |
| zinc | 400 | 2.52 | 600 | 2.57 |
| neutral | 400 | 2.48 | 600 | 2.54 |
| stone | 400 | 2.48 | 600 | 2.58 |
| mauve | 400 | 2.48 | 600 | 2.46 |
| olive | 400 | 2.25 | 600 | 2.83 |
| mist | 400 | 2.35 | 600 | 2.67 |
| taupe | 400 | 2.45 | 600 | 2.50 |

Contrast is measured the way axe-core measures it: out-of-gamut colors are clipped to sRGB, nothing is rounded, and a pair passes only when its ratio is strictly greater than the threshold.

### Semantic tokens

All 26 hues expose the same 15 tokens under `--c-*`. Use them where a specific color is wanted directly — tags, categories, charts.

```html
<span class="bg-(--c-lime-bg-subtle) text-(--c-lime-fg)">Draft</span>
```

### Palette

Adjusted from the Tailwind 4.3.0 default theme. All 26 hues are defined, including mauve, olive, mist and taupe, which Tailwind added in 4.2.

Every hue also carries two steps Tailwind does not have — 350 and 750 — each the OKLCH midpoint of its neighbours. They let a semantic token sit between two Tailwind steps where neither one holds the contrast requirement across hues.

## Typography

Modeled on [digital-go-jp/tailwind-theme-plugin](https://github.com/digital-go-jp/tailwind-theme-plugin).

Each utility sets font-size, line-height and letter-spacing together.

```html
<h1 class="text-display-48">…</h1>
<p class="text-body-16">…</p>
```

| Utility            | Variants                  | Use              |
| ------------------ | ------------------------- | ---------------- |
| `text-display-*`   | 64, 56, 48, 44            | Hero, page title |
| `text-highlight-*` | 36, 32, 28, 26, 24, 22    | Headings         |
| `text-body-*`      | 20, 18, 16                | Body copy        |
| `text-dense-*`     | 18, 16, 14 (+ `-compact`) | Tables, dense UI |
| `text-oneline-*`   | 18, 16, 14                | Labels, buttons  |
| `text-mono-*`      | 18, 16, 14                | Code             |

## Style

A style preset sets spacing, radius, shadow, duration and easing together, so that the whole interface reads as one.

| Preset      | Character                                                              |
| ----------- | ---------------------------------------------------------------------- |
| `neutral`   | A balanced default                                                     |
| `editorial` | Sharp corners and the shallowest shadows; hierarchy comes from borders |
| `soft`      | Generous rounding and pronounced elevation                             |
| `dense`     | Minimal rounding and elevation, for information-dense UI               |

```html
<div class="rounded-surface shadow-raised hover:shadow-raised-hover"></div>
```

Radius and easing do not name their utilities after the `@theme` namespace they live in: `--radius-*` generates `rounded-*`, and `--ease-*` generates `ease-*`.

`--spacing` is the base step of the spacing scale and has no utility of its own. `p-4` is emitted as `calc(var(--spacing) * 4)`, so switching preset moves `p-*`, `m-*`, `gap-*`, `size-*` and the rest together — note that the sizing utilities read the same variable.

| Utility         | Variants                                       | Sets                         |
| --------------- | ---------------------------------------------- | ---------------------------- |
| `p-*` `gap-*` … | every utility reads `--spacing`, the base step | spacing and sizing           |
| `rounded-*`     | tight, control, surface, overlay, pill         | `border-radius`              |
| `shadow-*`      | flat, raised, floating (+ `-hover`), overlay   | `box-shadow`                 |
| `duration-*`    | instant, transition, enter                     | `transition-duration`        |
| `ease-*`        | standard, emphasized                           | `transition-timing-function` |
| `animate-*`     | fade / scale / slide, in and out               | `animation`                  |

Every duration collapses to 1ms under `prefers-reduced-motion: reduce`.

### Motion

`animate-*` covers the enter and exit of the things that appear over the page.

| Utility                     | Use                      |
| --------------------------- | ------------------------ |
| `animate-fade-in` / `-out`  | Scrims, tab panels       |
| `animate-scale-in` / `-out` | Dialogs, menus, popovers |
| `animate-slide-in-from-*`   | Drawers, sheets, toasts  |
| `animate-slide-out-to-*`    | The same, leaving        |

The slide steps take `top`, `right`, `bottom` and `left`, and travel the full
size of the element unless it says otherwise.

```html
<div class="animate-slide-in-from-bottom [--slide-distance:1rem]"></div>
```

Every step is composed from the preset's duration and easing, so it follows the
preset and collapses to 1ms under reduced motion. Tailwind's own `animate-spin`,
`-ping`, `-pulse` and `-bounce` are untouched and keep animating under reduced
motion.

Exit animations only play while the element is still in the tree; unmount it on
`animationend`.

Collapse and expand are not included. They need a measured height, which no
keyframe can supply.

### Layering

`z-*` follows [Chakra UI's z-index scale](https://chakra-ui.com/docs/theming/z-index), so the order matches what component libraries assume — notably `popover` above `modal`, since a select opened inside a dialog has to clear it.

| Step       | Value | Step       | Value      |
| ---------- | ----- | ---------- | ---------- |
| `hide`     | -1    | `overlay`  | 1300       |
| `base`     | 0     | `modal`    | 1400       |
| `docked`   | 10    | `popover`  | 1500       |
| `dropdown` | 1000  | `skip-nav` | 1600       |
| `sticky`   | 1100  | `toast`    | 1700       |
| `banner`   | 1200  | `tooltip`  | 1800       |
|            |       | `max`      | 2147483647 |

Layering does not vary by preset, so these are used directly rather than through
one. `z-10` and `z-auto` still work alongside them. `<dialog>` and the Popover
API render in the top layer, where z-index has no effect.

### Shadow

`--shadow-2xs` through `--shadow-2xl` are the base scale, seven steps. A preset picks one step per role and does nothing more; light and dark are resolved inside the scale, so no preset carries a dark override.

Each step reads its colors through the custom properties below. These four are all that `[data-mode='dark']` swaps.

| Custom property         | Light     | Dark      |
| ----------------------- | --------- | --------- |
| `--shadow-color-subtle` | black 5%  | black 35% |
| `--shadow-color`        | black 10% | black 50% |
| `--shadow-color-strong` | black 25% | black 70% |
| `--shadow-rim-color`    | black 7%  | white 12% |

A cast shadow barely reads on a dark surface, so every step opens with a `0 0 0 1px var(--shadow-rim-color)` rim. In dark it is that white rim that separates a surface from its background, while the heavier cast carries the elevation. `--shadow-2xs` is the rim alone — an edge with no elevation.

To recolor the shadows, redefine those four under `:root` and `[data-mode='dark']`; the whole scale, and every preset built on it, follows.

---

## AI assistance

The documentation and code in this package were written with AI agents — Claude and ChatGPT. Every output was reviewed by a human before it was committed.

## License

MIT. See [LICENSE](./LICENSE).

The color palette comes from Tailwind CSS and the typography scale from digital-go-jp/tailwind-theme-plugin, both MIT. Their notices are reproduced in [THIRD-PARTY-NOTICES.md](./THIRD-PARTY-NOTICES.md).
