# AGENTS.md

tick is a library of framed figures (plot, stub, tui) for docs and dashboards: plain CSS plus one optional ES module, no build step, served by Cloudflare as static files from `public/`.

## Commands

- Test: `bun test` (runs `test/tick.test.js`)
- Regenerate `public/llms.txt`: `bun run llms`
- Run locally: `bunx wrangler dev`
- Deploy: `bunx wrangler deploy` (CI-driven for `main`, do not run by hand)
- There is no build and no linter.

## Layout

- `public/tick.css`: the library stylesheet. Every figure must render with this file alone.
- `public/tick.js`: the optional script (drag, punch, zone readouts, row selection, live graphs).
- `public/index.html`: the demo site. Each figure sits in a `<div class="demo" data-name="...">` block.
- `public/site.css`, `public/site.js`: styles and script for the demo site only.
- `public/llms.txt`: generated from the demos in `index.html`. Do not edit by hand.
- `public/skill.md`: the skill file that tells agents which figure fits which data.
- `public/fonts/`: subsetted fonts with their OFL licenses. `scripts/fonts.sh <ttf folder>` rebuilds them and needs `pyftsubset`.
- `public/_headers`, `wrangler.jsonc`: Cloudflare headers and routing.
- `test/tick.test.js`: unit tests for the pure helpers in `tick.js`.
- `docs/media/`: README images.

## Rules

- Keep the library dependency-free and build-free. No framework, no bundler.
- The stylesheet must work without the script. The script only adds behavior.
- Support a static mode (`data-tk-static` on any ancestor) and a theme (`data-tk-theme`) for every figure you add.
- After you change a demo in `public/index.html`, run `bun run llms` and commit the new `public/llms.txt`. CI fails if the two differ.
- `bun run llms` bakes in the origin `https://tick.crwl.tech`. Keep that argument.
- Keep the library near 7 KB gzipped. Say so in the PR if a change grows it.
- Fonts are loaded per face, only when a figure uses them. Keep it that way.
- Pushes to `main` deploy to production. Other branches get a preview build.

## Before you open a PR

CI (`.github/workflows/ci.yml`) runs these two checks:

1. `bun test`
2. `bun run llms && git diff --exit-code public/llms.txt`
