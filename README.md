# gguf-vram-calculator

One static page. Answers one question: **will this GGUF quant fit on this card?**

No build step, no dependencies, no network requests — a single `index.html` you can open from disk.

## The math (stated, not vibes)

```
weights  = params × 1e9 × (quant bpw ÷ 8) × 1.08
kv cache = 2 × layers × kv_heads × head_dim(128) × 2 bytes × context × kv_quant_factor
overhead = CUDA context + cuBLAS + compute buffers (≈ 0.6–1.2 GiB)
```

The 8% weight fudge is what real files show once embeddings and the output head stay above
the quant's nominal bits-per-weight. Verified against public GGUF files:

| Model · quant | Real file | This math |
|---|---|---|
| Llama-2 7B · Q4_K_M | 4.08 GiB | 4.09 |
| Llama-3 8B · Q4_K_M | 4.92 GiB | 4.88 |
| Mistral 7B · Q4_K_M | 4.37 GiB | 4.39 |
| Llama-3 8B · Q8_0 | 8.50 GiB | 8.55 |

What it does **not** count: MoE routing sharding, draft models, and whatever your desktop
already took.

## Use it

- Live: **https://mrsaynothing.github.io/gguf-vram-calculator/**
- Or: clone, open `index.html`. That's the whole install.

## Embed

```html
<iframe src="https://mrsaynothing.github.io/gguf-vram-calculator/"
        style="width:100%;height:1100px;border:0" loading="lazy" title="GGUF VRAM calculator"></iframe>
```

## Why this exists

Every "can I run X on my GPU" thread re-derives this arithmetic from scratch, and half the
answers forget the KV cache entirely. The calculator puts the three numbers — weights,
KV cache, overhead — in one place, with the formulas printed so you can check them.

Part of the [local-LLM cluster on mrsaynothing.dev](https://mrsaynothing.dev/?utm_source=gguf-vram-calculator&utm_medium=readme&utm_campaign=tool) —
the GGUF board lives at [mrsaynothing.dev/en/board/gguf](https://mrsaynothing.dev/en/board/gguf?utm_source=gguf-vram-calculator&utm_medium=readme&utm_campaign=tool).

## License

MIT
