# FastYoke Docs (markdown mirror)

This repo is the markdown form of every public page on
[fastyoke.io](https://fastyoke.io). It exists so AI agents,
crawlers, and any reader who prefers plain text can read the
docs without rendering JavaScript.

The content is automatically synced from FastYoke's private
monorepo on every push to `main` — last updated
2026-05-11T06:49:15.282Z.

## Where to start

- [`llms.txt`](./llms.txt) — index of every page in this repo,
  per the [llms.txt convention](https://llmstxt.org).
- [`llms-full.txt`](./llms-full.txt) — concatenated full text
  of every page in one file.
- [`docs.md`](./docs.md) — the docs landing page; see also
  [`docs/`](./docs) for per-section pages.
- [`index.md`](./index.md), [`pricing.md`](./pricing.md),
  [`privacy.md`](./privacy.md) — marketing surfaces.

## URL ↔ file mapping

Pages live at the same path here as on fastyoke.io. Replace
the host with this repo's tree URL and add `.md`:

| Live URL | Mirror file |
|---|---|
| `https://fastyoke.io/docs/lcap` | [`docs/lcap.md`](./docs/lcap.md) |
| `https://fastyoke.io/docs/forms` | [`docs/forms.md`](./docs/forms.md) |
| `https://fastyoke.io/pricing` | [`pricing.md`](./pricing.md) |

## What this repo is not

- Not the source of truth — edits here are overwritten on every
  sync. File issues and PRs against the private monorepo (or
  via support@fastyoke.io for non-developers).
- Not a complete copy of the platform — the FastYoke runtime,
  SDK source, CLI source, and admin UI live in the private
  monorepo. The publicly-mirrored SDK source is at
  [versacomp/fastyoke-sdk](https://github.com/versacomp/fastyoke-sdk);
  the CLI is at
  [versacomp/fastyoke-cli](https://github.com/versacomp/fastyoke-cli).

## License

Documentation is licensed CC BY 4.0 unless a specific page
notes otherwise. See `LICENSE` for the full text.
