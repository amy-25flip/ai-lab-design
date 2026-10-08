# Methodology

How model and defense choices are decided: by measurement, with predictions
committed before results are seen.

## 1. Model tournament

A custom task suite, weighted to match real use rather than a generic
benchmark. Example category weighting:

| Category | Weight |
|---|---|
| Coding | 30% |
| Reasoning | 20% |
| Firmware / security analysis | 15% |
| Structured output | 10% |
| Tool use | 10% |
| Context retrieval | 10% |
| Instruction following | 5% |

### Controls that keep it honest

- **Common context ceiling for the main comparison.** When models support
  different maximum context lengths, the head-to-head quality score is run at a
  *shared* ceiling so the result measures model quality, not context budget.
  Each model's higher capacity is reported separately as a secondary finding.
- **Hold quantization constant where possible**, and note it as a confound
  where not — comparing models at different quantization levels measures two
  things at once.
- **Deterministic scoring where the answer is checkable** (exact / regex /
  execution). Coding is scored by running candidate code in the sandbox, not by
  string match. Open-ended security analysis uses a rubric or model-graded
  score and is flagged as such.
- **Repeats with intent.** Same-seed repeats test determinism; different-seed
  repeats estimate variance. The write-up states which is being measured.
- **Thermal awareness.** On fanless hardware, the model tested last runs on a
  warmer chip. Let hardware cool between runs, or log thermal state at each
  measurement so a "slow" result can be distinguished from throttling.
- **Low-weight categories are high-variance** when they contain few tasks; their
  sub-scores are reported as indicative, not definitive.

## 2. Pre-registration

Before running anything, write down 3–4 concrete, falsifiable predictions
(e.g. "the smaller newer-generation model will match the larger older one on
reasoning but lose on multi-file coding"). After the run, report which held and
which didn't. Being wrong and saying so is stronger evidence of rigor than only
reporting favorable results.

## 3. Capability-vs-safety as a real question

Rather than ranking models on a leaderboard, test a hypothesis: does removing a
model's refusal behavior cost measurable capability, and does it increase
*unsafe compliance under adversarial input*? This links evaluation directly to
security: compare a refusal-removed model against its standard counterpart on
both capability tasks and on how readily each executes a malicious instruction
smuggled into tool output.

## 4. Security-defense evaluation

The agent platform is attacked with a corpus of injection and malformed-input
payloads spanning categories such as path traversal, argument injection,
unauthorized tool chaining, and instruction override via tool output. The
corpus is replayed against the tools **before and after** a defense layer, and
the result is reported as:

- attack-success rate (before vs. after),
- false-positive rate on legitimate tasks (the defense must not break normal
  use),
- latency cost of the defense.

Where possible the corpus is enriched with **real** adversarial input captured
from a honeypot, so the evaluation uses in-the-wild payloads, not only
synthetic ones.

## 5. Reproducibility

- Pin every dependency version; record the runtime/build identifiers of each
  model served.
- Version the benchmark data and the task suite.
- Write the harness so a third party could rerun it against a different model.
- Present results in a short paper shape: hypothesis, method, results,
  limitations.
