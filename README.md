[![arXiv](https://img.shields.io/badge/arXiv-2401.12345-b31b1b.svg)](https://arxiv.org/abs/2610.30297)

# Replication Appendices — Refugee Law Lab Bill C-12 Study

**Author**: Sean Rehaag

**Date of Last Update**: October 22, 2026

**Link to Working Paper**: [SSRN](https://ssrn.com/abstract=7650958) or [arXiv](https://arxiv.org/abs/2610.30297)

Bill C-12, the ['Strengthening Canada's Immigration System and Borders Act'](https://www.parl.ca/legisinfo/en/bill/45-1/c-12), 
makes refugee claims ineligible for referral to the Immigration and Refugee Board
when they are made more than one year after the claimant's first entry to Canada, diverting
them to the Pre-Removal Risk Assessment process. Advocates worry that claims involving
gender-based or SOGIE-based (sexual orientation, gender identity and expression)
persecution disproportionately arise after time in Canada, so that well-founded claims of these
types would be disproportionately barred. This study tests that concern empirically by categorizing
roughly 30,000 published Canadian refugee decisions (2006–2025) with a large language model.

Five notebooks walk through the study end to end, written as appendices to
a paper on Bill C-12, for readers who want to see (or re-run) exactly what
was done. The only thing not included in this repo is the decision texts themselves, which are
public in the A2AJ [`canadian-case-law`](https://huggingface.co/datasets/a2aj/canadian-case-law)
dataset on Hugging Face (pinned to one frozen revision in every notebook); the notebooks download
them when needed.

**Requirements**: Python 3.12 with the pinned packages in
[`requirements.txt`](requirements.txt) (`pip install -r requirements.txt`). Run each notebook
from the repository root, top to bottom. All notebooks are committed with their outputs, so
they can also be read like a report without running anything. Notebooks 02–05 run from the
committed `data/` files without network access; notebook 01 downloads the pinned corpus.

## The notebooks (in pipeline order)

| Notebook | What it does | Re-runnable as-is? |
|---|---|---|
| [`01_golden_samples.ipynb`](01_golden_samples.ipynb) | Reproduces the 600-case random sample exactly (pinned dataset revision, seed 12), verifies it against the committed reference file, and documents how the reference labels of the benchmark sample were created. | Yes (downloads the corpus on first run). |
| [`02_model_validation.ipynb`](02_model_validation.ipynb) | The complete production prompt, the code that ran the production model (Qwen3.5) on the 600 benchmark cases, the evaluation against the benchmark labels (paper Table 2), and breakdowns of the timing and basis disagreements by direction. | Evaluation: yes. Model calls: guarded by `RUN_MODEL = False` (needs a local model server and the case texts from notebook 01). |
| [`03_production_run.ipynb`](03_production_run.ipynb) | The full-corpus production run: worklist definition, run loop, status of the completed run (32,462 decisions, 2006–2025 — including the August 2026 FC pre-filter widening), the run facts printed from the committed run metadata (runs, retry ladder, gate pass rate under each pre-filter), and a run-to-run consistency check on the benchmark cases coded twice. | Status cell: yes. Model calls: guarded (`RUN_MODEL = False`; the full run takes ~17 h on a local GPU server). |
| [`04_human_review.ipynb`](04_human_review.ipynb) | The human reasonableness review, completed 13–14 August 2026: the deterministic 100-case random sample and its labelling instrument, the analysis of the completed review (with exact binomial intervals), a supplementary targeted review of 25 late-claim codes, and both reviews side by side (paper Table 3). | Yes (reads the committed review exports; rebuilding the labelling instruments is guarded by `BUILD_REVIEW_FILE = False`). |
| [`05_findings.ipynb`](05_findings.ipynb) | The findings: the corpus (paper Table 1), distributions and the share of late claims, the timing × gender/SOGIE association overall, per source, and for gender and SOGIE separately (Table 4), success and error-catching rates for late claims by level, timing, and basis, with the RPD and RLLR reported separately (Table 5), supplementary analyses S1–S8 (RLLR known-answer check, the association read the other way, components within sources, tests of late-vs-timely differences, robustness to unclear basis and unclear timing, and the figures cited from Appendices 2 and 4), and interpretation caveats. | Yes. |

## The data (`data/`)

| File | What it is |
|---|---|
| `golden_ai_generated_sample.json` | The benchmark sample: the 600 cases (citation, date, dataset row) with their timing/outcome/basis (and FC-gate) codes and a one-sentence evidence quote per code. Notebook 01 rebuilds the sample from scratch and verifies it against this file. |
| `example_golden_label.json` | One example of the per-case label files the coding agents wrote (codes plus a one-sentence evidence quote per code). |
| `golden_sample_coding_rubric.md` | The written coding rubric the reference coders applied. |
| `golden_predictions_qwen.json` | The production model's codes for the 600 reference cases (evaluated in notebook 02; also read by notebooks 03–05). |
| `production_codes.json` | The completed full-corpus run: one record per decision (uid, source, citation, date, four codes) for all 32,462 decisions (notebook 03). |
| `production_run_meta.json` | Run bookkeeping for the same 32,462 decisions, distilled from the per-decision output files of the research archive: which run coded each decision (main, 22–23 July 2026, or supplementary, 12 August 2026), which rung of the retry ladder answered, whether thinking was disabled, the `finish_reason`, and the one truncation flag; plus the model identifier and server version. Read by notebook 03. |
| `reasonableness_review.json` | The completed human reasonableness review (100 cases, four answers each, reviewer comments): the labelling-software export with the embedded decision texts and the reviewer's email address removed; nothing else is altered. |
| `reasonableness_review_late25.json` | The completed supplementary review of 25 late-claim codes (same format, same stripping). Kept separate because it conditions on the model coding a claim late — a targeted precision check, not a corpus-representative sample. |

**AI Disclosure**: Generative AI tools were used in preparing these appendices, including for background research, data extraction, coding, drafting and editing (Claude Code, using Fable 5 and 5.5 and Opus 5 and 5.5).

**Funding**: This research was supported by the Social Sciences and Humanities Research Council of Canada, the Law Foundation of Ontario, the Canada Foundation for Innovation, and the Ontario Research Fund.
