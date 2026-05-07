# 1bit-systems Research Notes

Created: 2026-05-07

This fork is currently synced with upstream at:

`cc5ee4c05c58ff3787d92ffe03c8136abece7927`

Upstream:

`https://github.com/AmberLJC/LLMSys-PaperList`

Fork:

`https://github.com/bong-water-water-bong/LLMSys-PaperList`

## Why This Repo Matters

This is the primary research feed for 1bit-systems systems design. It covers the layers we need for a local Strix Halo inference plane:

- LLM serving
- agent systems
- edge serving
- memory-constrained inference
- KV cache management
- model routing
- RAG/agent workflow serving
- benchmark and workload traces

## 1bit-systems Design Lens

1bit-systems should be treated as a local heterogeneous inference control plane:

- NPU lane: low-power FLM / Ryzen AI / XDNA / small models / low-active-parameter MoE.
- iGPU lane: ROCm/HIP/Vulkan llama.cpp for dense GGUF and high decode throughput.
- CPU lane: fallback, utility models, compatibility.
- API lane: OpenAI-compatible surface for agents, RAG stacks, and local tools.

The research questions from this repo:

- How should the router pick backends?
- How should prefill and decode be scheduled separately?
- How should KV cache be reused across agent turns?
- How should fallback be logged so it is never silent?
- How should benchmark/eval artifacts drive routing decisions?
- What edge-serving tricks apply to Strix Halo's unified memory and iGPU/NPU split?

## First Reading Queue

### Edge / Local Hardware

- `PowerInfer`: fast LLM serving with a consumer-grade GPU.
- `MoE-Lightning`: high-throughput MoE inference on memory-constrained GPUs.
- `prima.cpp`: 70B-scale inference on low-resource home clusters.
- `Characterizing Mobile SoC for Accelerating Heterogeneous LLM Inference`.
- `LLM in a flash`: inference with limited memory.

### Agent Serving / Control Plane

- `KVFlow`: prefix caching for multi-agent workflows.
- `DroidSpeak`: KV cache sharing for cross-LLM communication and multi-LLM serving.
- `Murakkab`: resource-efficient agentic workflow orchestration.
- `Aragog`: just-in-time model routing for scalable agentic workflows.
- `DualPath`: storage bandwidth bottlenecks in agentic LLM inference.
- `METIS`: quality-aware RAG systems with configuration adaptation.

### Runtime / Kernel / Inference

- `vLLM`: PagedAttention.
- `FlashAttention` and `FlashAttention-3`.
- `SageAttention`, `SageAttention2`, `SageAttention3`.
- `SARATHI`: chunked prefill and decode scheduling.
- `DistServe`: prefill/decode disaggregation.
- `MineDraft`: batch-parallel speculative decoding.
- `Continuum`: multi-turn agent scheduling with KV cache TTL.

### Cache / Compression / Low-Bit Systems

- `H2O`: heavy-hitter KV cache management.
- `Scissorhands`: KV cache compression.
- `SpecInfer`: speculative inference.
- `Jetfire`: INT8 training and inference with per-block quantization.
- `TurboMind`: full-stack inference with mixed precision.
- `BitSnap`: checkpoint sparsification and quantization in training.

## What To Extract For 1bit-systems

For each paper we inspect, capture:

```text
paper:
url:
section:
problem:
key idea:
hardware assumptions:
runtime assumptions:
applies to Strix Halo? yes/no/partial
applies to NPU lane? yes/no/partial
applies to iGPU/ROCm lane? yes/no/partial
applies to CPU fallback? yes/no/partial
implementation idea for 1bit-systems:
benchmark idea:
risks/caveats:
```

## Immediate Implementation Implications

1. `1bit status` should include backend capability metadata, not just process health.

2. Benchmark artifacts should include:
   - backend
   - model
   - quant format
   - prompt tokens
   - decode tokens
   - prefill throughput
   - decode throughput
   - combined throughput
   - context length
   - flash attention flag
   - route selected
   - fallback reason

3. The OpenAI-compatible proxy should log routing decisions explicitly.

4. Silent fallback should be treated as a bug.

5. Agent workflows should get stable route IDs and trace IDs.

6. KV cache reuse needs to become a first-class design topic once the basic control plane is stable.

7. Ryzen AI / XDNA / FLM support should be modeled as a backend lane, not as the whole system.

## Watch Keywords

```text
LLM serving
agent systems
edge serving
consumer GPU
memory-constrained GPU
heterogeneous inference
mobile SoC inference
model routing
quality-aware routing
RAG serving
agentic workflow serving
KV cache
prefix caching
KV cache sharing
KV cache compression
PagedAttention
FlashAttention
SageAttention
speculative decoding
chunked prefill
prefill decode disaggregation
MoE inference
low-bit attention
INT4 attention
FP4 attention
superchip
```

