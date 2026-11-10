---
name: knock-a-person
description: Look up a three.ws Knock door, show the price, and send one message to a real person with @three-ws/knock. Use when an agent needs a human's attention. Paid doors require explicit user approval of recipient, amount, token and chain.
---

# Knock a person with @three-ws/knock

Knock lets an agent reach a real person: the recipient sets a price, the sender pays it, and the message is delivered by their 3D companion. Node 20+, no dependencies. The package never signs or picks a chain: paid doors need an x402-capable `fetch` you supply.

```bash
npx @three-ws/knock quote nirholas
npx @three-ws/knock directory --limit 20
npx @three-ws/knock send nirholas "Two questions about the facilitator." --from "Ada" --subject "Your x402 settle path"
npx @three-ws/knock receipt "<receipt_url>"
```

Add `--json` to any command for machine-readable output.

## Spending rule (mandatory)

Paying a door moves real funds. Before any priced knock:

1. Run `quote <handle>` (free, no account) and read the live price.
2. Show the user: recipient, amount, token, chain (`confirmationFor(door)` returns exactly these, plus the endpoint).
3. Stop and wait for an explicit yes. Never pass `--yes` or call `knock` with `fetchWithPayment` without it.

The CLI itself prints those facts and waits for `y`; with no TTY it refuses. A free door sends immediately.

## Library

```js
import { quote, knock, receipt, directory, confirmationFor } from '@three-ws/knock';

const door = await quote('nirholas');   // { price, networks, max_chars, endpoint }
const c = confirmationFor(door);        // { recipient, amount, token, chains, endpoint, note }

const result = await knock({
  to: 'nirholas',
  from: 'Ada (research agent)',          // required
  message: 'Two questions about the facilitator.', // required
  subject: 'Your x402 settle path',
  fetchWithPayment,                      // required for priced doors, from your x402 client
  maxPriceAtomics: 100_000n,             // checked against the live price before paying
  requestId: 'ada-2026-08-28-001',       // idempotency key, a retry never double-pays
});
const state = await receipt(result.receipt_url); // status: pending | read | replied | dismissed
```

Other `knock` options: `url`, `senderKind` (`agent` | `human` | `unknown`, default `agent`), `origin` (default `https://three.ws`). Always set `maxPriceAtomics` on unattended knocks.

## Paying from the CLI

`send ... --payer ./payer.mjs`, where the module exports `fetchWithPayment` (or a default function), for example `wrapFetchWithPayment(fetch, wallet)` from `x402-fetch`.

## Errors

`KnockError` carries `code`, `status`, `data`. Codes: `bad_handle`, `no_door`, `door_closed`, `door_full`, `message_too_long`, `message_too_short`, `over_budget`, `payment_required`. Refusals happen before the 402 is issued, so a failed knock is never charged.
