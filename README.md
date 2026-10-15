# vue-tsc setup ref regression

A setup-time assignment narrows a ref in the generated template, even though an async callback can change its value before a later render.

## Reproduce

```sh
npm ci
npm run typecheck
```

Expected: no errors. The template can check for the `'error'` state assigned by the mounted callback.

Actual with vue-tsc 3.3.13:

```text
App.vue(14,15): error TS2367: This comparison appears to be unintentional because the types '"uploading"' and '"error"' have no overlap.
```

Vue is pinned to 3.5.44 and TypeScript to 6.0.3. vue-tsc 3.3.12 also fails.

## Compare with 3.3.11

```sh
npm install --no-save --package-lock=false vue-tsc@3.3.11
npm run typecheck
```

This passes. Run `npm ci` to restore 3.3.13.

## Regression commit

Introduced by [cb81918: remove linkedCodeMap and __VLS_SetupExposed (#6175)](https://github.com/vuejs/language-tools/commit/cb81918cc65a84574f3658889c41996e3973bda5). Source builds of its immediate parent, `8c85e388`, pass; `cb81918` fails with the same dependencies.

The generated check changed from `__VLS_ctx.status` to `status.value`, carrying setup-time narrowing into the template.
