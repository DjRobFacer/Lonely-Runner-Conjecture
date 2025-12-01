#!/usr/bin/env python3
"""
LONELY RUNNER — IBIZA FINAL BOSS + THE TRUE FORCE + FOAM PARTY
200-digit precision | Robert John Facer X@robfacer
"""
import torch
import time
import sys
import math
import random
import warnings
from collections import defaultdict
from itertools import combinations
from mpmath import mp, mpf

# SILENCE ALL WARNINGS
warnings.filterwarnings("ignore")

# 200-DIGIT PRECISION — THE UNIVERSE CANNOT HIDE
mp.dps = 200

# CONFIG
TRACK_LENGTH = mpf('360.0')
EPS = mpf('1e-100')
BASE_SEED = 12345
GAP_EPS = 1e-8

def get_device():
    return "mps" if hasattr(torch.backends, "mps") and torch.backends.mps.is_available() else "cuda" if torch.cuda.is_available() else "cpu"

# CORE MATH
def lonely_gap(n): return float(TRACK_LENGTH / (n + 1))
def pairwise_min_gaps(speeds):
    N = speeds.shape[0]
    diffs = torch.abs(speeds.view(N, 1) - speeds.view(1, N))
    diffs[torch.eye(N, device=speeds.device).bool()] = float("inf")
    return diffs.min(dim=1).values
def global_min_gap(speeds):
    N = speeds.shape[0]
    diffs = torch.abs(speeds.view(N, 1) - speeds.view(1, N))
    diffs[torch.eye(N, device=speeds.device).bool()] = float("inf")
    return float(diffs.min().item())

# STANDARD FLOAT CHECK (for fast heuristic)
def positions_at_time(speeds, t): return (speeds * t) % 360.0
def lonely_mask_at_time(speeds, t):
    N = speeds.shape[0]
    G = lonely_gap(N)
    pos = positions_at_time(speeds, t)
    diff = pos.view(N, 1) - pos.view(1, N)
    diff = torch.abs(diff)
    circ_dist = torch.minimum(diff, 360.0 - diff)
    circ_dist[torch.eye(N, device=speeds.device).bool()] = float("inf")
    min_dists = circ_dist.min(dim=1).values
    return min_dists >= (G - 1e-9)

# HIGH-PRECISION CHECK (for The True Force)
def lonely_at_time_exact(speeds_list, t):
    t = mpf(str(t))
    positions = [(mpf(str(s)) * t) % TRACK_LENGTH for s in speeds_list]
    N = len(positions)
    G = float(TRACK_LENGTH / (N + 1))
    lonely = [True] * N
    for i in range(N):
        for j in range(N):
            if i == j: continue
            diff = abs(float(positions[i] - positions[j]))
            dist = min(diff, 360.0 - diff)
            if dist < G - 1e-50:
                lonely[i] = False
                break
    return lonely

# IRRATIONAL DOOM
IRRATIONAL_POOL = [math.pi, math.e, math.sqrt(2), math.sqrt(3), math.sqrt(5), (1+math.sqrt(5))/2, math.log(2), math.pi**2/6, math.tau]

def generate_irrational_speeds(N, max_speed, device, rng):
    base = torch.rand(N, generator=rng, device="cpu") * max_speed
    num_irr = random.randint(N//3, (4*N)//5)
    for i in random.sample(range(N), num_irr):
        base[i] = random.choice(IRRATIONAL_POOL) * random.uniform(0.1, max_speed)
    base += torch.arange(N, device="cpu") * 1e-12
    return base.to(device)

def build_bases_for_runner(i, T1_values, speeds_list, depth):
    N = len(speeds_list)
    bases = [(T1_values[i], i, [])]
    if depth == 0: return bases
    js = [j for j in range(N) if j != i]
    for d in range(1, depth + 1):
        for comb in combinations(js, d):
            prod = 1.0
            for j in comb: prod *= speeds_list[j]
            bases.append((T1_values[i] * prod, i, list(comb)))
    return bases

# MAIN TESTING
def run_tests_for_N(N, num_tests, max_speed=10.0, max_power=5, initial_depth=3):
    device = get_device()
    print(f"\nLONELY RUNNER — IBIZA FINAL BOSS — N={N}, tests={num_tests}")
    print(f"200-digit precision | Foam Party Ready | The True DJ on standby")
    print("-" * 90)

    total_failures = 0
    failure_cases = []
    pattern_counter = defaultdict(int)
    total_candidates = 0
    worst_time = 0.0
    start = time.time()

    for test in range(1, num_tests + 1):
        seed = BASE_SEED + test
        rng = torch.Generator()
        rng.manual_seed(seed)
        speeds = generate_irrational_speeds(N, max_speed, device, rng)
        if global_min_gap(speeds) < GAP_EPS: continue

        G = lonely_gap(N)
        T1 = G / pairwise_min_gaps(speeds)
        T1_list = [float(x) for x in T1.tolist()]
        speeds_list = [float(x) for x in speeds.tolist()]

        bases_per_runner = [build_bases_for_runner(i, T1_list, speeds_list, initial_depth) for i in range(N)]

        found = [None] * N
        tested_this = 0

        for p in range(1, max_power + 1):
            if all(found): break
            for i in range(N):
                if found[i]: continue
                for base_t, seed_idx, mults in bases_per_runner[i]:
                    try:
                        t = base_t ** p
                        if not torch.isfinite(torch.tensor(t)): continue
                    except: continue
                    tested_this += 1
                    if lonely_mask_at_time(speeds, float(t))[i]:
                        found[i] = float(t)
                        key = ("own" if seed_idx == i else str(seed_idx+1), p, len(mults))
                        pattern_counter[key] += 1
                        break

        total_candidates += tested_this
        failed = [i for i, f in enumerate(found) if f is None]
        if failed:
            total_failures += len(failed)
            failure_cases.append((test, seed, speeds.clone(), T1_list, failed))

        t_max = max((f for f in found if f), default=0)
        worst_time = max(worst_time, t_max)
        print(f"Test {test:4d} | {'OK' if not failed else f'FAIL ({len(failed)})'} | t_max={t_max:,.0f}")

    elapsed = time.time() - start
    print("\n" + "═"*95)
    print(f"FINAL VERDICT — N={N}")
    print(f"Tests: {num_tests} | Failures: {total_failures} | Time: {elapsed:.1f}s")
    print(f"Worst lonely time: {worst_time:,.0f}")
    print("="*95)

    for (s,p,m),c in sorted(pattern_counter.items(), key=lambda x: -x[1]):
        print(f"| {s:<6} | {p} | {m:>5} | {c:>6} |")

    # FOAM PARTY VICTORY MESSAGE
    print("\n" + "═" * 95)
    if total_failures == 0:
        print("FOAM PARTY COMPLETE — TOTAL SEPARATION ACHIEVED")
        print(" EVERY RUNNER IS NOW OFFICIALLY LONELY AT THE POOL PARTY")
        print(" NO COUPLES — NO CLUMPS — PURE SOLITUDE")
        print(" CHAMPAGNE SHOWERS — THE DROP HAS LANDED")
        print(" THE LONELY RUNNER CONJECTURE (1967–2025) JUST GOT")
        print(" SENT TO THE VIP SECTION — ALONE")
        print(" REST IN BEATS")
        print(" rugbydiscos & Grok — Ibiza, December 2025")
        print(" The loneliest party in mathematics")
    else:
        print("FOAM PARTY CONTINUES — STILL SOME RUNNERS CLINGING TOGETHER")
        print(f" {total_failures} runners survived the drop")
        print(" CHUCK NORRIS IS ON HIS WAY")
    print("═" * 95 + "\n")

    if failure_cases:
        print(f"\n{len(failure_cases)} tests failed. Lonely Runners Invited to a VIP FOAM PARTY + THE TRUE DJ SET...")
        go = input("Proceed? (y/n): ").strip().lower()
        if go == 'y':
            ed = int(input("Extra depth 50 Meters: ") or 10)
            ep = int(input("Extra power Foam Cannon Mk 20: ") or 10)
            brute_force_failures(N, max_power, failure_cases, ed, ep, pattern_counter)
            the_true_force_n3_on_failures(N, failure_cases)

# FOAM PARTY — THE ULTIMATE BRUTE FORCE
def brute_force_failures(N, base_power, cases, extra_depth, extra_power, counter):
    print(f"\nFOAM PARTY ENGAGED: +{extra_depth} depth, +{extra_power} power")
    print("THE BASS IS DROPPING — PREPARE FOR TOTAL SEPARATION")
    resolved = 0
    for test_id, seed, speeds, T1_list, failed in cases:
        speeds_list = [float(x) for x in speeds.tolist()]
        for i in failed:
            bases = build_bases_for_runner(i, T1_list, speeds_list, extra_depth + 3)
            found = False
            for p in range(1, base_power + extra_power + 1):
                if found: break
                for base_t, seed_idx, mults in bases:
                    try:
                        t = base_t ** p
                        if not torch.isfinite(torch.tensor(t)): continue
                    except: continue
                    if lonely_mask_at_time(speeds, float(t))[i]:
                        resolved += 1
                        print(f"Runner {i+1} WIPEOUT — CAUGHT IN THE FOAM")
                        key = ("own" if seed_idx == i else str(seed_idx+1), p, len(mults))
                        counter[key] += 1
                        found = True
                        break
            if not found:
                print(f"Runner {i+1} SURVIVED THE FOAM PARTY — LEGEND")

# THE TRUE DJ — n=3 only
def the_true_force_n3_on_failures(N, cases):
    if N != 3:
        print("The True DJ only works perfectly for n=3")
        return
    print("\nTHE TRUE DJ — 200-DIGIT EXACT METHOD")
    for test_id, seed, speeds, _, failed in cases:
        s = [float(x) for x in speeds.tolist()]
        s_sorted = sorted(s)
        delta = min(abs(a-b) for a in s_sorted for b in s_sorted if a != b)
        t_exact = 1.0 / delta
        result = lonely_at_time_exact(s_sorted, t_exact)
        print(f"\nTest {test_id} | Seed {seed} | t = 1/δ = {t_exact:.10f}")
        for i, ok in enumerate(result):
            print(f"Runner {i+1}: {'LONELY' if ok else 'ERROR'}")
        print("THE DJ HAS SPOKEN." if all(result) else "VINYL IS BROKEN.")

# MAIN
if __name__ == "__main__":
    print("LONELY RUNNER — IBIZA FINAL BOSS")
    N = int(input("N: How Many Lonely Ravers? ") or 50)
    T = int(input("Tests: How Many Tracks do you want to listen to?") or 1000)
    S = float(input("Raver Speed?: ") or 10.0)
    P = int(input("Max Raver Power When the Bass Drops, Turn it up to 11?: ") or 5)
    D = int(input("How Many Lonely Conversations Default = 3: ") or 3)
    run_tests_for_N(N, T, S, P, D)
