# Evidence Verification

## What reviewers can inspect

Download and extract [measurement-records.zip](measurement-records.zip). The package contains recorded data and documentation, with no executable/source code.

| Directory | Contents |
|---|---|
| `data/` | Original synthetic records, declared event fields, 16 questions and recorded environment |
| `evaluation/` | Expected answer terms and source IDs |
| `evidence/raw/` | Primary 240 requests/results, usage, timing, summary, expected metrics and review labels |
| `evidence/functional.json` | Recorded basic functional checks |
| `evidence/demo/` | Display receipts and actual verification stdout for the two support demonstrations |
| `figures/observations.csv` | Recorded observations for the plots |

The archive retains original request and result records. It does not include the engine, reproduction scripts, internal algorithms, model weights, private keys, databases or runtime configuration. The archive contains the primary experiment's 240 answer-call records. The two demonstration receipts are separate from the benchmark denominator.

## Numerical cross-check

1. Match each primary request to its result using the saved call index, question ID, configuration and repeat.
2. Check that there are 16 distinct questions and three repeats per configuration.
3. Average server-reported input tokens over the 48 calls in each configuration.
4. For each question, take the median of the three `ttft_seconds` and `completion_seconds` values; average the 16 medians and multiply by 1,000 for milliseconds.
5. Inspect `manual-review.json` alongside the question, original record, expected answer and actual model answer. Review labels are judgments that can be independently challenged.
6. Compare the resulting values with `expected-metrics.json` and [RESULTS.md](RESULTS.md).

A spreadsheet or an independently written script can perform the calculations. No Tenconic source access is required to check the published arithmetic.

## Verification boundary

These are operator-exported measurement records. They permit evidence inspection and independent numerical recalculation. They do not, on their own, establish an independent engine rerun or external certification. Executing Tenconic again requires access to the private engine; the public repository does not expose its implementation or provide an always-on inference endpoint.

The screenshots are unmodified captures of the actual browser UI. The terminal-style viewer streams stdout from the real local verification program. Its `SAVED API` rows refer to previously recorded API calls and are explicitly labeled accordingly.
