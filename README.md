# ToolCall-15

![ToolCall-15 screenshot](./screenshot.png)

ToolCall-15 is an official BenchLocal Bench Pack for tool use and tool-call loop scoring. The repo keeps one benchmark core and exposes it through a standalone web app, a BenchLocal adapter, and a CLI runner.

## What It Measures

ToolCall-15 is organized into 5 categories, with 3 scenarios per category:

- Tool Selection
- Parameter Precision
- Multi-Step Chains
- Restraint and Refusal
- Error Recovery

Each scenario is scored as:

- `2` points for a pass
- `1` point for a partial pass
- `0` points for a fail

Each category is worth `6` points. The final score is the average of the 5 category percentages, rounded to a whole number.

## Bench Pack Structure

```text
app/                    Next.js standalone app
components/             Standalone UI components
lib/                    Benchmark core, scoring, tool loop, and transport
benchlocal/             Thin BenchLocal SDK adapter
cli/                    Non-UI runner
benchlocal.pack.json  Static install/discovery manifest
METHODOLOGY.md          Published benchmark methodology
```

## BenchLocal Adapter

- `benchlocal/index.ts` is the only place that imports `@benchlocal/sdk`.
- `lib/` stays framework-agnostic and is shared by the web app, CLI, and BenchLocal.
- `benchlocal.pack.json` is the canonical Bench Pack metadata manifest used for install, inspection, and runtime metadata.
- Per-pack default sampling belongs on the manifest. ToolCall-15 defaults to `temperature: 0`.

## Methodology

The benchmark spec is documented in [METHODOLOGY.md](./METHODOLOGY.md) and implemented in [lib/benchmark.ts](./lib/benchmark.ts).

### Design goals

- Reproducible: the system prompt, tool schema, mocked tool outputs, and scoring logic are all versioned in the repo.
- Visual: the dashboard makes the outcome of each scenario obvious without external scoring scripts.
- Balanced: the suite spreads scenarios across distinct tool-use failure modes instead of over-indexing on one skill.
- Deterministic: tool results are mocked and the benchmark uses `temperature: 0`.
- Inspectable: every scenario stores a raw trace so failures can be audited.

## Standalone App

### Requirements

- Node.js 20 or newer
- npm
- At least one reachable OpenAI-compatible provider

### Install

```bash
npm install
cp .env.example .env
```

### Run

```bash
npm run dev
```

Open `http://localhost:3000`.

## BenchLocal and CLI

- BenchLocal build: `npm run build:benchlocal`
- CLI runner: `npm run cli`

## Validation

```bash
npm run lint
npm run typecheck
npm run build:benchlocal
```

## Repository Structure

- [app/](./app) contains the Next.js app router entry points and styles.
- [components/dashboard.tsx](./components/dashboard.tsx) renders the benchmark UI and live event handling.
- [app/api/run/route.ts](./app/api/run/route.ts) streams benchmark progress over Server-Sent Events.
- [lib/benchmark.ts](./lib/benchmark.ts) defines the benchmark spec, mocked tools, and scoring logic.
- [lib/orchestrator.ts](./lib/orchestrator.ts) runs scenarios and captures traces.
- [lib/llm-client.ts](./lib/llm-client.ts) contains the OpenAI-compatible client adapter.
- [lib/models.ts](./lib/models.ts) parses provider configuration and model groups.

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE).

## Author

Created by [stevibe](https://x.com/stevibe).
