# Aman Carbon-AI

**Explainable Modular Multi-Source Screening for Sustainable and Inclusive Carbon Finance**

Aman Carbon-AI is an open, reproducible prototype that screens carbon-credit projects for integrity risk using only public data: registry status histories, issuance and retirement records, and each project's own registry-stated annual emission reductions. It returns reason-coded signals for human review. It is **not** a fraud classifier: a flag is a request for evidence, not a finding of fraud.

The empirical validation is Verra-centred. Performance on other registries and in operational settings has not been tested.

## What the analysis shows

- **Registry status misses many integrity actions.** Status transitions recover 11 of the 23 Verra-confirmed over-issuance projects (48%, Wilson 95% CI 29–67%). The other 12 were corrected while staying *Registered*. Of 35 China projects Verra put on hold for review in December 2025, 31 were in a normal state a year earlier.
- **The claim-consistency ratio mainly marks high-risk methodologies.** Confirmed projects issue far less than they claimed, but so do ordinary projects using the same methodologies. Within a methodology there is a modest, exploratory signal (flagged projects at a median 37th percentile across 47 projects from 25 developers).
- **Negative results are reported.** The issuance-lag difference disappears after vintage matching; trained models do no better than simple rules; watch status does not predict risk; and the signals do not transfer to a third scheme (Chinese rice cultivation).

## Repository contents

| File | Purpose |
|---|---|
| `Aman_Carbon.ipynb` | The full analysis, from raw data to every table and figure, in 14 sections |
| `DATA_DICTIONARY.md` | Feature formulas, the status ontology, labels and the decision (arbiter) rules |
| `input_file_hashes.csv` | SHA-256 hashes of the four input files used (written by the notebook, Section 9) |
| `requirements_used.txt` | Package versions of the run that produced the reported results (written by the last cell) |
| `LICENSE` | MIT licence |

## Data

All inputs are public. They come from the Berkeley Carbon Trading Project's
[Voluntary Registry Offsets Database](https://gspp.berkeley.edu/berkeley-carbon-trading-project/offsets-database) (VROD),
released under the [Creative Commons Attribution 4.0 (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) licence.
Because the analysis uses specific past versions, an archived copy of the exact files is deposited with the release of this
repository on Zenodo (see the DOI badge, once published). They can also be downloaded from the VROD page:

| File | Version |
|---|---|
| `VROD-registry-files--2024-12-year-end.xlsx` | Raw registry files, December 2024 year-end |
| `VROD-registry-files--2025-12.xlsx` | Raw registry files, December 2025 |
| `VROD-registry-files--2026-02.xlsx` | Raw registry files, February 2026 |
| `Voluntary-Registry-Offsets-Database--v2026-04.xlsx` | Harmonized database, April 2026 |

Check your copies against `input_file_hashes.csv`; a different file version will change the results.

**Data citation (CC BY 4.0 attribution).** Berkeley Carbon Trading Project. *Voluntary Registry Offsets Database*, raw registry files
(December 2024, December 2025, February 2026) and harmonized database v2026-04. University of California, Berkeley.
Retrieved from https://gspp.berkeley.edu/berkeley-carbon-trading-project/offsets-database. The files are redistributed unchanged.

Confirmed-case labels come from Verra's published findings (C-Quest Capital over-issuance review, October 2024; China project rejections and reviews, December 2025; rice-cultivation rejections, August 2024, and compensation update, January 2025). The project lists are written into the notebook.

## How to run

1. Open `Aman_Carbon.ipynb` in Google Colab (or Jupyter with Python 3.10+).
2. Upload the four data files to `/content` (or the working directory).
3. Run all cells from top to bottom in one session (**Runtime → Run all**). Most of the run time is bootstrap resampling.
4. The last cell zips every output table and figure into `aman_carbon_ai_outputs.zip`.

Random seed: 42 throughout. The optional on-chain lookup reads an Etherscan key from the `ETHERSCAN_API_KEY` environment variable; no key is stored in the notebook, and the rest of the analysis runs without it.

## Status

Research prototype accompanying a manuscript under review. All machine-learning results are exploratory. Do not use the scores to reject or approve credits automatically.

## Licence

MIT; see `LICENSE`.
