# Aman Carbon-AI — Data dictionary

Definitions used throughout `Aman_Carbon.ipynb`. Unless stated otherwise, features use only issuance and retirement records dated on or before **31 December 2024** (the leakage cutoff). For the rice scheme the cutoff is **30 June 2024**, before Verra's rejection.

## 1. Status ontology

Every raw registry status maps to one of three tiers.

| Tier | Raw statuses |
|---|---|
| `normal` | Listed; Registered; Completed; Under development; Under validation / under_validation; validated; Transitioned; Gold Standard Certified Design; Gold Standard Certified Project; Transfer to Another GHG Program; Units Transferred from Approved GHG Program; Requantification Requested; Registration requested; Verification approval requested; Registration and verification approval requested; Crediting Period Renewal Requested; Crediting Period Renewal and Verification Approval Requested |
| `watch` | On Hold - see notification letter; Late to verify |
| `risk_flag` | Canceled / cancelled; Withdrawn; Inactive; Rejected by Administrator; Registration request denied; Registration and verification approval request denied; Verification approval request denied; Crediting Period Renewal Request Denied; Crediting Period Renewal and Verification Approval Request Denied; validation_unsuccessful |

Snapshots: December 2024, December 2025, February 2026 (raw registry files) and April 2026 (harmonized file).

**Candidate pool.** A project enters the pool when two consecutive *observed* snapshots move from `normal` or `watch` into `risk_flag`. A missing snapshot is skipped; projects observed only once cannot enter. A project enters once, at its first qualifying transition, and stays in the pool if its status later reverts.

## 2. Project-level features

| Feature | Definition |
|---|---|
| `n_batches` | Number of issuance batches |
| `share_retired` | Credits retired or cancelled by the cutoff ÷ credits issued (credit-weighted) |
| `mean_issuance_lag_days` | Mean over batches of (issuance date − vintage end date), in days |
| `batch_size_cv` | Standard deviation ÷ mean of batch sizes |
| `issued` | Total credits issued by the cutoff |
| `ccr` (claim-consistency ratio) | Issued ÷ (E × Y). **E** is the registry-reported estimated annual emission reduction (projects with missing or non-positive E are excluded). **Y** is the length in years of the *union* of the batches' vintage periods (overlapping or split batches count once), with a minimum of one month. A value below 1 means fewer credits were issued than the project itself estimated for the periods covered. |

The four features `n_batches`, `share_retired`, `mean_issuance_lag_days` and `batch_size_cv` were pre-specified. `ccr` was defined after they had been tested and is reported as post hoc.

**Within-methodology percentile.** For a flagged project, the share of never-flagged projects of the same methodology with a lower `ccr` (ties count one half). Only methodologies with at least five never-flagged projects are used.

## 3. Labels

| Label | Definition |
|---|---|
| Confirmed (23 with issuance) | 19 C-Quest Capital projects with compensated over-issued credits in Verra's October 2024 review, and 4 Guizhou Baiheng projects rejected in December 2025. VCS2380 is kept only as a sensitivity check (it has no issuance and is not named in those findings). |
| Third scheme (rice) | The 25 Chinese rice-cultivation projects with issued credits among the 37 Verra rejected in August 2024; external test only. |
| Under review (45) | The 35 China projects put on hold for quality-control review and the 10 withdrawn projects reviewed in December 2025. Out-of-sample check only; never a positive label. |
| Candidate, unverified | Other pool projects. Never used as positive labels. |
| Eligible control | Verra project currently Registered or Completed, never in `watch` or `risk_flag`, not sharing a proponent with a confirmed case (the generic "Multiple Proponents" is ignored), and not linked to C-Quest, Bridge Carbon or Guizhou Baiheng. |

A registry rejection or withdrawal is not treated as a fraud finding.

## 4. Matching

| Design | Rule |
|---|---|
| Type and size | Exact on project type; nearest neighbour on log₁₀ credits within 0.5 pooled SD; up to five controls; no replacement; largest cases first |
| Methodology, vintage and size (revision) | Exact on methodology (falling back to project type when no same-methodology control is within the caliper); first vintage year within ±1 year; nearest log₁₀ credits within the same caliper; up to five controls; no replacement |

## 5. Decision protocol (arbiter)

Four modules return evidence items (direction, strength, reason): Registry, Transaction, Claim-Consistency and On-Chain (informational only). Rules apply in order:

| Tier | Rule | Recommended action |
|---|---|---|
| Insufficient evidence | No current status and no risk signal | Data request or manual verification |
| Red | Latest status is an adverse registry decision (rejection, denial, cancellation or failed validation), or a risk status entered during the study window | Committee review and evidence request |
| Amber | Any risk signal with strength ≥ 0.5, or two modules each reporting a risk signal ≥ 0.4 | Enhanced due diligence |
| Green | All other projects | Routine monitoring (not an approval) |

Signal strengths, fixed in advance:

| Signal | Strength |
|---|---|
| Adverse registry decision | 1.0 |
| Other risk status | 0.8 |
| Transition into risk | 0.6 |
| Current watch status | 0.5 |
| Past watch status | 0.3 |
| Transaction-model score in the top 5% of the cohort | 0.5 |
| Issued-to-claimed ratio ≥ 2.0 | 0.7 |
| Issued-to-claimed ratio 1.2 to 2.0 | 0.4 |

Every Red, Amber or contradictory decision goes to human review; nothing is rejected automatically. Each decision carries reason codes, missing-evidence notes and a confidence level. A simple point score (40 for current risk status, 20 for a transition into risk, 15 for fast issuance, cap 100) is kept for comparison only.

## 6. Statistics

Mann-Whitney U tests with rank-biserial effect sizes (r = 2U ÷ (n₁n₂) − 1; negative means lower in flagged projects); 2,000-resample bootstrap intervals; Benjamini-Hochberg false-discovery-rate correction; Wilson intervals for proportions. AUC intervals resample whole matched sets. Random Forests use 300 trees, minimum leaf size 2, no tuning; their scores are used only for ranking. All machine-learning results are exploratory. Seed 42.
