# TipJar — accept TRX & USDT-TRC20 donations with one `<script>` tag

**No backend. No database. No server. No npm build. No account.**
Paste one tag, get a donation button with QR code, wallet deep-links and
optional on-chain receipt confirmation.

```html
<script src="https://cdn.jsdelivr.net/gh/boo651218-pixel/tipjar@main/src/tipjar.js"
        data-address="TYourAddressHere"
        data-token="USDT"
        data-amounts="5,10,25,100"
        defer></script>
```

Live demo: https://boo651218-pixel.github.io/tipjar/

---

## Why this exists

Accepting USDT-TRC20 on a static site (GitHub Pages, Notion, Carrd, a blog,
a Linktree, a Telegram bot's landing page) currently means either:

1. pasting a 34-character address as plain text (nobody scans it, nobody pays), or
2. signing up for a hosted payment processor that takes 2–5 %, needs KYC, and
   can freeze the payout.

TipJar is neither. It is 100 % client-side. The money goes **directly to your
address** — TipJar never touches a cent, never sees a key, never proxies a
transaction. There is nothing to breach.

## Features

| | |
|---|---|
| Zero backend | static JS + CSS injected at runtime, works on any host incl. `file://` |
| TRX + any TRC20 token | native TRX, USDT out of the box, `data-contract` for anything else |
| QR + deep links | one QR that TronLink / Trust / Ledger Live / exchanges all read |
| Preset & custom amounts | `data-amounts="5,10,25"` + free-text field, decimal-safe |
| Copy-to-clipboard | address + amount, with visual confirmation |
| On-chain receipts (optional) | point it at your **own** free TronGrid key and it watches for incoming transfers, fires `tipjar:confirmed` |
| Themable | light/dark, accent colour, 3 layouts (floating pill, inline card, modal-only) |
| Accessible | focus trap, `Esc` to close, ARIA labels, keyboard-only operable |
| No tracking | no analytics, no external requests unless you turn receipts on |
| ~9 KB gzipped | no dependencies except the bundled QR generator |

## Quickstart

Drop the tag anywhere before `</body>`. A floating "☕ Tip" pill appears
bottom-right; clicking it opens the tip modal.

Inline instead of floating:

```html
<div id="tip"></div>
<script src="src/tipjar.js" data-address="TYourAddressHere" data-target="#tip" defer></script>
```

Programmatic:

```js
window.TipJar = { address: 'TYourAddressHere', token: 'USDT', amounts: [10, 50] };
// then, after the script loads:
TipJar.open();            // show the modal
TipJar.open({ amount: 25 });
TipJar.setToken('TRX');
TipJar.close();
```

## Configuration

All attributes are `data-*` on the `<script>` tag (or camelCase keys on
`window.TipJar`, script-tag config wins).

| Attribute | Default | Meaning |
|---|---|---|
| `data-address` | *(required)* | TRON base58 recipient address |
| `data-token` | `TRX` | `TRX`, `USDT`, or a contract address |
| `data-contract` | – | explicit TRC20 contract address (overrides `data-token`) |
| `data-decimals` | `6` | token decimals, used with `data-contract` |
| `data-symbol` | `TRX` | display symbol, used with `data-contract` |
| `data-amounts` | `1,5,10,50` | comma-separated presets |
| `data-title` | `Leave a tip` | modal heading |
| `data-message` | – | one line of supporting copy |
| `data-target` | – | CSS selector for inline mount (else floating pill) |
| `data-layout` | `pill` | `pill` \| `card` \| `button` |
| `data-theme` | `auto` | `auto` \| `light` \| `dark` |
| `data-accent` | `#EF0057` | any CSS colour |
| `data-label` | `☕ Tip` | pill/button text |
| `data-min` | `0.1` | minimum accepted amount (warning only) |
| `data-trongrid` | – | full node base URL, defaults to `https://api.trongrid.io` |
| `data-api-key` | – | **your** free TronGrid key — enables receipt watching |
| `data-watch` | `off` | `on` starts polling for incoming transfers |

### On-chain receipts

If you supply `data-api-key` (free from
[TronGrid](https://www.trongrid.io/)) and `data-watch="on"`, TipJar polls the
public node for transfers *into* your address every 20 s and emits an event:

```js
document.addEventListener('tipjar:confirmed', e => {
  // e.detail = { txid, amount, token, from }
  unlockStuff(e.detail);
});
```

Nothing is stored, no webhook, no server. The poll only reads public chain data
that anyone can query. **TipJar cannot and does not move funds — it has no keys.**

## Security notes (read this)

- The address is rendered as text and encoded into a QR you can verify by eye.
  Always check the first/last 4 characters before trusting a QR.
- Validate `data-address` server-side never applies (there is no server) — but
  the widget rejects anything that is not `T` + 33 base58 chars, so typos surface
  immediately instead of silently sending funds into the void.
- TipJar is MIT licensed and 250 lines. Read it. It is meant to be read.

## Recipes

**Blog (Jekyll / Hugo / Astro / WordPress)** — paste into the footer include.
**Notion / Carrd** — embed an HTML block with the tag.
**Telegram / Discord bio** — deploy the demo page, share the link.
**Podcast shownotes** — one line, per-episode amount presets.

## License

MIT. Use it commercially, fork it, rebrand it.

TipJar is free and will stay free. If it saves you an afternoon, a tip to
`TYourAddressHere` (the one you configure) is a nice way to say so — the
maintainer's address is in the demo page's `data-address`.

---

## Need something built on this?

I take small paid jobs in this exact niche — TRON/TronJS data pulls, token
transfer CSV exports, balance-history reports, custom checkout flows, sybil /
airdrop-distribution filters, explorer scripts. Fixed price, delivered in days,
paid in USDT-TRC20.

Open an [issue](https://github.com/boo651218-pixel/tipjar/issues) with what you
need and I'll tell you honestly whether it's worth paying for.
