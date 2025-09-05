# Chapter I: Bitcoin Fundamentals

## Section I: Bitcoin Core Concepts

### Genesis and Philosophy

Bitcoin's creation was a direct response to the 2008 global financial crisis. On January 3rd, 2009, its anonymous creator, Satoshi Nakamoto, embedded a newspaper headline into the very first block, known as the genesis block. The headline—*The Times*, "Chancellor on brink of second bailout for banks"—serves as a permanent critique of a traditional financial system dependent on centralized control.

This act highlights Bitcoin's mission: to be an alternative to the traditional banking system. Its philosophy is rooted in the cypherpunk belief in using strong cryptography to achieve individual sovereignty over one's finances. To accomplish this, Bitcoin operates as a peer-to-peer electronic cash system without trusted third parties. Its monetary policy is predictable and enforced by code, featuring a fixed, immutable supply cap of 21 million BTC. This creates digital scarcity, standing in stark contrast to fiat currencies that central banks can print at will.

But creating a decentralized alternative to traditional banking raises a fundamental challenge: how do you get thousands of computers around the world to agree on who owns what, without a central authority to settle disputes?

### Consensus and Chain Selection 

Bitcoin solves this through a robust consensus mechanism. Bitcoin uses **Nakamoto Consensus**, which is often simplified as the "longest chain rule" but is more accurately described as the "heaviest chain rule." Nodes choose the valid chain with the greatest accumulated proof-of-work, computed from each block’s target (nBits). Think of two hikers taking different routes: one logs 1,000 easy steps, the other 600 hard steps. We don’t score by steps (block count) but by calories—a stand-in for work—so the steeper, harder route can “weigh” more even with fewer steps. Likewise, nodes sum each block’s work and follow the chain with the most total work. You can’t win by making a long, low-effort chain because difficulty rules are enforced; to rewrite history you must produce at least as much cumulative work as the honest chain (and more, if you’re behind).

### Mining and Proof-of-Work

What if you needed to prove you'd done a lot of work, but couldn't trust anyone to verify it? Bitcoin solves this through **Proof-of-Work**—a system where miners compete to solve cryptographic puzzles that require enormous computational effort but can be quickly verified by anyone.

Here's how it works: Miners bundle transactions into a block and repeatedly hash the block header using the double SHA-256 algorithm. They're searching for a hash value below a specific target—like rolling dice until you get a number lower than a certain threshold, except they're "rolling" trillions of times per second.

**Hash rate** is the speed of this guessing process: how many hashes a miner—or the entire network—can try each second while searching for a valid block. More hash rate means more chances per second to find a block. It's measured in hashes per second (H/s), commonly shown as TH/s (terahashes), PH/s (petahashes), or EH/s (exahashes). Importantly, higher total network hash rate doesn't make blocks come faster on average—difficulty adjusts to maintain the ~10 minute target.

To generate different guesses, miners vary several fields: the **nonce** (a 32-bit number offering about 4 billion attempts), the timestamp, and sometimes the version field. When the nonce space is exhausted, miners modify arbitrary data in the coinbase transaction—often called an **extra nonce**—which changes the block's Merkle root and effectively gives them a fresh header space to search.

To keep the average block time at approximately 10 minutes, the network performs a **difficulty retarget** every 2,016 blocks (about two weeks). The algorithm measures the actual time taken for those 2,016 blocks and adjusts difficulty accordingly, though it clamps extreme changes between ¼× and 4× to prevent wild swings.

Hash rate comes from **ASIC hardware**—specialized computer chips designed solely for Bitcoin mining that are thousands of times more efficient than regular computers. These ASICs are typically organized into mining pools, where thousands of miners combine their computing power and share rewards proportionally. Pools coordinate this work using the **Stratum protocol**, which gives each miner a unique coinbase and job template so everyone searches distinct header space while avoiding duplicate work.

Miners use pools because finding a block is like a huge lottery. With one home ASIC, your chance on any given day is tiny—you could wait years and still never hit one. Pools let miners combine their hash rate so blocks are found regularly, and rewards are split by each miner's share of work. That means steadier, more predictable payouts instead of long dry spells.

Occasionally, two miners find valid blocks at nearly the same time, creating temporary forks in the blockchain. These **stale blocks** and brief **chain reorganizations** are normal—the network automatically settles on the chain with more accumulated work. This is why merchants typically wait for multiple **confirmations** (additional blocks built on top) before considering large payments final.

### Monetary Policy

Bitcoin has a predictable, algorithmic monetary policy with a fixed issuance schedule. The **block reward**, or subsidy, is cut in half every 210,000 blocks, an event known as the **"halving"** that occurs roughly every four years. The subsidy began at 50 BTC and has since been reduced to 25, 12.5, 6.25, and most recently to 3.125 BTC after the 2024 halving.

This mechanism makes Bitcoin a **disinflationary asset**, as its inflation rate trends toward zero. Around the year 2140, the subsidy will cease, and miners will be compensated solely by transaction fees. Due to integer rounding in halvings, the terminal supply converges to ~20,999,999.9769 BTC.

This predictable scarcity is a cornerstone of Bitcoin's value proposition as a store of value, though scarcity alone doesn't guarantee price appreciation—that requires sustained demand to accompany the diminishing supply. Over time, miner security budgets shift from subsidy to fees, making a healthy fee market important for long-term incentives.

## Section II: Bitcoin Technical Architecture

### UTXO Model

How do you track ownership in a system without accounts? Bitcoin takes a different approach from traditional banking by using an **Unspent Transaction Output (UTXO) model**.

Think of it like physical cash in your wallet. Instead of having a single account balance, you have individual bills of different denominations—a $20, two $5s, and some $1s. When you buy something for $7, you might use a $5 and two $1s, getting back change if needed.

Bitcoin works similarly. Instead of a single balance, your wallet holds a collection of UTXOs—individual digital "coins" of varying amounts. When you send bitcoin, your wallet performs **coin selection** (choosing which UTXOs to spend, with privacy and fee trade-offs), consumes them entirely, and creates new UTXOs as outputs: one for the recipient and another as "change" back to you. This elegant design prevents double spending: once a UTXO is spent in a confirmed transaction, it's permanently removed from the UTXO set and can never be spent again.

Each full node maintains its own view of the global **UTXO set**—the complete collection of all spendable outputs—derived from the validated blockchain.

**Bitcoin Script** is a simple programming language that defines spending conditions. Each output carries a **locking script** (scriptPubKey), and inputs provide **unlocking data** (scriptSig and/or witness for SegWit) that must satisfy that script. Addresses are just encodings of common script templates like P2PKH, P2SH, P2WPKH, and P2TR (Taproot).

**Timelocks** make transactions invalid until a specified time or block height—either **absolute** timelocks (nLockTime or OP_CHECKLOCKTIMEVERIFY) or **relative** timelocks (nSequence with OP_CHECKSEQUENCEVERIFY). These enable more complex contracts like Lightning channels, vaults, and escrow arrangements.

### Transaction Structure and Prioritization

Once you understand how UTXOs work, the next question is: how do transactions actually get processed? A Bitcoin transaction consists of **inputs** (the UTXOs being spent) and **outputs** (the new UTXOs being created). The transaction **fee** equals the sum of inputs minus the sum of outputs. Once broadcast, transactions enter each node's **mempool**—a pool of unconfirmed transactions.

Here's where economics comes into play. Since blocks are limited to 4,000,000 weight units (~1,000,000 vB), miners must choose which transactions to include from their own mempools. They naturally prioritize transactions that pay the highest **fee rate**, measured in satoshis per virtual byte (sats/vB), where virtual bytes are derived from transaction weight. A satoshi is the smallest unit of bitcoin—there are 100 million satoshis in one bitcoin.

This creates a **fee market** where users essentially bid for block space. Need your transaction confirmed quickly during network congestion? Pay a higher fee rate. Can wait? Pay less and wait for a quieter period. If your transaction gets stuck, you can use **Replace-by-Fee (RBF)** to broadcast a higher-fee replacement, or **Child-Pays-for-Parent (CPFP)** to create a high-fee child transaction that incentivizes miners to include the parent.

---

## Section III: Bitcoin Upgrades and Scaling

### Understanding Fork Types

How do you upgrade a decentralized network where no one's in charge? Bitcoin has two main upgrade mechanisms that allow the protocol to evolve while maintaining consensus.

#### Hard Forks

**Hard forks** are incompatible upgrades that loosen or change consensus rules. Think of it like changing the width of train tracks.If your train (node) doesn’t switch to the new wheel size, it simply can’t run on the new tracks.Everyone has to upgrade or they’ll keep running on the old line, which becomes a different railway. Bitcoin avoids this because coordinating a whole railway swap is risky and can split passengers and schedules for good. Hard forks are extremely rare in Bitcoin due to coordination challenges and the risk of permanent network splits. 

A notable example is Bitcoin Cash (BCH), created in 2017 by changing rules (notably much larger blocks). In practice, that approach fractured liquidity and community mindshare; over time BCH has retained only a small fraction of Bitcoin’s adoption, hashpower, and market value. Critically, though, deciding what’s the “real Bitcoin” isn’t something the code can decree—there’s no central authority. It’s a messy blend of social consensus (what users, exchanges, wallets, and merchants run), economic gravity (where liquidity settles), and security assumptions (what most full nodes enforce). Markets have decidedly treated BTC as the Schelling point, but that outcome is ultimately social, not ordained.

#### Soft Forks

**Soft forks** are backward-compatible protocol upgrades that tighten consensus rules without breaking the network. Think of it like a club tightening its dress code from “no beachwear” to “collared shirts only.”  People who didn’t hear about the new rule can still walk in and think everything’s fine, because the club still looks and works the same to them. The upgraded bouncers enforce the stricter rule; the non-upgraded ones don’t, but they still recognize everyone as legitimate club members. Non-upgraded Bitcoin nodes still see new blocks as valid but don't enforce the stricter rules themselves, allowing the network to upgrade without splitting into incompatible versions. They require majority support to avoid chain splits, with examples including SegWit, Taproot, and the disabling of OP_CAT.

#### Activation Mechanisms

**Miner Activated Soft Forks (MASF)** rely on hash power signaling—miners indicate readiness by including version bits in block headers. Once a threshold (typically 95%) is reached, the soft fork activates. This was used for upgrades like SegWit (eventually) and most historical soft forks.

**User Activated Soft Forks (UASF)** represent an alternative where economic nodes coordinate a "flag day" to start enforcing tighter rules—potentially regardless of miner signaling. If enough economic nodes and service providers participate, miners face a simple incentive: follow the new rules to get paid, or mine a chain most users won't accept.

**Speedy Trial** is a short BIP9-style miner-signaling trial with a **90%** threshold over 2,016-block windows. If it locks in, activation occurs later at a preset block height; if it times out, no activation occurs and other mechanisms can be considered. This method was successfully used for Taproot activation in 2021.

#### The Challenge of Change

Despite backward compatibility, getting any soft fork into Bitcoin is intentionally difficult. Many developers prioritize **protocol ossification**—the idea that Bitcoin should become increasingly resistant to change as it matures. This conservative approach means proposals undergo years of review, testing, and community debate.

### Bitcoin's Major Upgrades

#### Early Soft Forks (2010-2012)

Bitcoin's earliest soft forks focused on security improvements. The **OP_CAT removal in 2010** disabled the OP_CAT opcode to prevent potential denial-of-service attacks. Various other opcode restrictions were implemented during this period to tighten script validation and improve overall security.

#### Segregated Witness - SegWit (2017)

The **SegWit activation saga** represents one of the most important case studies in Bitcoin's governance, demonstrating how protocol upgrades work—and sometimes don't work—in a truly decentralized system.

SegWit was a landmark upgrade that solved multiple critical issues. Before SegWit, Bitcoin had a critical bug: third parties could alter a transaction's signature and change its ID (TXID) before confirmation, without affecting the transaction's validity. This **transaction malleability** made it risky to build dependent transactions or second-layer protocols like Lightning.

SegWit moved signature data to a separate "witness" structure, making transaction IDs immutable once created. It also introduced **block weight**—a new measurement system with a 4,000,000 weight unit maximum instead of a simple 1MB limit. This effectively increased block capacity while incentivizing adoption of more efficient SegWit addresses. The weight system counts witness data as one-quarter for weight calculation (commonly described as a "75% discount"), creating a backwards-compatible blocksize increase.

To understand the political dynamics, it's helpful to think of pre-SegWit Bitcoin as **"Bitcoin 1.0"**—a system with a hard 1MB blocksize limit and transaction malleability issues. **SegWit represented "Bitcoin 1.1"**—mostly backwards compatible with Bitcoin 1.0, but fixing protocol bugs and enabling second-layer networks while providing a one-time capacity increase.

The original activation mechanism used BIP 9 with a 95% threshold: during any 2,016-block difficulty adjustment period within the window from November 15, 2016 to November 15, 2017 (UTC), if 95% or more of mined blocks signaled SegWit readiness, the upgrade would lock in. After a grace period, SegWit would activate and the network would accept the new transaction types.

However, some large miners withheld signaling, treating "SegWit Readiness" signaling as a "SegWit Willingness" indicator instead. Despite years of development work by Core developers and third-party services, and support from many economic nodes, these miners were blocking activation by refusing to signal—not because of technical concerns, but as political leverage in the broader "blocksize wars."

This created an unprecedented situation: many participants in the Bitcoin ecosystem supported an upgrade they believed would benefit the network, but a group of miners could indefinitely block progress through coordinated non-signaling.

**BIP 148** represented a proposed solution to this governance deadlock. BIP 148 **changed consensus rules for participating nodes by rejecting any non-signaling (bit-1) blocks after August 1st, 2017**. While it leveraged the existing BIP 141 deployment, this "reject non-signaling blocks" rule was new and could have caused a chain split if not widely adopted.

The mechanism was straightforward: **BIP 148 nodes would reject any block that failed to signal SegWit support after the flag day**. This created economic pressure by making non-signaling blocks invalid for BIP 148 nodes, potentially forcing miners to choose between signaling support or mining a chain that some economic actors would reject.

If enough economic nodes (exchanges, services, businesses) ran BIP 148, miners faced a stark choice: signal SegWit support and get paid in bitcoin that the broader economy would accept, or mine a chain that major economic actors would ignore.

The threat of BIP 148 created powerful economic incentives that ultimately resolved the impasse:

- **BIP 91**: Locked in July 21, 2017 → Activated July 23, 2017 (enforced that miners signal bit-1, enabling BIP 141 to reach its threshold)
- **BIP 148 (UASF)**: Planned August 1, 2017 flag day to reject non-SegWit-signaling blocks  
- **SegWit (BIP 141)**: Locked in August 9, 2017 → Activated August 24, 2017 (block 481,824)

Faced with the credible threat that many economic nodes would enforce SegWit activation regardless of miner preferences, the miners began signaling support. BIP 91 was deployed as an intermediate solution that allowed miners to signal SegWit support before the August 1st UASF deadline.

The SegWit activation demonstrates several crucial principles:

1. **Economic nodes can influence protocol rules** when there's sufficient coordination. Miners must produce blocks that the economic majority will accept and value.
2. **Soft forks can be enforced by users** when there's sufficient economic coordination, even against miner resistance.
3. **Credible threats matter more than actual deployment**. BIP 148 succeeded largely because the threat was believable, not because a majority of nodes actually ran it.
4. **Bitcoin's governance is antifragile**. The system found a way to route around the blockade and activate beneficial upgrades despite coordinated resistance.

While SegWit technically activated via the original miner signaling mechanism (BIP 141), the credible UASF threat (BIP 148) was a significant catalyst that helped resolve the impasse. This demonstrated that Bitcoin users and economic nodes can coordinate to influence protocol governance, even when facing miner resistance.

#### Taproot (2021)

The **Taproot upgrade** significantly improved privacy, efficiency, and smart contract capabilities by combining two key technologies. Taproot locked in June 2021 and activated at **block 709,632** on **November 14, 2021 (05:15 UTC)**. **Schnorr Signatures** enable key and signature aggregation through schemes like MuSig2, allowing complex multi-party transactions to appear as single signatures on-chain. **Merkleized Abstract Syntax Trees (MAST)** structure complex spending conditions efficiently, where only the condition that's met needs to be revealed.

Together, these features provide major benefits: complex transactions become indistinguishable from simple payments for key-path spends, delivering significant privacy and scalability improvements. When script-path spends are used, only the revealed branch is disclosed, maintaining privacy for unused conditions.

### Network Standards and Features

#### Child-Pays-for-Parent (CPFP)
Create a new child transaction that spends an output from the stuck parent with a high fee rate, so miners include the package (parent + child) because the combined/ancestor feerate is attractive. Use CPFP when you can’t (or don’t want to) replace the parent but control one of its outputs (sender’s change or the recipient’s output).

#### Replace-by-Fee (RBF)
Sender rebroadcasts a higher-fee replacement of an unconfirmed transaction. Opt-in RBF is defined by BIP-125 (wallets signal replaceability); as of **Bitcoin Core v29**, **full-RBF is the default relay policy** in Core (the `mempoolfullrbf` option was removed). Use RBF when you control the original tx and it can be replaced.

RBF is a sender-driven replacement while CPFP is a child-driven package mining. Either party with a spendable output can do CPFP. They’re compatible and can be used together if needed.

#### OP_RETURN Data Embedding
The **OP_RETURN opcode** allows embedding small amounts of arbitrary data in transactions. The OP_RETURN relay cap (historically ~80 bytes) is slated to be removed **by default in Bitcoin Core v30** (policy, not consensus), effectively permitting much larger OP_RETURN data subject to transaction size/weight limits; operators can still run stricter policies.

### Address Types and Formats

Bitcoin addresses have evolved to improve efficiency and enable new features: Legacy (starts with 1) is the oldest and works everywhere but typically incurs slightly higher fees; P2SH (starts with 3) is a broad compatibility wrapper often used for multisig or older SegWit, and addresses starting with 3 are not necessarily multisig; Native SegWit (starts with bc1q) is the modern default with lower fees and all‑lowercase safety; and Taproot (starts with bc1p) is the newest, enabling advanced features with good fee efficiency and broad support across modern wallets (some services are still catching up).

---

## Section IV: Bitcoin Layer 2 and Extensions

### Lightning Network

What if you could make instant Bitcoin payments without waiting for block confirmations or paying high fees? The **Lightning Network** makes this possible through a clever Layer 2 protocol that moves most transactions off the main blockchain.

The concept is elegantly simple: instead of broadcasting every payment to the entire network, two parties can open a private **payment channel** by locking funds in a shared on-chain account (technically a 2-of-2 multisig output).

Once the channel is established, you and your counterparty can transact an unlimited number of times by updating your channel's balance sheet off-chain. All state changes require mutual agreement and are secured by cryptography. When you're finished, you can close the channel by broadcasting the final state to the Bitcoin blockchain. The network can also route your payments across multiple interconnected channels.

Lightning uses **HTLCs** and **onion routing** for private, trust-minimized payments; **watchtowers** help penalize cheating. Channel liquidity is directional (inbound vs outbound) and affects routing success; rebalancing and swap services help manage liquidity for reliable routing.

Think of Lightning as a canal system with locks. You can only send a boat if there’s enough water on your side (outbound capacity), and you can only receive if the other side has room to raise water to meet you (inbound capacity). Multi-hop routes work only when each lock along the path has water oriented the right way. Rebalancing is like shifting barges to move water back without closing the canal. HTLCs are sealed containers that either pass every lock intact or return unopened; onion routing means each lock‑keeper sees only the next hop, not the whole voyage.

---

## Section V: Bitcoin Network Operations and Security Model

### Roles at a Glance

**Users/wallets** create and sign transactions, then broadcast them to the network (you can do this without running your own node). **Full nodes** independently validate and relay transactions and blocks, enforcing consensus rules for themselves (running a node is not the same as mining). **Miners** assemble validated transactions into candidate blocks and perform Proof‑of‑Work to win block production (miners typically run a full node, but mining is the energy‑intensive block creation role).

#### What Miners Do—and Don't—Control

- **Control:** transaction inclusion and ordering; which valid fork they mine on; the possibility of short-term reorganizations and censorship within the rules.
- **Do not control:** the validity rules themselves (as established earlier, full nodes enforce validity rules; hash rate does not). Miners cannot make invalid blocks or rule changes "valid" without the consent of the nodes that verify and the market that values the coin; attempting to do so just creates a different chain that users can ignore.

#### Economic Majority and Social Choice

What the market calls “Bitcoin” is whatever coin users, exchanges, custodians, merchants, and wallets choose to value and transact. Miners are paid in that coin, so they’re strongly incentivized to mine the chain those actors accept. That influence is social/economic, not a protocol role: users still require some aligned hashrate for liveness and security on their chosen rules, and coordinating a true “economic majority” is hard in practice.

### Node Types and Network Topology

The Bitcoin network is a decentralized system composed of different participants:

- **Full nodes** form the network's backbone, storing the complete blockchain and independently validating all transactions and blocks against consensus rules.
- **Pruned nodes** offer the same validation security but discard old block data to save disk space.
- **SPV (Simplified Payment Verification) clients**, common in mobile wallets, download only block headers and trust full nodes for transaction validation.

To maintain its decentralized topology, the network relies on **DNS seeds** and peer-to-peer exchange for discovering other nodes.

### Block Propagation and Network Synchronization

When a new node joins, it performs an **Initial Block Download (IBD)** to sync the entire blockchain from its peers. To ensure new blocks propagate quickly and efficiently, the network uses optimized protocols like **Compact Block Relay**, which minimizes bandwidth by only sending information that nodes don't already have. Nodes also engage in **mempool synchronization** to share unconfirmed transactions. The network is resilient to partitions (temporary splits), which self-heal once connectivity is restored.

Additional efforts like **FIBRE** (fast relay) and **Erlay** (proposed mempool gossip reduction) improve propagation latency and bandwidth efficiency.

### Attack Vectors and Economic Security

Bitcoin's security is economic and probabilistic. The most cited threat is a **51% attack**, where an entity controlling a majority of the network's hashpower could attempt to rewrite history. However, the immense cost of acquiring and running this hardware, combined with the fact that a successful attack would devalue the asset, makes it economically irrational.

Security is achieved through **confirmation depth**; each subsequent block exponentially increases the work required to alter a transaction. This leads to **probabilistic finality**, where after a certain number of confirmations (e.g., six), a transaction is considered irreversible. The system is designed so that economic incentives strongly reward miners for honest behavior.

Other threats include **eclipse attacks** (peer isolation) and **selfish mining**; diversity of peers, network-level protections, and monitoring help mitigate these risks.

### Key Management and Wallet Security

The foundational principle of self-custody is **"Not your keys, not your coins."** Securely managing private keys is paramount:

- **Hierarchical Deterministic (HD) wallets** generate a nearly infinite number of addresses from a single backup seed phrase.
- **Multi-signature wallets** require multiple keys to authorize a transaction, distributing trust and securing funds.
- **Hardware wallets** provide the highest level of security by keeping private keys completely offline, isolated from internet-connected devices.

### Privacy Model

How private are your Bitcoin transactions? Bitcoin is **pseudonymous**, not anonymous. While your addresses are not directly linked to your real-world identity, transaction graph analysis can be used to cluster addresses and track the flow of funds. This risk is significantly increased by address reuse. Furthermore, **KYC/AML** (Know Your Customer/Anti-Money Laundering) regulations at exchanges create links between your on-chain activity and real-world identity, creating privacy gaps.

Common privacy practices include avoiding address reuse and optionally leveraging **CoinJoin-style tools** to reduce heuristic linking.

### Network Economics

At a system level, the miner **security budget** is total revenue paid to block producers over time: subsidy + fees (per block, per day, or per epoch). Expressed in BTC this is straightforward, but for gauging attack resistance the relevant unit is typically **USD per unit time**, since both miners and potential attackers procure hardware, facilities, and energy in fiat terms. As specialized hardware improves, the cost per hash declines; holding "hashes" constant does not hold attacker cost constant. What matters economically is the dollar cost to acquire and operate enough hash rate for long enough to reliably reorg the chain.

This framing underscores a long‑run concern: the subsidy halves roughly every four years (see Monetary Policy above). If transaction fees and/or BTC price do not rise sufficiently to offset successive halvings, the USD‑denominated security budget trends lower. A materially smaller budget can lead to miner exits, weaker competition for blocks, and a lower dollar cost for would‑be attackers to rent or acquire a majority share of hash rate for a window of time. In the limit (around 2140) the subsidy falls to ~0, so durable **fee demand** must carry the full security budget—via payments, L2 settlements, inscriptions, batched rollup data, and other valuable uses of block space. Healthy fee markets over the cycle are therefore not a cosmetic metric; they are the funding mechanism for Bitcoin’s long‑term security.

### Network Resilience and Antifragility

Bitcoin is designed to be **antifragile**—it grows stronger from stress and attacks. Its resilience stems from several factors:

- Geographic distribution of nodes and miners resists localized disruptions.
- **Protocol ossification**, or resistance to change, enhances stability and predictability.
- Its design assumes an adversarial environment, built to function despite malicious actors.

The network has survived numerous technical, political, and economic challenges, demonstrating its robust and self-healing nature.

### Bitcoin Inscriptions and Ordinals

**Ordinal Theory** is a social convention that assigns a unique serial number to every satoshi, allowing individual sats to be tracked and transferred. **Inscriptions** use this method to embed arbitrary data, like images or text, into the witness portion of a Bitcoin transaction.

This process became practical thanks to two soft forks: SegWit, which provided a block space discount for witness data, and Taproot, which enabled more flexible and larger script paths.

**BRC-20 tokens** are an experimental standard built on this technology, using JSON text inscriptions to signal "deploy," "mint," and "transfer" functions. An important limitation is that BRC-20s have no native token logic in consensus. Their state is not enforced by the Bitcoin protocol itself but is tracked by off-chain indexers that interpret the inscribed data.

Relay and mining policies for large inscriptions can vary, affecting inclusion and propagation.