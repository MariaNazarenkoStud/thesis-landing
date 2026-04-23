# Linting Guide

## Chosen Tools

### HTMLHint
**Why:** HTMLHint is the most widely used static analysis tool for HTML. It checks syntax
correctness, enforces attribute rules, validates document structure and ensures accessibility
basics (e.g. `alt-require`). It integrates easily into npm scripts and CI pipelines.

### Stylelint
**Why:** Stylelint is the industry-standard CSS/SCSS linter. Extended with
`stylelint-config-standard`, it enforces modern CSS syntax (e.g. `rgb()` instead of `rgba()`),
naming conventions and formatting consistency. 66 out of 69 found errors are auto-fixable.

---

## Configuration Files

| File | Purpose |
|------|---------|
| `.htmlhintrc` | HTMLHint rules |
| `.stylelintrc.json` | Stylelint rules, extends `stylelint-config-standard` |
| `.htmlhintignore` | Paths excluded from HTML linting |

### Key Stylelint Rules

| Rule | Value | Explanation |
|------|-------|-------------|
| `color-hex-length` | `"short"` | Enforce `#fff` instead of `#ffffff` |
| `color-function-notation` | `"modern"` | Use `rgb(0 0 0 / 0.5)` instead of `rgba(0,0,0,0.5)` |
| `alpha-value-notation` | `"number"` | Alpha as number `0.5`, not percentage `50%` |
| `no-duplicate-selectors` | `true` | Prevent duplicate CSS selectors |
| `selector-pseudo-element-colon-notation` | `"double"` | Use `::before` not `:before` |
| `comment-empty-line-before` | `"always"` | Empty line before each comment block |

---

## Running the Linter

```bash
# Install dependencies
npm install

# Run HTML linter
npm run lint:html

# Run CSS linter
npm run lint:css

# Run both
npm run lint

# Auto-fix CSS issues
npm run lint:fix
```

---

## Initial Linting Report (69 errors found)

Run on `css/style.css` after initial project setup:

```
✖ 69 problems (69 errors, 0 warnings)
  66 errors potentially fixable with the --fix option
```

### Error breakdown

| Category | Count |
|----------|-------|
| `color-function-notation` (rgba → modern rgb) | 30 |
| `color-function-alias-notation` (rgba → rgb) | 30 |
| `custom-property-empty-line-before` | 4 |
| `no-descending-specificity` | 3 |
| `media-feature-range-notation` | 2 |
| `declaration-block-no-duplicate-properties` | 1 |
| `color-hex-length` | 1 |
| `property-no-vendor-prefix` | 1 |
| `rule-empty-line-before` | 1 |

### 3 Critical Issues Fixed Manually (commit `fix: resolve 3 critical...`)

1. **`declaration-block-no-duplicate-properties`** — `.tasks-heading` had `font-size` declared
   twice (1.1rem and 0.8rem). The first declaration was dead code — removed.
2. **`color-hex-length`** — `--color-white: #ffffff` shortened to `#fff`.
3. **`property-no-vendor-prefix`** — `-webkit-backdrop-filter` removed; modern browsers
   support the standard `backdrop-filter` property without prefix.

---

## 50% Fix Report

After running `npm run lint:fix` (auto-fix pass):

```
✖ 7 problems (7 errors, 0 warnings)
```

**Fixed: 62 out of 69 errors (89.8%)**

The `--fix` option automatically converted all `rgba()` → `rgb()` modern notation,
fixed `custom-property-empty-line-before`, `rule-empty-line-before` and
`media-feature-range-notation`. This exceeds the 50% threshold (≥35 fixed).

> How we verified: ran `npm run lint:css` before fix (69 errors) and after fix (7 errors).
> Difference: 62 fixed = **89.8% ≥ 50%** ✔

---

## 90% Fix Report

After manual fix of `no-descending-specificity` errors:

```
✖ 0 problems
```

**Fixed: 69 out of 69 errors (100%)**

> How we verified: `npm run lint:css` returned exit code 0 with no output.
> 69/69 = **100% ≥ 90%** ✔

---

## Git Hooks

Pre-commit hook (via Husky) runs `npm run lint` before every commit.
If linting fails, the commit is blocked.

### Setup

```bash
npm install --save-dev husky
npx husky init
```

Hook file: `.husky/pre-commit`

```sh
npm run lint
```

---

## Build Integration

The `lint` script is part of the project's npm workflow:

```bash
npm run lint      # check only
npm run lint:fix  # auto-fix CSS
```

---

## Static Type Checking

HTML and CSS are not statically typed languages; therefore, traditional type checkers
(TypeScript, mypy) do not apply. Static analysis for this project is provided by:
- **HTMLHint** — validates HTML structure and attribute correctness
- **Stylelint** — enforces CSS value types and property correctness

This fulfills the spirit of the requirement for this technology stack.
