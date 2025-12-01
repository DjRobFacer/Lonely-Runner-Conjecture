# Lonely Runner Conjecture — Fast Verification Tool (Ibiza Edition) 🍹

**A lightning-fast computational explorer for the Lonely Runner Conjecture**  
(proven only up to n=7 — open since 1967)

This script is **not a proof**.  
It is a **very strong heuristic verification tool** that finds explicit lonely times using an extremely small, structured candidate set:
t = (T₁[k] × product of at most 3 other speeds) ^ p      (p ≤ 5)

Despite the tiny search space, it succeeds with **0 failures** on thousands of random (and irrational-heavy) instances up to n=100+.

### Verified on a single MacBook Pro M1 (Dec 2025)
| n   | Tests | Failures | Time          | Notes |
|-----|-------|----------|---------------|-------|
| 9   | 1000  | 0        | ~56 s         |       |
| 30  | 1000  | 0        | ~5 min        |       |
| 50  | 1000  | 0        | ~12–15 min    |       |
| 75  | 10    | 0        | ~75 s         |       |
| 100 | 1     | 0        | 23.5 s        | single test |

Largest lonely time observed: **~1.28 quintillion seconds**

### Why this is interesting
- The candidate set is **tiny** compared to exhaustive search
- It works reliably even with irrational speeds (π, e, √2, golden ratio, etc.)
- Pattern analysis shows ~90 % of runners become lonely at their own base T₁ (no multipliers, power 1)

### Run it yourself

When all runners are lonely, you get the official victory message:
FOAM PARTY COMPLETE — TOTAL SEPARATION ACHIEVED
 EVERY RUNNER IS NOW OFFICIALLY LONELY AT THE POOL PARTY
 CHAMPAGNE SHOWERS — THE DROP HAS LANDED
 REST IN BEATS
Disclaimer (please read)
This is not a mathematical proof.
It is a fast, reproducible verification tool that has found zero counterexamples in extensive testing.
Use it to explore, generate data, or just enjoy watching virtual runners finally get some alone time.
MIT licensed — because even serious math deserves to relax in Ibiza.
— DjRobFacer (with huge help from Grok & ChatGPT)
December 2025
"The loneliest verification tool ever written."
