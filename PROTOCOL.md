# Measurement Protocol

## Workload and shared inputs

The experiment uses 24 synthetic customer-support event/document records and 16 distinct questions about current policies, historical policies, corrections, invalidated commitments and customer scope.

Every configuration receives the same original text, source IDs, speaker, recorded date and caller-declared event metadata: subject, attribute, value, operation and effective date. Customer, subject, attribute, effective-time and knowledge-time eligibility filters are shared. This trial measures memory use after explicit event registration.

## Configurations

| Configuration | Context supplied to the answer model |
|---|---|
| Tenconic state API | Requested state records returned by the actual private engine, selected for the declared query conditions |
| Chroma K=1 | Up to one most-similar eligible original record, with declared fields |
| Chroma K=4 | Up to four most-similar eligible original records, with declared fields |
| Chroma K=12 | Up to twelve most-similar eligible original records, with declared fields |
| Eligible originals | All original records passing the same eligibility filters |

**K is the maximum number of retrieved records**, not the model size or token count. One original record is one retrieval unit; fewer records are returned when fewer are eligible. Retrieved original records are provided in time order.

Chroma uses chromadb 1.5.9, a persistent local collection, cosine HNSW search and Qwen3-Embedding-0.6B Q8_0 CPU embeddings. Chroma is an established open-source retrieval/database project: [official repository](https://github.com/chroma-core/chroma) and [documentation](https://docs.trychroma.com/). The comparator is the implementation described here, not a vendor-wide product ranking.

## Shared answer runtime

| Setting | Value |
|---|---|
| Model | Qwen3.5-4B Q4_K_M |
| Runtime | llama.cpp b11074, CUDA 12.4 |
| Context window | 8,192 |
| Temperature / seed | 0 / 42 |
| Maximum output | 160 tokens |
| Thinking / prompt cache | Disabled / disabled |
| System instruction and output schema | Shared across configurations |
| Recorded cached input tokens | 0 |

Each question uses one answer-model call, without retries. Registration and the measured state-calculation path make no answer-model calls. Model startup, indexing and fixture preparation are outside query latency. Configuration order rotates across question/repeat combinations.

## Metric definitions

**Input tokens:** actual server-reported input usage, averaged over the 48 calls per configuration.

**Time to first answer content:** from the start of retrieval/state lookup to the first streamed answer content. This is not the first HTTP byte or first reasoning token.

**Time to completed answer:** from the same start point to the completed answer. It includes retrieval, communication, prompt processing and output generation. Actual output lengths are included: Tenconic averaged 36.25 output tokens; Chroma K=12 averaged 47.25. The completion-time reduction therefore includes output-generation effects.

**Timing aggregation:** take the median of three repeats for each of the 16 questions, then average those 16 medians. Repeats are not additional independent questions.

**Reviewed answer and source correctness:** review the entire answer for factual, temporal and source consistency. The stored labels were produced by the study's Codex analysis/review agents and lead reviewer; this was not a blind external assessment. Automatic literal-term checks are separately retained and are not substituted for whole-answer review.

## Separate functional checks

Both paths passed the eight recorded basic checks: user isolation, session persistence, original/source lookup, speaker preservation, date preservation, explicit update with history, selected-memory invalidation and actual process restart/restore. These checks are separate from answer accuracy and are documented in the measurement package.

## Scope

This is a focused raw-original policy-state experiment. It does not measure human integration time, subscription pricing or customer total monetary savings. Query latency and token savings are specific to this model, machine and workload. The original records, ambiguous fixture cases and review labels remain inspectable rather than being rewritten to fit the result.
