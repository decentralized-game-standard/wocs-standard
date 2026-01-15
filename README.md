# WOSS: Work-Offer Settlement Standard

**A Protocol for Permissionless, Instant-Settled Coordination — Conceptual, 2026-01-14**

In the physical world, play organizes itself through voluntary exchange. Friends meet at a park for pickup basketball: one person brings the ball, another stakes $20 for the winners, someone referees for a few bucks. No league approval required. A neighborhood poker game sets its own buy-in and payouts. Local chess clubs run tournaments with entry fees funding prizes. These activities emerge spontaneously—participants signal needs, offer value, settle instantly in cash—without a central authority extracting rent or capable of shutting it down.

Imagine if Nike claimed ownership of all basketballs and banned unauthorized games. Or if a single poker company could forbid home games unless they took a 30% rake. Or if FIDE sent cease-and-desists to casual park chess tournaments. The absurdity highlights how digital games have been architected backward: publishers control the "ball," the "table," the rules, and the payouts. Value flows upward; permission is required for everything.

WOSS restores the natural order. It is a minimal, three-event protocol on Nostr for broadcasting offers of work, claiming completion, and settling in sats via Lightning. No platform. No gatekeepers. No mandatory reputation system. Just structured signals that enable free-market coordination at microscopic cost.

## The Centralized Coordination Failure in Gaming

Central control creates fragility and misaligned incentives.

- **Modder Exploitation** — Communities extend games for decades, generating immense value with near-zero compensation. Bethesda's repeated attempts at paid mods—Skyrim's 2015 Steam workshop (75% platform cuts, rampant theft concerns, withdrawn after backlash), Fallout 4's Creation Club (curated but widely criticized as overpriced micro-DLC), and 2023–2024 "Creations" rebrand—failed because high rents and top-down curation stifled organic creation.

- **Esports Fragility** — Publishers can kill competitive scenes overnight. Blizzard's 2018 abrupt cancellation of Heroes of the Storm's Global Championship and esports infrastructure—pulling developers, ending tournaments—left players and organizers stranded despite an active community.

- **Server Hosting Burden** — Community-run servers preserve games (Minecraft's vast private server ecosystem, WoW private realms) but rely on donations or out-of-pocket costs. Hosting fees range from $10–200/month depending on scale; without direct, granular funding mechanisms, many shut down.

- **Asset Creation Gatekeeping** — Artists, modelers, and designers depend on platform marketplaces or publisher approval. Commissions happen through Discord or forums with trust issues and payment friction.

- **Anti-Cheat Monopolies** — Services like Easy Anti-Cheat (owned by Epic) are centralized chokepoints. Outages cascade across titles; integration requires compliance with one company's terms; alternatives are scarce.

These are symptoms of artificial scarcity imposed on abundant digital goods.

## The Free-Market Alternative: Deflationary Coordination

Libertarian thinkers have long described how voluntary exchange coordinates dispersed knowledge better than central planning. Hayek's "Use of Knowledge in Society" (1945) argues that prices aggregate local, tacit information no planner could collect—creating spontaneous order.

Jeff Booth extends this to technology's deflationary force: innovation drives costs toward zero, enabling abundance when money is sound. Inflationary fiat distorts this by forcing growth-at-all-costs; Bitcoin's fixed supply and Lightning's near-zero-fee micropayments align incentives—value flows directly to creators, coordination becomes frictionless.

WOSS operationalizes this for gaming. Lightning makes offering 100 sats for a bug fix or 100,000 sats for a tournament prize as easy as cash in a park game. Nostr broadcasts signals globally without permission.

## Technical Specification: Three Primitives

WOSS uses standard Nostr kinds for radical minimalism.

### 1. OFFER (kind 32001)
Broadcast: "I will pay X sats for Y."

```json
{
  "kind": 32001,
  "tags": [
    ["d", "<unique-id>"],
    ["sats", "50000"],
    ["t", "server-hosting"],
    ["t", "minecraft-rpg-realm"],
    ["duration", "month"]
  ],
  "content": "50k sats/month to host a 50-slot Minecraft server with these mods..."
}
```

Mandatory: `d` (addressable), `sats`. Optional freeform tags for filtering.

### 2. FULFILL (kind 32002)
Claim: "I did Y; pay me."

```json
{
  "kind": 32002,
  "tags": [
    ["d", "<fulfill-id>"],
    ["e", "<offer-event-id>", "", "offer"],
    ["proof", "<server-ip-or-dashboard-link>"],
    ["invoice", "lnbc50000..."]
  ],
  "content": "Server live at example.com:25565, uptime guaranteed..."
}
```

Mandatory: reference to offer, Lightning invoice.

### 3. ACK (kind 32003)
Settle: "Paid" or "Rejected."

```json
{
  "kind": 32003,
  "tags": [
    ["d", "<ack-id>"],
    ["e", "<fulfill-event-id>", "", "fulfill"],
    ["status", "paid"],
    ["preimage", "<payment-proof>"]
  ]
}
```

Public ACKs create transparent history.

The protocol enforces nothing beyond structure—evaluation, reputation, disputes are market opportunities for third parties.

## Emergent Solutions

- **Server Hosting** — Communities post recurring offers; hosts compete on reliability. Costs drop as Lightning enables micro-sponsorships.

- **Anti-Cheat Services** — Developers offer "integrate my open anti-cheat Processor" via GERS; games pay per deployment or subscription.

- **Asset Creation** — Curators offer bounties for new AEMS Entities or Manifestations. Artists fulfill with proofs.

- **Modding Renaissance** — Direct payment for features, fixes, or cosmetics—no 30–75% cuts.

- **Permissionless Esports** — Organizers offer prize pools; participants fulfill by placing. Streams zap casters in real time.

- **Discovery** — Tags and third-party indexes replace algorithmic gatekeepers.

All integrated: AEMS assets traded via ownership transfers, GERS engines using funded processors, everything settled instantly.

## Why This Works Now

Lightning's deflationary economics—fees trending toward zero—makes coordination abundant. Nostr zaps already demonstrate micro-value flow; WOSS adds structure for commitments.

Real-world play thrives without corporations. Digital play can too.

Post an offer. Fulfill one. Watch markets emerge.

**MIT License** — Open for extension, implementation, experimentation.