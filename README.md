# WOSS: Work-Offer Settlement Standard

**A Minimal Coordination Language for Decentralized Work — Version 0.2 (Conceptual) — January 2026**

WOSS is a language for coordinating work and settling in sats. Three event types. One settlement layer. No gatekeepers.

*Status: Conceptual. This document defines the protocol. Third-party tooling builds on top.*

---

## Philosophy: A Language, Not a Platform

WOSS is NOT a marketplace. It's a **language**.

| WOSS is... | WOSS is NOT... |
|----------------------|---------------------------|
| A grammar for coordination | A marketplace platform |
| A settlement layer (sats) | A reputation system |
| A public bulletin board | A matching engine |
| Rules you MUST follow to participate | Opinions about HOW to participate |

Like TCP/IP doesn't care what you send—cat videos, banking data, games—WOSS doesn't care what the work is, who does it, or how you find it.

It only cares that:
1. **Offers are structured** so anyone can parse them
2. **Fulfillments reference offers** so there's a trail
3. **Sats flow** as the universal acknowledgment

---

## Why Sats?

Sats are the universal acknowledgment because they're **permissionless**.

- No KYC required
- No platform approval
- No banking relationship
- No species requirement

A human can earn sats. A bot can earn sats. An AI agent can earn sats. A Martian with a Lightning node can earn sats.

Lightning settles in milliseconds. Invoices are machine-readable. Anyone in the network can participate. That's the point.

---

## The Three Primitives

The entire protocol is three event types.

### 1. OFFER (kind: 32001)

"I will pay sats for something."

```json
{
  "kind": 32001,
  "content": "<optional human-readable description>",
  "tags": [
    ["d", "<unique-offer-id>"],
    ["sats", "<amount>"],
    ["t", "<freeform-tag>"],
    ["t", "<another-tag>"]
  ],
  "pubkey": "<offerer>",
  "created_at": <timestamp>
}
```

**Mandatory:**
- `kind`: 32001 (identifies this as a WOSS offer)
- `d`: unique identifier (NIP-33 addressable)
- `sats`: how much you'll pay

**Optional:**
- `t`: freeform tags (for third-party filtering)
- `content`: human-readable description
- Any other tags you want

The protocol doesn't validate your tags. It doesn't parse your description. It doesn't care what the work is. That's between you and whoever fulfills it.

### 2. FULFILL (kind: 32002)

"I did the thing. Here's proof. Pay me."

```json
{
  "kind": 32002,
  "content": "<optional description of what was done>",
  "tags": [
    ["d", "<unique-fulfill-id>"],
    ["e", "<offer-event-id>", "", "offer"],
    ["proof", "<uri-or-hash-or-inline>"],
    ["invoice", "<lightning-invoice>"]
  ],
  "pubkey": "<fulfiller>",
  "created_at": <timestamp>
}
```

**Mandatory:**
- `kind`: 32002
- `e`: reference to the offer being fulfilled
- `invoice`: Lightning invoice to pay

**Optional:**
- `proof`: whatever proves the work (format is YOUR business)
- `content`: description of what was done

The protocol doesn't verify the proof. The protocol doesn't validate the work. The offerer decides if it's acceptable. If they pay, the job is done.

### 3. ACK (kind: 32003)

"Paid." Or: "Rejected."

```json
{
  "kind": 32003,
  "content": "",
  "tags": [
    ["d", "<unique-ack-id>"],
    ["e", "<fulfill-event-id>", "", "fulfill"],
    ["status", "paid"],
    ["preimage", "<lightning-preimage>"]
  ],
  "pubkey": "<offerer>",
  "created_at": <timestamp>
}
```

**Mandatory:**
- `kind`: 32003
- `e`: reference to the fulfillment
- `status`: `paid` or `rejected`

**If paid:**
- `preimage`: proof of payment

That's it. The protocol is complete.

---

## What the Protocol Doesn't Do

By design, the protocol has no opinions on:

| Concern | Protocol Position |
|---------|-------------------|
| **Filtering** | Use third-party curators |
| **Matching** | Use third-party matching engines |
| **Reputation** | Third parties aggregate payment history |
| **Disputes** | Third parties offer arbitration |
| **Work types** | Tags are freeform; define whatever you want |
| **Proof formats** | Offerer decides what proof is acceptable |
| **Worker types** | Human, bot, AI—if the work gets done, it gets done |

The protocol is **radically neutral**. It doesn't prefer humans over AI. It doesn't prefer fast over slow. It doesn't prefer cheap over expensive.

It just says: **"Speak this language. Settle in sats."**

---

## The Third-Party Ecosystem

The protocol enables but doesn't define:

```
┌─────────────────────────────────────────────────────────────────┐
│                    THE NOSTR BAZAAR                             │
│                                                                 │
│  All OFFER, FULFILL, ACK events are PUBLIC.                     │
│  Anyone can read. Anyone can participate.                       │
│  The protocol is the language. Sats are the currency.           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Third parties build on top
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
   ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
   │  CURATORS   │     │ REPUTATION  │     │  ARBITERS   │
   │             │     │ AGGREGATORS │     │             │
   │ Filter by   │     │ Track       │     │ Resolve     │
   │ your prefs  │     │ history     │     │ disputes    │
   └─────────────┘     └─────────────┘     └─────────────┘
```

Want to only see offers tagged `matchmaking`? Use a curator.
Want to know if a fulfiller has a good track record? Use a reputation aggregator.
Want protection against non-payment? Use an escrow service.

None of this is WOSS. All of it is enabled BY WOSS.

---

## Prototype Path

### Tier 1: Post an Offer (No Code)

Open any Nostr client that supports custom kinds. Post:

```json
{
  "kind": 32001,
  "content": "Test offer",
  "tags": [
    ["d", "my-first-offer"],
    ["sats", "100"],
    ["t", "test"]
  ]
}
```

You've broadcast a WOSS offer. Anyone can fulfill it.

### Tier 2: Fulfill an Offer

Watch for kind 32001 events. Find one you can fulfill. Do the work. Post:

```json
{
  "kind": 32002,
  "content": "Done",
  "tags": [
    ["d", "my-fulfillment"],
    ["e", "<offer-event-id>", "", "offer"],
    ["proof", "I did the thing"],
    ["invoice", "lnbc100n..."]
  ]
}
```

### Tier 3: Complete the Loop

Offerer sees the fulfillment. Evaluates the proof (their call). Pays the invoice. Posts ACK with preimage.

You've participated in a decentralized labor market.

---

## Related Standards

WOSS is part of the [Decentralized Game Standard](../../.github/profile/README.md) ecosystem:

| Standard | How It Relates |
|----------|----------------|
| [AEMS](../aems/README.md) | Offers can request work on AEMS entities |
| [GERS](../gers/README.md) | Processors can be distributed work, coordinated via WOSS |

---

## Contributing

WOSS is a living standard. Shape it:
- **Discuss**: Question the primitives
- **Build**: Create third-party tooling
- **Extend**: Propose new use cases

The protocol stays minimal. The ecosystem grows.
