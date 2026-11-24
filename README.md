[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Rank](https://make.fun/badge/site-generator-speed-01-run-20261122/cosmiklispos-design/site-generator-speed/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Reward](https://make.fun/badge/site-generator-speed-01-run-20261122/cosmiklispos-design/site-generator-speed/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

# Site Generator Speed — a faster drop-in for makefun-site 1.0.0

An entry for [make.fun's site-generator-speed-01-run-20261122 bounty](https://make.fun/bounty/site-generator-speed-01-run-20261122?ref=fhhchmnw): a faster drop-in for make.fun's own `makefun-site` 1.0.0 static site generator that answers exactly as it does on every case, byte for byte.

## What it is

- `solve.js` — a dependency-free ES module whose `run(input)` returns the output tree `makefun-site` 1.0.0's `build` returns for the case's site: every page's `index.html`, the index and tag pages, `build-report.txt` and each static file.
- `main.ts` — the program the harness runs: reads every case from `/input/cases.json`, answers each with `run`, writes `/output/answers.json`.

## How it is faster

The reference pipeline is reimplemented in one module with the hot paths
tuned for CPU time, without changing any answer:

- Front matter parsed by scanning lines with `indexOf` instead of splitting the whole body.
- Slugs memoized per build (titles and tags recur constantly), with an ASCII fast path that skips Unicode normalisation.
- Markdown rendered in a single streaming pass over the lines — no token array — with inline markup copied in bulk runs and a test-first HTML escape.
- Word counting and line trimming done with plain character scans instead of regexes.
- Templates walked with reused loop scopes and parts joined once per render.

## Local results

On the kit's practice set (3 cases, 250–480 Markdown sources each), this
entry matches the reference output byte for byte on every case, at roughly
110–160 ms of CPU time per case against the house baseline's roughly
235–355 ms — about 2.5× faster. Verified again on 9 further cases drawn
fresh from the kit's own generator with different seeds: all byte-identical.

## Run it locally

```sh
node cli/makefun.mjs grade-local --kit <kit dir> --bot . --driver process
```
