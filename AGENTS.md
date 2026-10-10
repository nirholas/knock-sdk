# AGENTS.md

Instructions for AI coding agents working in or with `@three-ws/knock`.

## What this is

Reach a real person, and pay their price to do it. Every three.ws account can publish a priced door; paying it buys one message through, delivered in person by their 3D companion. Wallet-agnostic: bring any x402 client.

## Use it

```bash
npm install @three-ws/knock
```

Full API reference: [README.md](README.md). Machine-readable summary: [llms.txt](llms.txt). Full text: [llms-full.txt](llms-full.txt).

## Work on it

- Node 20 or newer, ESM only.
- Run the tests with `npm test` before proposing a change.
- Keep runtime dependencies at their current count. Prefer a well-maintained library over new hand-rolled code, and say why in the pull request.
- No mocks, placeholders or TODO comments in committed code.
- Do not use em-dashes or en-dashes in code, comments or docs.

## Source of truth

This repository is exported from https://github.com/nirholas/three.ws/tree/main/packages/knock-sdk. Bug reports belong here; larger changes should be proposed against the monorepo path.
