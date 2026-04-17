# ⚡ Fast - Payments for AI Agents

**Give your AI agent a wallet. Three commands.**

Your agent creates an account, funds it with a credit card or crypto, and pays for anything - other agents, APIs, real-world goods. No bank account, no KYC for small amounts, no integration complexity.

[fast.xyz](https://fast.xyz) · [docs.fast.xyz](https://docs.fast.xyz) · [@fastxyz](https://x.com/fastxyz) · [Discord](https://discord.gg/fast)

---

## Quick Start

```bash
npm install @fastxyz/sdk
```

```typescript
import { FastProvider, Signer, TransactionBuilder } from '@fastxyz/sdk';

const provider = new FastProvider({
  rpcUrl: 'https://api.fast.xyz/proxy',
});

// Step 1: Initialize Identity
const signer = new Signer('abcdef0123456789...');
const pubKey = await signer.getPublicKey();
const account = await provider.getAccountInfo({ address: pubKey });

// Step 2: Atomic Build & Sign
const envelope = await new TransactionBuilder({
  networkId: 'fast:mainnet',
  signer,
  nonce: account.nextNonce,
})
  .addTokenTransfer({
    tokenId: '11'.repeat(32),
    recipient: 'fast1recipient...',
    amount: 1000n,
    userData: null,
  })
  .sign();

// Step 3: Broadcast & Submit
const result = await provider.submitTransaction(envelope);
```

That's it. Your agent can now transact.

---

## What Your Agent Can Do

**Buy things on Amazon** - [shop.fast.xyz](https://shop.fast.xyz). Agent receives USDC or credits, buys physical goods, delivered to any address.
**Pay other agents** - Direct agent-to-agent transfers. Sub-second settlement. Works for micropayments (sub-cent) and larger amounts.
**Receive payments from humans** - Humans top up an agent's wallet with their credit card. No crypto knowledge required.

---

## How It's Different

**vs. Stripe/PayPal:** Agents can't open bank accounts or pass KYC. Fast doesn't require either.
**vs. Stablecoins:** Access most major stablecoin blockchains with no throughput ceiling.

---

## Integrations

- **MCP server** - Works with Claude, ChatGPT, any LLM with tool use
- **TypeScript SDK** - `npm install @fastxyz/sdk`
- **Python SDK** - Coming soon
- **CLI** - `npm install -g @fastxyz/cli`
- [**SKILL.md**](https://github.com/fastxyz/fast-sdk/blob/main/skills/fast/SKILL.md) - Drop-in agent payment skill for any agent framework

---

## Resources

- Website: [fast.xyz](https://fast.xyz)
- Docs: [docs.fast.xyz](https://docs.fast.xyz)
- SDK: [@fastxyz/sdk](https://www.npmjs.com/package/@fastxyz/sdk) on npm
