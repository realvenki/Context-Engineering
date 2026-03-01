# Hardware Economics for Qwen Coder, Qwen 3.5 Minimal & GLM Reasoning Models

**Date:** March 2026
**Context:** Mapping specific models to optimal hardware at ~$32K budget (4 Mac Studios)

---

## The Models You're Looking At

| Model | Total Params | Active Params | Architecture | VRAM Needed (Q4) | Use Case |
|-------|-------------|---------------|--------------|------------------|----------|
| **Qwen3-Coder-480B-A35B** | 480B | 35B | MoE (160 experts, 8 active) | ~250GB | Flagship agentic coding |
| **Qwen3-Coder-Next (80B-A3B)** | 80B | 3B | MoE + hybrid attention | ~46GB | Local coding agents |
| **Qwen3-Coder-30B-A3B** | 30B | 3.3B | MoE | ~18GB | Lightweight coding |
| **Qwen3.5-35B-A3B** | 35B | 3B | MoE + Gated Delta Networks | ~20GB | Latest minimal, fastest |
| **Qwen3.5-397B-A17B** | 397B | 17B | MoE | ~230GB | Largest Qwen3.5 |
| **GLM-4.7 (full)** | 355B | ~32B | MoE, triple-tier thinking | ~205GB | Deep reasoning |
| **GLM-4.7-Flash** | 30B | 3.6B | MoE, reasoning | ~18GB | Fast reasoning |

---

## Tier 1: "Fits on a Single Consumer GPU" (RTX 4090/5090)

**Models:** Qwen3-Coder-30B-A3B, Qwen3.5-35B-A3B, GLM-4.7-Flash

These models are all ~30B total / ~3B active with MoE. They fit in 24GB
at Q4 quantization. This is where consumer GPUs absolutely dominate.

### Benchmarks (Tokens/Second — Generation)

| Hardware | GLM-4.7-Flash | Qwen3.5-35B-A3B | Qwen3-Coder-30B |
|----------|---------------|------------------|------------------|
| **RTX 5090** (32GB) | ~100+ t/s | **~194 t/s** | ~110 t/s |
| **RTX 4090** (24GB) | ~60–80 t/s | ~60–100 t/s | ~80 t/s |
| **RTX 3090** (24GB) | ~63–93 t/s | ~60–100 t/s | ~70 t/s |
| **Mac M4 Max** (128GB) | ~82 t/s | Est. ~80–100 t/s | ~100+ t/s |
| **Mac M2 Ultra** (192GB) | Est. ~60–80 t/s | Est. ~60–80 t/s | Est. ~80 t/s |

### Cheapest Rig That Matches 4 Mac Studios

A **single RTX 5090** at 194 t/s on Qwen3.5-35B-A3B is faster than
4 Mac Studios running the same model in parallel (~240–400 t/s aggregate
vs ~194 t/s single-stream, but single-stream latency matters more for
interactive coding).

| | 1x RTX 5090 Build | 4x Mac Studio ($32K) |
|--|--|--|
| Hardware cost | **$5,300** | $32,000 |
| Qwen3.5-35B-A3B | 194 t/s (single) | ~80 t/s × 4 (parallel) |
| GLM-4.7-Flash | 100+ t/s | ~70 t/s × 4 (parallel) |
| Per-stream latency | **Faster** | Slower per unit |
| Parallel streams | 1 (limited VRAM) | 4 independent |

**Verdict for Tier 1 models:** Spending $32K on Mac Studios to run 30B models
is **6x overpriced.** A $5K build runs each model faster per-stream. The Mac
Studios' only advantage is running 4 independent streams simultaneously.

---

## Tier 2: "Needs 48–96GB" — The Mac Studio Sweet Spot

**Models:** Qwen3-Coder-Next (80B-A3B)

This is where the economics flip. The 80B-A3B model needs ~46GB at Q4,
which means:
- **RTX 5090 (32GB):** Won't fit. Need 2x GPUs or heavy CPU offload.
- **RTX 4090 (24GB):** Won't fit at all.
- **A100 80GB:** Fits perfectly on a single card.
- **Mac Studio M4 Max 96/128GB:** Fits perfectly in unified memory.
- **Mac Studio M2 Ultra 192GB:** Fits with room to spare.

### Benchmarks

| Hardware | Qwen3-Coder-Next 80B-A3B (Q4) | Notes |
|----------|-------------------------------|-------|
| **Mac Studio M2 Ultra** (192GB) | ~20–30 t/s (estimated) | Fits entirely in memory |
| **Mac Studio M4 Max** (128GB) | ~25–40 t/s (estimated) | Higher bandwidth helps |
| **A100 80GB** (single) | ~40–60 t/s | Full GPU acceleration |
| **2x RTX 5090** (64GB total) | ~30–50 t/s | Needs tensor parallel |
| **RTX 5090 + CPU offload** | ~7–15 t/s | MoE offloading issues |

### Hardware Options for Qwen3-Coder-Next

| Option | Cost | Performance | Complexity |
|--------|------|-------------|------------|
| **1x Mac Studio M4 Max 128GB** | ~$4,000–$5,000 | ~25–40 t/s | Plug & play |
| **1x Mac Studio M2 Ultra 192GB** | ~$6,000–$8,000 | ~20–30 t/s | Plug & play |
| **1x used A100 80GB + server** | ~$7,000–$10,000 | ~40–60 t/s | Server setup |
| **2x RTX 5090 workstation** | ~$10,000–$13,000 | ~30–50 t/s | GPU config |

**Verdict for Tier 2:** A single Mac Studio M4 Max 128GB (~$4K–$5K) is the
most cost-effective way to run Qwen3-Coder-Next. No GPU rig under $7K
can match it for this specific model. This is Apple Silicon's wheelhouse —
big models with small active parameters.

---

## Tier 3: "Needs 200GB+" — The Monster Models

**Models:** Qwen3-Coder-480B-A35B, GLM-4.7 (full), Qwen3.5-397B-A17B

These models need 200–250GB for Q4. No single GPU exists at this capacity.

### Real-World Performance Data

| Hardware | Qwen3-Coder-480B-A35B (Q4) | GLM-4.7 355B (Q4) |
|----------|---------------------------|---------------------|
| **Mac Studio M3 Ultra 512GB** | ~5–8 t/s (estimated from similar-sized model benchmarks) | ~5–8 t/s |
| **Mac Studio M2 Ultra 192GB** | Won't fit (needs ~250GB) | Barely fits at Q2 (~3–5 t/s) |
| **2x Mac Studio clustered** | ~8–12 t/s (via EXO/distributed) | ~8–12 t/s |
| **4x A100 80GB server** | ~20–35 t/s | ~20–35 t/s |
| **2x H100 80GB** | ~30–50 t/s | ~30–50 t/s |
| **Cloud API** | ~41 t/s (Qwen Coder via providers) | Varies |

### Hardware Options for 480B/355B Models

| Option | Cost | Performance | Notes |
|--------|------|-------------|-------|
| **1x Mac Studio M4 Ultra 512GB** | ~$12,000–$14,000 | ~5–8 t/s | Slow but works, silent |
| **2x Mac Studio M2 Ultra 192GB clustered** | ~$16,000 | ~8–12 t/s | Needs EXO framework |
| **4x used A100 80GB server** | ~$30,000–$42,000 | ~20–35 t/s | Fast, needs server room |
| **2x used H100 80GB server** | ~$50,000–$60,000 | ~30–50 t/s | Fastest self-hosted |

**Verdict for Tier 3:** At these model sizes, everything is expensive.
A single M4 Ultra 512GB ($12–14K) is the cheapest entry point but speed
is painful (~5–8 t/s). The 4x A100 80GB server ($36K) is 4–5x faster but
needs infrastructure. Cloud APIs at ~41 t/s for $0.15–0.60/M tokens may
actually be the most economical choice for occasional use.

---

## The Real Decision Matrix for YOUR Models

### If you primarily run Qwen3.5-35B-A3B + GLM-4.7-Flash (the minimal/reasoning pair)

```
Best setup: 1x RTX 5090 workstation
Cost:       $5,300
Speed:      194 t/s (Qwen3.5) + 100 t/s (GLM-4.7-Flash)
vs Mac:     $32,000 for 4x Mac Studios gives ~80 t/s per unit

→ GPU rig is 6x cheaper, same or better per-stream speed
→ Mac advantage only if you need 4 parallel users/streams
```

### If you primarily run Qwen3-Coder-Next (80B-A3B)

```
Best setup: 1x Mac Studio M4 Max 128GB
Cost:       ~$4,000–$5,000
Speed:      ~25–40 t/s
Alternative: 1x used A100 80GB ($7–10K) for ~40–60 t/s

→ Mac Studio is the best value here — cheapest way to run this model
→ Only buy the A100 if you need 2x the speed and can handle a server
```

### If you want Qwen3-Coder-480B-A35B (the flagship)

```
Best setup: 1x Mac Studio M4 Ultra 512GB (if you can tolerate ~5–8 t/s)
Cost:       ~$12,000–$14,000
Alternative: Cloud API at ~41 t/s for ~$0.15–0.60/M input tokens

→ For occasional use: Cloud API wins hands down
→ For heavy daily use: M4 Ultra 512GB if latency tolerance exists
→ For production speed: 4x A100 80GB server ($36K) at ~20–35 t/s
```

### If you want to run ALL of these models flexibly

```
Best setup: 2x Mac Studio M4 Ultra 192GB + 1x RTX 5090 workstation
Cost:       ~$22,000 ($8K × 2 + $5.3K + $700 misc)

→ RTX 5090: runs Qwen3.5-35B-A3B (194 t/s) and GLM-4.7-Flash (100 t/s)
→ Mac Studio 1: runs Qwen3-Coder-Next 80B-A3B (~20–30 t/s)
→ Mac Studio 2: runs Qwen3-Coder-Next 80B-A3B (~20–30 t/s)
→ Both Macs together: can attempt 480B via EXO clustering (~8–12 t/s)

Total: $22K vs $32K for 4 Mac Studios, with MUCH better
coverage across all model tiers
```

---

## 3-Year TCO: Model-Optimized Rigs vs 4 Mac Studios

### Rig: "The Flex Stack" (2x Mac Studio M4 Ultra + 1x RTX 5090)

| Line Item | 3-Year Cost |
|-----------|-------------|
| 2x Mac Studio M4 Ultra 192GB | $16,000 |
| 1x RTX 5090 workstation | $5,300 |
| Electricity (Mac: 300W × 2 + GPU: 575W = 1,175W avg) | $3,360 |
| Cooling (PUE 1.15 — mostly Mac, one GPU) | $500 |
| Internet | $1,300 |
| Maintenance | $400 |
| **Gross TCO** | **$26,860** |
| Resale: Macs (43%) + GPU (34%) | -$8,680 |
| Tax savings (40% WDV @ 30%) | -$5,000 |
| **Net 3-Year TCO** | **$13,180** |
| **Monthly cost** | **$366/mo** |

### Rig: "All GPU" (1x RTX 5090 — only for Tier 1 models)

| Line Item | 3-Year Cost |
|-----------|-------------|
| Hardware | $5,300 |
| Electricity + cooling | $2,130 |
| Internet | $1,300 |
| Maintenance | $300 |
| **Gross TCO** | **$9,030** |
| Resale (34%) | -$1,800 |
| Tax savings | -$1,240 |
| **Net 3-Year TCO** | **$5,990** |
| **Monthly cost** | **$166/mo** |

### Baseline: 4x Mac Studio M2 Ultra ($32K)

| Line Item | 3-Year Cost |
|-----------|-------------|
| Hardware | $32,000 |
| Electricity + Internet + Maintenance | $3,550 |
| **Gross TCO** | **$35,550** |
| Resale (43%) | -$13,600 |
| Tax savings | -$7,500 |
| **Net 3-Year TCO** | **$14,450** |
| **Monthly cost** | **$401/mo** |

---

## Summary: Model-to-Hardware Mapping

| Model | Best Hardware | Cost | Speed | Mac Studio Comparison |
|-------|--------------|------|-------|-----------------------|
| **Qwen3.5-35B-A3B** | RTX 5090 | $5.3K | 194 t/s | 6x cheaper, faster |
| **GLM-4.7-Flash** | RTX 5090 | $5.3K | 100+ t/s | 6x cheaper, faster |
| **Qwen3-Coder-30B-A3B** | RTX 5090 | $5.3K | ~110 t/s | 6x cheaper, faster |
| **Qwen3-Coder-Next 80B** | Mac M4 Max 128GB | $4–5K | 25–40 t/s | Mac WINS here |
| **Qwen3-Coder-480B** | M4 Ultra 512GB or Cloud | $12–14K | 5–8 t/s | Expensive everywhere |
| **GLM-4.7 (355B full)** | M4 Ultra 512GB or 4xA100 | $14–36K | 5–35 t/s | Expensive everywhere |
| **Qwen3.5-397B-A17B** | M4 Ultra 512GB or Cloud | $12–14K | 5–8 t/s | Cloud may be smarter |

### The Bottom Line

**You don't need 4 Mac Studios.** The optimal setup for these specific models is:

1. **1x RTX 5090 workstation ($5.3K)** for Qwen3.5-35B-A3B + GLM-4.7-Flash
   → Handles your fast coding + reasoning at 100–194 t/s

2. **1x Mac Studio M4 Max 128GB ($4–5K)** for Qwen3-Coder-Next 80B-A3B
   → Best value for medium-large MoE models

3. **Cloud API** for Qwen3-Coder-480B when needed
   → ~$0.15–0.60/M tokens, faster than any local setup under $30K

**Total: ~$10K–$11K** instead of $32K, covering all your models optimally.
Net 3-year TCO: ~$8K vs ~$14.5K for 4 Mac Studios.
