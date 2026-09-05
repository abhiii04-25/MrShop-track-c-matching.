# Design Decisions & Trade-offs — Track C

## Why rules-based scoring instead of ML

At launch, Mr.Shop has no historical interaction data to train a recommender
on (classic cold-start problem). A transparent, weighted rules-based score
(budget fit + style overlap + purchase history overlap) is:

- **Interpretable** — every recommendation can be explained in one sentence,
  which matters because the surface is a chat message, not a UI with filters
  the user can inspect themselves.
- **Fast to ship** — no training pipeline, no labeled data requirement.
- **Debuggable** — if a bad match surfaces, the breakdown (`budget: 0.2,
  style: 0.9, history: 0.1`) tells you exactly which signal is misfiring.

## Why these three signals and these weights

- **Budget fit (40%)** — weighted highest alongside style because a
  recommendation the user can't afford is actively annoying, not just
  suboptimal.
- **Style match (40%)** — the core value proposition of a *stylist*, so it
  needs equal weight to budget.
- **Purchase history (20%)** — a useful secondary signal but weighted lower
  since early on, most users won't have purchase history yet; it shouldn't
  dominate for new users.

These weights are a starting hypothesis, not a fixed truth — see scaling
notes below.

## How this scales

1. **Cold start → warm start**: as real purchase/interaction data
   accumulates, replace the hand-tuned weights with weights learned from
   actual conversion data (e.g. logistic regression over the same three
   features, or gradient boosting once more features exist).
2. **Tag overlap → embeddings**: the current Jaccard-style tag overlap is a
   reasonable proxy for "style match," but a true embedding-based similarity
   (e.g. encoding wardrobe + style tags into a vector and comparing to
   partner embeddings) would capture nuance that a fixed tag taxonomy misses
   (e.g. "streetwear" and "casual" being closer than "streetwear" and
   "formal" even without an explicit shared tag).
3. **More signals**: browsing behavior, explicit feedback ("not interested"),
   seasonal trends, and stylist-specific ratings can be added as additional
   weighted terms without changing the overall architecture.
4. **A/B testing weights**: once live, weights can be tuned via A/B tests
   measuring actual conversion/booking rate rather than assumption.

## What I'd add with more time

- A feedback loop where a user's reaction to a recommendation ("not my
  style") down-weights that partner's tags for that user going forward.
- Cold-start handling for *new partners* with no track record — e.g. a small
  exploration boost so new partners get shown even if their score is
  slightly lower, so the system doesn't just entrench early winners.
- Deduplication/diversity logic so top-3 matches aren't all near-identical
  price points from the same category.
