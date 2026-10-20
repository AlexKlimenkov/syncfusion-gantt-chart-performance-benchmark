# Reproducing the figures in issue #4

This branch starts from `ab9544b`, the upstream `master` on 2026-10-20, when [issue #4](https://github.com/SyncfusionExamples/gantt-chart-performance-benchmark/issues/4) was opened. It adds only what is needed to build the DHTMLX and Syncfusion apps with the versions that the blog post gave on that day: DHTMLX 10.0.2 and Syncfusion 35.1.37.

Upstream `master` has changed since then: PR #5 (2026-10-21) pins Syncfusion to 35.1.41. The blog post has given Syncfusion 35.1.41 since 2026-10-22. This branch keeps 35.1.37, the version that the figures in #4 use.

## What is changed

- `dhtmlx-gantt-benchmark/package.json`: `@dhtmlx/trial-react-gantt` is pinned to `10.0.2`. On 2026-10-20 the `"*"` installed 10.0.3.
- `syncfusion-gantt-benchmark/package.json`: `@syncfusion/ej2-react-gantt` is pinned to `35.1.37`, and npm `overrides` pin every `@syncfusion/*` package in the dependency tree to `35.1.37`. Without the overrides, the `~35.1.37` ranges inside the Syncfusion packages install 35.1.41.
- `package-lock.json` in both apps: the dependency trees of the installs that produced the figures in #4 (React 19.3.1, `react-scripts` 5.0.1). One entry differs: TypeScript is 4.9.5 here and was 7.0.2 in those installs, because `npm ci` rejects 7.0.2 (`react-scripts` asks for `^3.2.1 || ^4`). The apps are plain JavaScript and do not use TypeScript; the JS bundles built from this branch are the same as the bundles that were measured.
- `.env` in both apps: `DISABLE_ESLINT_PLUGIN=true`. With the packages that npm resolved on 2026-10-20, the ESLint step of `react-scripts` fails with `Environment key "jest/globals" is unknown`, in `npm run build` and in `npm start`. The lint step does not change the bundle.

The sources of all apps are unchanged, and the Bryntum and Kendo apps are not touched.

## Running

One app at a time, with DevTools closed:

```sh
cd dhtmlx-gantt-benchmark        # or syncfusion-gantt-benchmark
npm ci
npm run build
python -m http.server 4211 --directory build    # or any other static server
```

Open `http://localhost:4211/` and read the render time on the page:

- Stress test: open **Stress Test** and click **Run 50K / 10 levels** or **Run 100K / 10 levels**.
- Initial Load: open **Initial Load** and click a size button.

The figures in #4 were measured on Windows 11, AMD Ryzen 7 4800H laptop, with Node 24.3.0 and npm 11.4.2. The Chromium 151 figures are each the median of 3 runs, every run in a fresh browser window; the Edge 155 figures are one run.

`npm run build` also rewrites `public/bundle-report.html` and `public/bundle-stats.json`. That is the repository's own build script.
