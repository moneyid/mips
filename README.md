# Money Improvement Proposals (MIPs)

## The Problem

Paying with stablecoins today is broken. You get a long hex address, you have to figure out which network it's on, you copy-paste and pray you didn't miss a character, you manually type the amount, you pick the right token from a list of 50, and if you get any of it wrong -- your money is gone. QR codes only encode an address, nothing else. There's no way to know what tokens the receiver actually accepts or on which chains.

Compare that to scanning a QR code to pay with Lightning: one scan, amount included, done.

MoneyID brings that experience to stablecoins and crypto.

## What MoneyID Does

A single MoneyID -- `bob@receiver.com` or a QR code -- tells the wallet everything: what currencies the receiver accepts, on which chains, for how much, with a description and a fresh payment address. The wallet knows exactly what to do. One scan, one tap, done.

```
Merchant terminal shows QR:  moneyid:shop+order-42@merchant.co

Customer scans it. Wallet:
  1. Resolves the address → gets USDT on Tron, USDC on Base, USDC on Solana
  2. Shows: "Pay $25.00 to CoffeeShop for Order #42"
  3. Amount is fixed -- no manual entry needed
  4. Customer picks USDC on Base, taps Pay
  5. Wallet sends to a fresh address with the exact amount
  6. Terminal gets instant confirmation, prints receipt
```

No copy-pasting addresses. No manual amount entry. No guessing which network. No wrong token.

Works for terminals and point-of-sale (fixed amount -- scan and pay), online invoices (fixed amount with a tag), and donations or tips (open amount -- user picks how much).

## How It Works

```
1. User enters bob@receiver.com (or scans moneyid:bob@receiver.com)
2. Wallet GETs https://receiver.com/.well-known/moneyid/bob
3. Service returns supported currencies, chains, amount, metadata
4. User picks 10 USDT on Ethereum
5. Wallet POSTs {amount, currency, network, chainId, tokenAddress?} to callback
6. Service returns payment instruction {address, token, amount, expiry}
7. Wallet executes on-chain transfer
```

## Specifications

| MIP | Title | Description | Status |
|-----|-------|-------------|--------|
| [01](01.md) | Protocol Foundation | Address format, URI scheme, URL resolution, transport rules, versioning | Draft |
| [02](02.md) | Pay | Complete send-money flow: discovery, currencies, chains, payment instructions, metadata, comments, success actions, conversion | Draft |

Each MIP is a complete, self-contained specification. You read one document to implement one flow.

## Why MoneyID

LNURL pioneered URL-based payment discovery for Lightning. MoneyID takes the best of that design and rebuilds it for multi-chain crypto and stablecoins.

| | LNURL | UMA | Raw Addresses / ENS | **MoneyID** |
|---|---|---|---|---|
| Docs to read for a payment | 9+ | 6 | N/A | **1** |
| Multi-chain | No (Lightning only) | Partial (Lightning + Spark) | Single chain per address | **Yes (EVM, Solana, Tron, any)** |
| Stablecoin native | No (millisats only) | Partial (conversion over LN) | Yes but no discovery | **Yes (token-native amounts, multi-currency discovery)** |
| Amount in QR | Yes | Yes | No (manual entry) | **Yes** |
| Human-readable addresses | Yes (`user@domain`) | Yes (`$user@domain`) | Partial (ENS, .sol) | **Yes (`user@domain`)** |
| Dynamic payment addresses | Yes | Yes | No (static) | **Yes (fresh per request)** |
| Rich metadata | Fragmented across docs | Yes | No | **Yes (built into pay flow)** |
| Compliance overhead | None | Heavy (travel rule, KYC, VASP sigs) | None | **None** |

## Design Principles

**Scan and pay.** A QR code or a `user@domain` address carries everything the wallet needs. Amount, currency, chain, metadata -- all resolved automatically. No manual entry, no guessing. Works at POS terminals (fixed amount, instant confirmation) and online (invoices, donations, tips).

**Complete specs, not fragments.** LNURL was written incrementally -- payRequest is LUD-06, success actions are LUD-09, comments are LUD-12, payer identity is LUD-18. MoneyID includes all of these in a single MIP-02 document. Future MIPs add new capabilities, not patch missing pieces.

**Multi-chain from day one.** Currency and chain discovery is built into every flow. A single MoneyID can accept USDT on Ethereum, USDC on Base, and PYUSD on Solana.

**Token-native amounts.** Amounts are strings in the token's smallest unit (e.g., `"1000000"` = 1 USDT with 6 decimals). No conversion to millisats, no floating-point issues.

**Plain HTTPS.** No bech32 encoding. URLs are URLs. JSON is JSON. The `moneyid:` URI scheme lets wallets identify MoneyID links unambiguously.

## How to Propose a New MIP

1. Open a pull request with a new `XX.md` file using the next available number.
2. Follow the structure of existing MIPs: title, author, status, clear step-by-step flow with request/response examples.
3. A MIP should describe a complete flow, not a patch to an existing one.
4. Discussion happens on the PR. To be accepted, a MIP must be implemented or actively being implemented by 2 or more wallets/services.

## Community

- [GitHub Issues](https://github.com/moneyid/mips/issues) -- Bug reports, questions, feature requests
- [GitHub Discussions](https://github.com/moneyid/mips/discussions) -- General protocol discussion

## License

[MIT](LICENSE)
