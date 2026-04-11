<div align="center">

<h1>⚡ Fast — Payments for Agents</h1>

<p><strong>The first payment system built for the agentic economy.</strong><br/>
Parallel settlement infrastructure for AI agents and humans to move money globally, instantly, and securely.<br/>
Transactions settle in <strong>under 100 milliseconds</strong> with mathematically proven correctness.</p>

<p>
  <a href="https://fast.xyz"><img src="https://img.shields.io/badge/fast.xyz-000000?style=flat-square&logoColor=white" /></a>
  <a href="https://docs.fast.xyz"><img src="https://img.shields.io/badge/Docs-000000?style=flat-square&logoColor=white" /></a>
  <a href="https://discord.gg/fast"><img src="https://img.shields.io/badge/Discord-000000?style=flat-square&logo=discord&logoColor=white" /></a>
  <a href="https://x.com/fastxyz"><img src="https://img.shields.io/badge/@fastxyz-000000?style=flat-square&logo=x&logoColor=white" /></a>
  <a href="https://pi2.network"><img src="https://img.shields.io/badge/Pi2_Labs-000000?style=flat-square&logoColor=white" /></a>
</p>

</div>

---

> Today's payment systems process transactions one at a time through a single queue. Fast eliminates that bottleneck entirely — enabling millions of simultaneous transactions with mathematically proven correctness.

---

## Why Fast Exists

Billions of AI agents are coming online. They'll transact at machine speed — executing micropayments, settling trades, and coordinating economic activity every millisecond of every day.

**The problem:** No payment system exists that is both globally permissionless and infinitely scalable.

- **Traditional rails** (Visa, Stripe, PayPal) are fast but centralized — agents can't open bank accounts or get credit cards
- **Decentralized systems** force all transactions through sequential ordering, creating an inherent throughput ceiling

**Fast solves this.** By requiring only local ordering (per-account sequencing) instead of global ordering, independent transactions process in parallel — simultaneously and without coordination.

---

## What Fast Does

| | Capability | Detail |
|:---:|:---|:---|
| ⚡ | **Agentic Payments** | Agent-to-agent, agent-to-human, and human-to-human transactions on a single infrastructure |
| 🔀 | **Parallel Settlement** | Transactions between independent accounts settle simultaneously — no sequential bottleneck |
| 🏁 | **Instant Finality** | Sub-100ms confirmation. Transactions are final the moment they settle. |
| 📈 | **150,000+ TPS** | Current throughput without optimization — architecturally uncapped |
| 🔬 | **Mathematically Verified** | Built on formally verified foundations by the creator of the K Framework |
| 🌐 | **Permissionless** | No KYC for network access. Any agent, any developer, anywhere. |

---

## The Fast Stack

**⚡ Fast — Parallel Settlement Layer**  
The core infrastructure. Parallel execution of payments with instant finality and mathematically proven correctness. Not a faster blockchain — a fundamentally different architecture.

**🌐 AllSet — Unified Liquidity**  
Capital becomes globally usable. Ownership is portable across apps, networks, and venues — without moving assets. Agents access liquidity wherever it exists.

**🖥️ Fast Terminal — Agent Payment Interface** *(Coming Soon)*  
Where agents execute payments at machine speed. The interface layer for autonomous commerce.

---

## Quick Start

```bash
npm install @fastxyz/sdk
```

## Core Concepts

| Class                | Purpose                                      |
| -------------------- | -------------------------------------------- |
| `Signer`             | Holds an Ed25519 private key, signs messages |
| `FastProvider`       | JSON-RPC client for the Fast proxy API       |
| `TransactionBuilder` | Fluent builder for all transaction types     |

**Typical flow:** Create Signer → Create Provider → Get account info → Build transaction → Sign → Submit.

---

## Workflows

### 1. Create a Signer

```ts
import { Signer } from '@fastxyz/sdk';

// From a 32-byte hex private key (0x prefix optional)
const signer = new Signer('abcdef0123456789...');

// Or from raw bytes or a number array
const signer = new Signer(new Uint8Array(32));

const pubKey = await signer.getPublicKey(); // Uint8Array (32)
const address = await signer.getFastAddress(); // "fast1..."
```

### 2. Connect to the Network

```ts
import { FastProvider } from '@fastxyz/sdk';

const provider = new FastProvider({
  rpcUrl: 'https://api.fast.xyz/proxy',
});
```

There is no default URL — `rpcUrl` is always required.

### 3. Check Account Info

```ts
const account = await provider.getAccountInfo({
  address: pubKey, // Uint8Array, hex string, or bech32m
});

console.log('Native balance:', account.balance);
console.log('Token balances:', account.tokenBalance); // [tokenId, amount][] pairs
console.log('Next nonce:', account.nextNonce);
```

Optional filters (all default to `null` if omitted):

```ts
const account = await provider.getAccountInfo({
  address: pubKey,
  tokenBalancesFilter: [tokenIdBytes],
  stateKeyFilter: [stateKeyBytes],
  certificateByNonce: { start: 0n, limit: 10 },
});
```

### 4. Transfer Tokens

```ts
import { TransactionBuilder } from '@fastxyz/sdk';

const account = await provider.getAccountInfo({ address: pubKey });

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

const result = await provider.submitTransaction(envelope);
```

### 5. Build Other Transaction Types

`TransactionBuilder` supports 10 builder methods via fluent chaining. Single operations produce a direct claim; multiple operations are automatically batched.

```ts
const builder = new TransactionBuilder({ networkId, signer, nonce });

builder.addTokenCreation({ tokenName, decimals, initialAmount, mints, userData });
builder.addTokenManagement({ tokenId, updateId, newAdmin, mints, userData });
builder.addMint({ tokenId, recipient, amount });
builder.addBurn({ tokenId, amount });
builder.addStateInitialization({ key, initialState });
builder.addStateUpdate({ key, previousState, nextState, computeClaimTxHash, computeClaimTxTimestamp });
builder.addStateReset({ key, resetState });
// verifierCommittee = addresses of verifiers, verifierQuorum = minimum signatures needed, claimData = the claim being verified
builder.addExternalClaim({ claim: { verifierCommittee, verifierQuorum, claimData }, signatures });
// Informs the network that the account is leaving the verifier set.
builder.addLeaveCommittee();

// Batch multiple operations
builder.addBurn({ tokenId, amount: 100n }).addBurn({ tokenId, amount: 200n });

const envelope = await builder.sign();
```

**Builder reuse:** Call `builder.reset()` to clear operations, then `builder.setNonce(newNonce)` for the next transaction.

### 6. Sign and Verify Messages

```ts
// Sign raw bytes
const sig = await signer.signMessage(messageBytes);

// Sign BCS-encoded typed data (domain-prefixed)
const sig = await signer.signTypedData(bcsType, data);

// Verify
import { verify, verifyTypedData } from '@fastxyz/sdk';

const valid = await verify(sig, messageBytes, pubKey);
const valid = await verifyTypedData(sig, bcsType, data, pubKey);
```

### 7. Query Certificates and Token Metadata

```ts
// Get finalized transaction certificates
const certs = await provider.getTransactionCertificates({
  address: pubKey,
  fromNonce: 0n,
  limit: 10,
});

// Get token metadata
const tokenInfo = await provider.getTokenInfo({ tokenIds: [tokenIdBytes] });

// Get pending multisig transactions
const pending = await provider.getPendingMultisigTransactions({ address: pubKey });
```

📖 [Read the Docs](https://docs.fast.xyz) · 🔑 [Get API Access](https://fast.xyz) · 💬 [Join the Community](https://discord.gg/fast)

---

## Use Cases

- **Agentic Payments** — AI agents transacting autonomously at machine speed: paying for services, settling trades, splitting costs
- **Agent Marketplaces** — Infrastructure for autonomous commerce at scale where agents offer and purchase services
- **Cross-App Trading** — Agents execute strategies across multiple venues simultaneously with unified liquidity
- **Micropayments** — Millions of sub-cent transactions per second for AI micro-services
- **Stablecoin Settlement** — Real-world assets and stablecoins moving at internet speed
- **Human-to-Agent Payments** — Seamless flows between people and the agents working on their behalf

---

## Architecture

Fast is built on a breakthrough in distributed systems: **you don't need global transaction ordering to prevent double-spending.** You only need local ordering — each account's transactions in sequence, while independent transactions happen in parallel.

This insight, published in peer-reviewed research and built on the Actor Model, eliminates the scalability ceiling that every blockchain inherits from Bitcoin's 2008 design.

- **Embarrassingly parallel** — scales linearly with hardware
- **Formally verified** — mathematical proof of correctness, not probabilistic security
- **No consensus bottleneck** — independent transactions never wait for each other
- **Byzantine fault tolerant** — secure against malicious actors

---

## Founded by Scientists

**Grigore Roșu** — IEEE Fellow, AAAS Fellow. Former NASA researcher. Professor at UIUC. Creator of the K Framework and Matching Logic. Founded Runtime Verification. Built formal verification tools used across the industry.

**Sriram Vishwanath** — IEEE Fellow. Professor at Georgia Tech. Five startups, three successful exits. Expert in information theory and distributed systems.

Fast is developed by **[Pi2 Labs](https://pi2.network)** — the research arm building the mathematical foundations for the agentic economy.

---

## Resources

| Resource | Link |
|:---|:---|
| Website | [fast.xyz](https://fast.xyz) |
| Documentation | [docs.fast.xyz](https://docs.fast.xyz) |
| API Reference | [api.fast.xyz](https://api.fast.xyz) |
| Research Papers | [pi2.network/papers](https://pi2.network/papers) |
| Blog | [blog.fast.xyz](https://blog.fast.xyz) |
| X | [@fastxyz](https://x.com/fastxyz) |
| Discord | [discord.gg/fast](https://discord.gg/fast) |

---

<div align="center">

`agentic payments` · `AI agent payments` · `parallel settlement` · `instant finality` · `payment infrastructure` · `machine-speed payments` · `agentic economy` · `micropayments` · `permissionless payments` · `stablecoin settlement` · `autonomous agents` · `agent marketplace`

</div>
