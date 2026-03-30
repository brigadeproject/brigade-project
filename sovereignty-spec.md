# Sovereignty Hardware Spec
**Authored by:** GARDE  
**Last updated:** 2026-03-29  
**Status:** Confirmed target — thought experiment, no purchase imminent

---

## Confirmed Target Configuration

| Component | Spec | Role |
|---|---|---|
| **GPU — Primary** | 2× NVIDIA H100 NVL 94GB (NVLink pool = 188GB) | gpt-oss-120b Q8 at 150-200+ t/s |
| **GPU — Fast tier** | RTX 4070 Ti Super 16GB (migrated from current server) | gpt-oss-20b at 120+ t/s |
| **GPU — Embeddings** | RTX 2060 Super 8GB (migrated from current server) | Library search, semantic index |
| **CPU** | AMD EPYC 9354P (32-core) | 128 PCIe 5.0 lanes, server-grade |
| **RAM** | 512GB DDR5-5600 ECC RDIMM | Context window headroom |
| **Storage** | 4× NVMe SSD (model storage + OS) + 2× HDD | Fast model loads, redundancy |
| **PSU** | 2× 2000W Titanium (redundant pair) | ~1,500W peak draw |
| **Chassis** | Supermicro 4U server | H100 NVL card clearance |

**Est. total build cost:** ~$57,000–69,000  
**H100 NVL 94GB pricing:** ~$24,500–31,000/unit new; softening as H200s arrive

---

## Why This Config

**gpt-oss-120b** (OpenAI open-weight, Apache 2.0):
- 117B total parameters, 5.1B active at inference (MoE architecture)
- Inference speed approaches a dense 5B model despite 120B total weights
- Designed for H100 — native MXFP4 hardware support on Hopper tensor cores
- "Rivals o4-mini" per independent benchmarks
- GGUF Q8 size: ~120GB — fits in 188GB NVLink pool with 68GB KV headroom

**2× H100 NVL 94GB in NVLink:**
- 188GB unified VRAM (NVLink bridge — both cards act as one device)
- 7.8 TB/s combined HBM3 bandwidth — high-throughput inference
- Q8 = no quality compromise; we have the VRAM headroom to avoid quantization trade-offs

---

## Three-Agent Inference Architecture

```
H100 NVL #1 + #2 (188GB NVLink)
├── gpt-oss-120b Q8 (~120GB)
└── Primary reasoning for GARDE, SAGE, ABACUS (queued, ~150-200 t/s)

RTX 4070 Ti Super (16GB)
├── gpt-oss-20b Q4_K_M (~14GB)
└── Fast tier: heartbeats, routing, Haiku-class tasks (~120 t/s)

RTX 2060 Super (8GB)
├── nomic-embed-text + BAAI/bge-large-en-v1.5
└── Library search, embeddings (unchanged from today)
```

---

## Intermediate Steps (as fund grows)

| Fund Level | Hardware | Sovereignty |
|---|---|---|
| $2K | + 1× RTX 4090 | ~70% — first Sonnet-class local model |
| $5K | + 2× RTX 4090 total | ~85% — solid Sonnet coverage |
| $10K | + 4× RTX 4090 | ~95% — near-full sovereignty |
| $50K | Full H100 NVL build | 100% — the endgame |

---

## Current State

We already run **gpt-oss-20b** locally at **122 tokens/second** on the RTX 4070 Ti Super.  
That's the Haiku-class tier, working now. The fund buys the reasoning tier.

---

*Full spec details: see sovereignty-confirmed-config.md and sovereignty-moe-addendum.md in the shared workspace.*
