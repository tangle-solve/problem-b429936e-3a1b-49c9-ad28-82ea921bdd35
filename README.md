<!-- solve-problem:b429936e-3a1b-49c9-ad28-82ea921bdd35 -->
# Many-position preference auctions

A [Tangle Solve problem](https://solve.tangle.tools/problems/b429936e-3a1b-49c9-ad28-82ea921bdd35).

## What is the problem?

Design auction mechanisms for selling **positions in a blockchain block** (e.g., transaction ordering slots) to bidders with **private preferences over positions**. Formally: there are $k$ positions (slots) in a block and $n$ bidders; bidder $i$ has a private valuation function $v_i: [k] \to \mathbb{R}_{\ge 0}$ (value for being placed in each position, e.g., position 1 = front-run advantage). The auctioneer (block proposer) must (a) elicit preferences, (b) assign bidders to positions, (c) charge payments, all under a **communication budget** — bidders cannot communicate their full $k$-dimensional type, only a bounded number $b$ of bits/values per bidder.

Goals, in tension:
1. **Welfare** (sum of values of allocated positions) competitive with the efficient assignment (a maximum-weight bipartite matching).
2. **Incentive compatibility** (DSIC or Bayes-Nash).
3. **False-name resistance**: a bidder splitting into multiple identities cannot gain.
4. **Seller-shill resistance**: the auctioneer cannot profit by injecting fake bids ("shills") into unfilled positions.

The census record is informal; the precise target is: *characterize the Pareto frontier of welfare vs. communication complexity for $k$-position private-preference auctions, with false-name and shill proofness, matching upper and lower bounds.* Note the two-position case ($k=2$) is the only solved baseline (see below); the open problem is general $k$.

## Why does it matter?

MEV (maximal extractable value) makes transaction ordering a commodity worth billions annually; order-flow auctions (Flashbots-style) are the de facto mechanism, and naive ones leak welfare to proposers and bots. A many-position mechanism with proven guarantees would let block builders sell the whole ordering vector rather than just top-of-block placement, directly increasing user surplus and reducing predatory extraction. It also connects to a classic theory question — how much communication is needed to approximate optimal assignment/matching — with a novel adversarial twist (false names, shills) unique to pseudonymous blockchains.

## What's already known?

Per the census (dated claims, **verify** against the actual paper — it postdates my reliable knowledge):

- **Wang and Yaish (2026)** study exactly the two-position version ($k=2$) and prove welfare guarantees of **1/2** under one communication regime and **$1 - 1/e$** under a more generous one. The 1−1/e constant strongly suggests a reduction to a single-item second-price/VCG-style contest or a matroid/SR-type argument; the 1/2 likely comes from a "one bit per bidder" or posted-order scheme.
- Beyond this: the relevant imported theory is (i) **communication complexity of approximate maximum matching** (e.g., Goel–Lenzeni–Wierman type results showing $\Theta(1/\varepsilon^2)$ or constant-bit protocols achieving constant-factor matchings) and (ii) **false-name-proof combinatorial auctions** (Yokoo et al.), where strong impossibility results exist once valuations are combinatorial — position valuations are a structured (assignment-type) special case.
- Credible-auctions literature (roughly "credible auctions" à la Ferreira–Weinberg) gives shill-resistance techniques via cryptographic commitments by the seller.

No tight bounds are known for $k > 2$; the gap between the two-position constants and any many-position guarantee is entirely open, as are lower bounds.

## What would count as solving it?

1. **Upper bound**: For every $k$ and communication budget $b(n,k)$, a DSIC, false-name-proof, seller-shill-resistant auction with welfare $\ge \alpha(k, b) \cdot \text{OPT}$, with explicit $\alpha$.
2. **Lower bound**: A matching impossibility — no mechanism with the same properties and communication budget $b$ achieves welfare ratio better than $\alpha(k,b) + o(1)$ (communication-complexity or direct revelation argument).
3. **Solving the census variant** means closing the gap: e.g., showing $\alpha = 1/2$ for all $k$ with $O(1)$ communication, or that $\alpha$ must degrade with $k$, and pinning the exact frontier including the transition between the 1/2 and $1-1/e$ regimes.
4. Weaker but publishable: any many-position guarantee at all with a proof that false-name/shill constraints are the binding obstruction.

## How to start

1. **Read Wang–Yaish (arXiv:2608.00786 / SSRN 7217199) first, especially their lower-bound section** — the two-position communication lower bound is the template you must generalize; note exactly what communication model they use (bits per bid? one scalar per bidder?). This matters enormously and the census is vague.
2. Identify where the 1−1/e comes from: likely a reduction to selling one "effective slot" via a lottery, i.e., $1-1/e$ from the classic bound for single-item auctions with $m$ discrete levels. If so, try generalizing to $k$ slots via $k$ parallel lotteries and check whether false-name resistance survives (bidders entering multiple lotteries).
3. Import matching-communication results: constant-bit protocols for approximate maximum matching in bipartite graphs suggest $1/2$ or $2/3$ welfare may be achievable with tiny communication — the obstacle is that bidder types are *rows* of the weight matrix and cannot be queried by the auctioneer, plus the IC/shill constraints that matching literature ignores.
4. For lower bounds: known false-name impossibilities for combinatorial auctions (Yokoo) plus direct-rectangle communication arguments are the natural tools.

## Key references

- **Wang, L., Yaish, Y. (2026). "Order Auctions with Private Position Preferences." arXiv:2608.00786; SSRN 7217199.** Solves the two-position case: welfare guarantees of 1/2 and 1−1/e under different communication allowances, with false-name and seller-shill resistance. (Post-2025; citation details to verify.) https://arxiv.org/abs/2608.00786
- **Ferreira, M., Weinberg, S.M. (2021). "Credible, Truthful, and Two-Round (Optimal) Auctions via Cryptographic Commitments." EC 2021.** Introduces seller-shill/credible-auction model and commitment-based techniques for making the auctioneer honest — the credibility toolkit to import. https://arxiv.org/abs/2004.05677
- **Yokoo, M., Sakurai, Y., Matsubara, S. (2004). "The effect of false-name bids in combinatorial auctions: new fraud in Internet auctions." Games and Economic Behavior.** Foundational impossibility results for false-name-proof combinatorial auctions; defines the constraint your mechanism must evade.
- **Goel, A., Lenzini, R., Wierman, A. (2012). "Communication vs. Approximation: A Trade-off in Private Data Sampling."** (Representative of the communication-vs-approximation tradeoff literature; choose the specific matching-approximation paper after checking the Wang–Yaish related-work section.)

## Warnings

- The key reference is dated **August 2026 and I cannot verify its contents**; treat the 1/2 and 1−1/e claims and its exact communication model as needing confirmation before building on them.
- The **communication model is the crux**: "communication allowance" could mean bits per bid, bids per bidder, or rounds. Different formalizations give different answers; pin this down before proving anything.
- Position valuations may not be arbitrary: in MEV they are often monotone (earlier = weakly better). Check whether Wang–Yaish assume monotonicity — the problem is dramatically different for general vs. monotone $v_i$.
- False-name resistance often conflicts with VCG-style efficiency; naive "run $k$ second-price auctions" fails both false-name and shill resistance — don't spend a week on it.
- Don't conflate welfare of allocated positions with MEV revenue extracted; the objectives diverge when shills enter.

---
*`BCGT-000296` · flagged for review*

## Acceptance guidance

1. **Upper bound**: For every $k$ and communication budget $b(n,k)$, a DSIC, false-name-proof, seller-shill-resistant auction with welfare $\ge \alpha(k, b) \cdot \text{OPT}$, with explicit $\alpha$.
2. **Lower bound**: A matching impossibility — no mechanism with the same properties and communication budget $b$ achieves welfare ratio better than $\alpha(k,b) + o(1)$ (communication-complexity or direct revelation argument).
3. **Solving the census variant** means closing the gap: e.g., showing $\alpha = 1/2$ for all $k$ with $O(1)$ communication, or that $\alpha$ must degrade with $k$, and pinning the exact frontier including the transition between the 1/2 and $1-1/e$ regimes.
4. Weaker but publishable: any many-position guarantee at all with a proof that false-name/shill constraints are the binding obstruction.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/b429936e-3a1b-49c9-ad28-82ea921bdd35?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000296.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000296)
