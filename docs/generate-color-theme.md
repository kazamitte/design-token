# Color theme generation

`generateColorTheme` is a function that returns CSS as a string, allowing the application to generate its own custom themes. A sample showing how to write the output to a file is provided below.

```js
import { writeFile } from 'node:fs/promises';
import { generateColorTheme } from '@kazamitte/design-token';

const css = generateColorTheme({
  primary: 'teal',
  secondary: 'violet',
  base: 'mist',
  link: 'blue',
});

await writeFile('src/styles/generated/theme.css', css);
```

For information on color pairings, please refer to [color-pairings.md](./color-pairings.md). It lists recommended secondary, base, and link colors for each of the 17 primary colors.
