# Reproducing the figures in issue #4

This branch goes with [issue #4](https://github.com/SyncfusionExamples/gantt-chart-performance-benchmark/issues/4). It starts from `ab9544b`, the upstream `master` on 2026-10-20, when the issue was opened. It adds only what is needed to build the DHTMLX and Syncfusion apps with the versions that the blog post gives. The sources of all apps are unchanged, and the Bryntum and Kendo apps are not touched.

The post changed its Syncfusion version on 2026-10-22, so the branch has two commits, one for each set of versions:

| Commit | DHTMLX | Syncfusion | Versions in the post | Figures |
|---|---|---|---|---|
| `977e437` | 10.0.2 | 35.1.37 | until 2026-10-22 | issue body, 2026-10-20 |
| branch tip | 10.0.2 | 35.1.41 | since 2026-10-22 | comment of 2026-10-27 |

To build the first set, run `git checkout 977e437` first. Upstream `master` has also moved since `ab9544b`: PR #5 (2026-10-21) pins `@syncfusion/ej2-react-gantt` to 35.1.41 and changes the README. It changes no app source.

## What is changed

- `dhtmlx-gantt-benchmark/package.json`: `@dhtmlx/trial-react-gantt` is pinned to `10.0.2`. On 2026-10-20 the `"*"` installed 10.0.3.
- `syncfusion-gantt-benchmark/package.json`: `@syncfusion/ej2-react-gantt` is pinned, and npm `overrides` pin every `@syncfusion/*` package in the dependency tree.
  - At `977e437`: every `@syncfusion/*` package is pinned to `35.1.37`.
  - At the branch tip: npm has no single 35.1.41 set. `ej2-gantt` and `ej2-react-gantt` have a 35.1.41 release; other packages stop at 35.1.37, 35.1.39 or 35.1.40; some have 35.1.42 since 2026-10-27. So each package is pinned to its newest version published before the post's update (2026-10-22 10:43 UTC): 35.1.41 for `ej2-gantt`, `ej2-react-gantt`, `ej2-buttons`, `ej2-calendars`, `ej2-dropdowns`, `ej2-filemanager`, `ej2-grids`, `ej2-inputs` and `ej2-layouts`; 35.1.40 for `ej2-navigations` and `ej2-richtexteditor`; 35.1.39 for `ej2-popups`; 35.1.37 for the other 16.
  - Without the overrides, the `~35.1.x` ranges inside the Syncfusion packages install newer versions: 35.1.41 on 2026-10-20, the 35.1.42 releases since 2026-10-27.
- `package-lock.json` in both apps: the dependency trees of the measured installs (React 19.3.1, `react-scripts` 5.0.1). The DHTMLX lockfile is the same in both commits; the Syncfusion lockfiles differ only in the `@syncfusion/*` versions. TypeScript is 4.9.5 in both lockfiles. The installs measured on 2026-10-20 had 7.0.2, which `npm ci` rejects (`react-scripts` asks for `^3.2.1 || ^4`). The apps are plain JavaScript and do not use TypeScript; the JS bundles built from this branch are the same as the bundles that were measured.
- `.env` in both apps: `DISABLE_ESLINT_PLUGIN=true`. With the packages that npm resolved on 2026-10-20, the ESLint step of `react-scripts` fails with `Environment key "jest/globals" is unknown`, in `npm run build` and in `npm start`. The lint step does not change the bundle.

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

The figures in #4 were measured on Windows 11, AMD Ryzen 7 4800H laptop, with Node 24.3.0 and npm 11.4.2:

- Issue body (2026-10-20): the Chromium 151 figures are each the median of 3 runs, every run in a fresh browser window; the Edge 155 figures are one run.
- Comment of 2026-10-27: Chromium 151, each figure the median of 3 runs, every run in a fresh browser window, all apps in one session.

`npm run build` also rewrites `public/bundle-report.html` and `public/bundle-stats.json`. That is the repository's own build script.
