# @core/checkout

[![genpm](https://api.genpm.net/v1/badge/@core/checkout.svg)](https://genpm.net/p/@core/checkout) ![license](https://img.shields.io/badge/license-MIT-blue)

> Stripe Checkout for one-off purchases: server-priced sessions, orders created only from the webhook, auto-refund if sold out

A [GenPM](https://genpm.net) module: **source code + AI rules + MCP config**, injected into your project instead of hidden in `node_modules`. The code lands in `src/lib/checkout`, pinned to a verified commit, and you own it.

## Install

```bash
genpm add @core/checkout
```

No genpm yet? `curl -fsSL https://genpm.net/install.sh | sh` (macOS/Linux), `irm https://genpm.net/install.ps1 | iex` (Windows), or one-off with `npx @genpm-net/cli add @core/checkout`.

Or let your AI agent do it: run `genpm mcp setup` once, then ask *"add @core/checkout with genpm"* in Claude Code, Cursor, VS Code, Windsurf, Codex or Gemini CLI. It shows you a dry-run plan before writing anything.

## What it does

Pays a @core/cart with Stripe Checkout (hosted page: no card data on your server). `startCheckout` recomputes totals,
blocks carts with issues or without shipping, sends server prices, a one-off coupon for the computed discounts and
the chosen shipping rate, and stores a snapshot. The order (@core/orders) is created ONLY in the Stripe webhook,
idempotently, from that snapshot after checking the charged amount; if the order can never be created (stock ran
out, a variant was withdrawn, invalid address…), the payment is refunded automatically, the customer and the team
are told and the webhook still answers 2xx (transient errors are rethrown so Stripe retries). Open sessions reserve
the discount codes they carry (`registerPendingCodeUses` of @core/cart); if a concurrent checkout took the last use,
the new session is expired and `startCheckout` throws `issues`. Also registers Stripe as the refund provider for the admin.

## At a glance

- **GenPM dependencies** (installed with it): [@core/cart](https://genpm.net/p/@core/cart), [@core/orders](https://genpm.net/p/@core/orders), [@core/shipping](https://genpm.net/p/@core/shipping), [@core/stripe](https://genpm.net/p/@core/stripe), [@core/catalog](https://genpm.net/p/@core/catalog), [@core/email](https://genpm.net/p/@core/email), [@core/jobs](https://genpm.net/p/@core/jobs), [@core/money](https://genpm.net/p/@core/money)
- **npm dependencies** (proposed, never installed without asking): `zod`, `stripe`
- **Environment variables** (added to `.env.example`): `SITE_URL`, `STRIPE_TAX`, `CHECKOUT_ALERT_TO`
- **MCP servers** (only added with your consent): `stripe`
- **AI rules**: [`AGENTS.md`](AGENTS.md), loaded by your agent only when it works in `src/lib/checkout`

## Security

GenPM installs a 40-character commit SHA (never a branch), verifies an Ed25519-signed manifest, never runs install scripts and scans AI rules for prompt injection. Details: [genpm.net/docs/security](https://genpm.net/docs/security).

## License

MIT — see [LICENSE](LICENSE). Browse the full catalog at [genpm.net](https://genpm.net).
