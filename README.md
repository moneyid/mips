# Money Improvement Proposals (MIPs)

MoneyID is a protocol for discoverable crypto payments. A single identifier -- `bob@receiver.com` or a QR code -- resolves to the receiver's supported currencies, chains, amounts, and a fresh payment address.

## The Problem

Stablecoin payments have no discovery layer. A wallet address carries no information about which tokens or chains the receiver accepts. Senders copy-paste hex strings, pick a network from a dropdown, type the amount by hand, and have no way to verify any of it before sending. QR codes encode an address and nothing else.

LNURL pioneered URL-based payment discovery for Lightning. MoneyID applies the same idea to multi-chain crypto and stablecoins.

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

At a point-of-sale terminal:

```
Merchant terminal shows QR:  moneyid:shop+order-42@merchant.co

Customer scans → wallet resolves → USDT on Tron, USDC on Base, USDC on Solana
Wallet shows: "Pay $25.00 to CoffeeShop for Order #42"
Amount is fixed. Customer picks USDC on Base, confirms.
Wallet sends to a fresh address. Terminal confirms, receipt prints.
```

Also works for invoices (fixed amount with a tag) and open-amount flows like tips or donations.

## Specifications

| MIP | Title | Description | Status |
|-----|-------|-------------|--------|
| [01](01.md) | Protocol Foundation | Address format, URI scheme, URL resolution, transport rules, versioning | Draft |
| [02](02.md) | Pay | Complete send-money flow: discovery, currencies, chains, payment instructions, metadata, comments, success actions, conversion | Draft |

Each MIP is self-contained. One document covers one flow.

## Why MoneyID

| | Raw Addresses | ENS / .sol | **MoneyID** |
|---|---|---|---|
| Multi-chain | One chain per address | One chain per name | **Any chain** |
| Currency discovery | No | No | **Yes** |
| Amount in QR | No | No | **Yes** |
| Human-readable | No | Yes | **Yes (`user@domain`)** |
| Fresh address per payment | No | No | **Yes** |
| Metadata | No | No | **Yes** |

## Design

- The full payment flow -- discovery, amounts, metadata, comments, payer identity, success actions -- lives in one document (MIP-02). Future MIPs add new flows, not patches.
- Currency and chain discovery is part of every request. One MoneyID can advertise USDT on Ethereum, USDC on Base, and PYUSD on Solana.
- Amounts are strings in the token's smallest unit (`"1000000"` = 1 USDT). No floating-point.
- Transport is HTTPS. Payloads are JSON. The `moneyid:` URI scheme identifies MoneyID links.

## Contributing

1. Open a pull request with a new `XX.md` file using the next available number.
2. Follow the structure of existing MIPs: title, author, status, step-by-step flow with request/response examples.
3. A MIP describes a complete flow, not a patch to an existing one.
4. Discussion happens on the PR. Acceptance requires implementation by 2+ wallets or services.

## Community

- [GitHub Issues](https://github.com/moneyid/mips/issues) -- Bug reports, questions, feature requests
- [GitHub Discussions](https://github.com/moneyid/mips/discussions) -- Protocol discussion

## License

[MIT](LICENSE)
