AI.Engineering

## Comprehension Debt

**id:** ai-comprehension-debt

**question:** What is comprehension debt when coding with AI?

**answer:** The accumulated gap between code or decisions accepted from AI and what the developer actually understands well enough to explain, debug, and maintain.

**mechanism:**
- Fast generation can outpace reading and validation.
- Unexplained assumptions compound across changes.
- Bugs become expensive when nobody can trace the reasoning.

**tradeoffs:** Delegate routine work, but require tests, architecture review and concise explanations for unfamiliar or consequential code.

**example:** An agent generates an authentication flow that passes simple tests, but the team cannot explain its token refresh or failure behavior.

**caveat:** A practical engineering framing, not a standardized scientific term. Added from the user's terminology; exact wording in the linked video has not been independently transcript-verified.

**source:** https://www.youtube.com/watch?v=DQBqh6gcx7U

---

AI.Engineering

## Intent Debt

**id:** ai-intent-debt

**question:** What is intent debt in AI-assisted development?

**answer:** The growing distance between what you actually want a system to accomplish and the requirements, constraints and design intent explicitly captured for the AI or future maintainers.

**mechanism:**
- Vague prompts leave important decisions implicit.
- AI may optimize for passing the immediate task, not long-term architecture.
- Subsequent edits inherit the mismatch.

**tradeoffs:** Write explicit acceptance criteria, invariants, boundaries and non-goals before delegating complex changes.

**example:** “Implement billing” produces working charges but misses refunds, idempotency, reconciliation and access boundaries.

**caveat:** Useful conceptual label; exact claims or definitions from the linked video have not been independently transcript-verified.

**source:** https://www.youtube.com/watch?v=DQBqh6gcx7U

---

AI.Engineering

## Cognitive Surrender

**id:** ai-cognitive-surrender

**question:** What does cognitive surrender mean in AI-assisted work?

**answer:** Deferring judgment to a plausible AI answer instead of independently assessing whether its assumptions, reasoning and result are correct.

**mechanism:**
- Fluency makes output feel authoritative.
- Developers may stop checking edge cases or alternatives.
- Repeated delegation can erode situational awareness.

**tradeoffs:** Use AI as a collaborator; retain ownership of architectural choices, correctness tests and final verification.

**example:** Accepting a generated database migration without questioning locking, backward compatibility or rollback.

**caveat:** This is a conceptual review aid, not a clinical diagnosis; the video transcript was not independently verified.

**source:** https://www.youtube.com/watch?v=DQBqh6gcx7U

---

AI.LLMInference

## KV Cache

**id:** ai-kv-cache

**question:** What is the transformer KV cache and why is it useful during autoregressive generation?

**answer:** It stores attention keys (K) and values (V) for previous tokens in each transformer layer, so generating the next token does not repeatedly recompute those prior tokens' K/V projections.

**mechanism:**
- At each layer, attention derives Q (query), K (key), V (value) representations.
- The new token's Q attends to cached K/V for earlier tokens plus its own K/V.
- Append the new token's K/V to the cache for future decode steps.
- Prefill processes the input prompt and populates the cache; decode then generates tokens incrementally.

**tradeoffs:** Speeds up autoregressive decoding but uses GPU/accelerator memory that grows with context length and concurrent sequences. Techniques such as grouped-query attention, quantized KV caches and paged attention reduce memory pressure.

**caveat:** The cache is *intermediate attention state*, not a database of facts or a cache of generated text. Its name comes from attention's key/value tensors. Exact video-specific claims were not verified.

**example:** With a 1,000-token prompt, token 1,001 can attend to cached prompt K/V rather than reprocessing all prior tokens just to rebuild their projections.

**related:** ai-prefill-decode

**source:** https://huggingface.co/docs/transformers/main/cache_explanation

---

AI.LLMInference

## Prefill vs Decode

**id:** ai-prefill-decode

**question:** What is the difference between prefill and decode in LLM inference?

**answer:** Prefill processes the supplied prompt and builds initial KV state, often with high parallelism. Decode generates subsequent tokens step by step, using the KV cache from previous steps.

**mechanism:**
- Prefill cost rises with prompt length.
- Decode is inherently sequential across generated tokens and often constrained by memory bandwidth/cache size.
- Time-to-first-token and inter-token latency describe different phases of perceived speed.

**related:** ai-kv-cache

**source:** https://huggingface.co/docs/transformers/main/cache_explanation

---
