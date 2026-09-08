# Fixing class conflicts with tailwind-merge

- Classes made with the `@utility` directive are invisible to tailwind-merge. By default `text-body-16` and `text-dense-14` do not cancel each other.
- Semantic names generated from a `@theme` namespace have the same problem. `rounded-surface`, `shadow-raised`, `ease-standard`, `animate-fade-in` and `z-modal` are not on a scale tailwind-merge knows.
- `conflictingClassGroups` copies what Tailwind's own `text-lg` does: a later `text-*` cancels an earlier `leading-*`.

## Use tailwind-merge's own group names

Every key in `extend.classGroups` **must match an existing group name exactly**. A key that does not match adds a new group instead of extending the old one.

Say you write `'transition-duration'`. Then `duration-instant` and `duration-enter` do cancel each other, but they sit in a different group from Tailwind's `duration-200`, so `duration-200 duration-transition` keeps both classes. The correct key is `'duration'`.

Read the keys of `getDefaultConfig().classGroups` to check a name. These are the ones this package needs.

| Token                        | Group name      |
| ---------------------------- | --------------- |
| `text-body-16` and the rest  | `font-size`     |
| `rounded-surface` and so on  | `rounded`       |
| `shadow-raised` and so on    | `shadow`        |
| `duration-enter` and so on   | `duration`      |
| `ease-standard` and so on    | `ease`          |
| `animate-fade-in` and so on  | `animate`       |
| `z-modal` and so on          | `z`             |
| `primary-bg-solid` and so on | `bg-color`      |
| `primary-fg` and so on       | `text-color`    |
| `primary-border-solid` …     | `border-color`  |
| `primary-focus-ring` …       | `outline-color` |

Two of these are easy to get wrong: the z-index group is `z`, not `z-index`, and the font size group is `font-size`, not `text`.

`animate-*` can also be registered through `extend.theme.animate`, which tailwind-merge reads as a scale. The config below uses `classGroups` for it so that every token is registered the same way. There is no theme key for z-index, so `z` has to go through `classGroups` in any case.

## Color roles need their full class names

A color role utility is named `{role}-{token}`, so its prefix is not Tailwind's `bg-*` or `text-*`. The short form (`{ bg: [...] }`) cannot say that, so the full class names are listed instead.

Without them an override like `tv({ base: 'primary-bg-solid', variants: { disabled: { true: 'base-bg-muted' } } })` does nothing. Both classes stay, and the CSS order decides which one wins.

## Config

```ts
import { createTV, type VariantProps } from 'tailwind-variants';

const ROLES = [
  'base',
  'primary',
  'secondary',
  'error',
  'warning',
  'success',
  'info',
];

const roleClasses = (suffixes: string[]) =>
  ROLES.flatMap((role) => suffixes.map((suffix) => `${role}-${suffix}`));

export const tv = createTV({
  twMerge: true,
  twMergeConfig: {
    extend: {
      classGroups: {
        'font-size': [
          {
            text: [
              'display-64',
              'display-56',
              'display-48',
              'display-44',
              'highlight-36',
              'highlight-32',
              'highlight-28',
              'highlight-26',
              'highlight-24',
              'highlight-22',
              'body-20',
              'body-18',
              'body-16',
              'dense-18',
              'dense-16',
              'dense-14',
              'dense-18-compact',
              'dense-16-compact',
              'dense-14-compact',
              'oneline-18',
              'oneline-16',
              'oneline-14',
              'mono-18',
              'mono-16',
              'mono-14',
            ],
          },
        ],
        duration: [{ duration: ['instant', 'transition', 'enter'] }],
        rounded: [
          { rounded: ['tight', 'control', 'surface', 'overlay', 'pill'] },
        ],
        shadow: [
          {
            shadow: [
              'flat',
              'flat-hover',
              'raised',
              'raised-hover',
              'floating',
              'floating-hover',
              'overlay',
            ],
          },
        ],
        ease: [{ ease: ['standard', 'emphasized'] }],
        animate: [
          {
            animate: [
              'fade-in',
              'fade-out',
              'scale-in',
              'scale-out',
              'slide-in-from-top',
              'slide-in-from-right',
              'slide-in-from-bottom',
              'slide-in-from-left',
              'slide-out-to-top',
              'slide-out-to-right',
              'slide-out-to-bottom',
              'slide-out-to-left',
            ],
          },
        ],
        z: [
          {
            z: [
              'hide',
              'base',
              'docked',
              'dropdown',
              'sticky',
              'banner',
              'overlay',
              'modal',
              'popover',
              'skip-nav',
              'toast',
              'tooltip',
              'max',
            ],
          },
        ],

        'bg-color': roleClasses([
          'bg',
          'bg-subtle',
          'bg-muted',
          'bg-selected',
          'bg-solid',
        ]),
        'text-color': [
          ...roleClasses(['fg-strong', 'fg', 'fg-muted', 'fg-subtle', 'fg-contrast']),
          'link-fg',
          'link-fg-strong',
        ],
        'border-color': roleClasses([
          'border-subtle',
          'border-muted',
          'border-selected',
          'border-solid',
        ]),
        'outline-color': roleClasses(['focus-ring']),
      },
      conflictingClassGroups: {
        'font-size': ['leading'],
      },
    },
  },
});

export type { VariantProps };
```

`example/src/lib/tv.test.ts` checks two things for every group: that the classes in it cancel each other, and that pairs which must **not** cancel both survive — `text-red-500` with `text-body-16`, `shadow-red-500` with `shadow-raised`, `animate-slide-in-from-bottom` with `[--slide-distance:1rem]`.
