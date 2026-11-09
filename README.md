---
license: other
license_name: upstream-terms-preserved
license_link: LICENSE.md
task_categories:
- tabular-classification
configs:
- config_name: default
  data_files:
  - split: train
    path: extract/public/train.csv
  - split: validation
    path: extract/public/eval.csv
  - split: test
    path: extract/private/holdout.csv
---

# Will a consumer bank-account complaint be resolved with monetary relief? Predicting CFPB complaint dispositions at intake

Will a consumer complaint about a checking or savings account be closed with monetary relief by the company?

Derived from U.S. Consumer Financial Protection Bureau (CFPB). This is not its official product.

Prediction time: At date_received, when the complaint enters the CFPB and before it is sent to the company; only intake-form fields and calendar fields derived from date_received are used. Company response is a later disposition and is never an input.

Evaluation scope: `retrospective_research`. Public validation is development data. The downloadable labeled holdout is public; it is not cryptographically sealed. No final holdout benchmark score is claimed.

## Measured baseline

The existing runner was used unchanged on public validation. See `evaluation.json` for rows, controls, per-year results and limitations.

```json
{
  "full_model": {
    "auc": 0.7412404943853466,
    "brier": 0.12052050807755041
  },
  "majority_prior": {
    "auc": 0.5,
    "brier": 0.13404766161474882
  },
  "calendar_identity": {
    "auc": 0.5091543168210645,
    "brier": 0.1340587658722308
  }
}
```

## Limitations

- Published complaints are not a statistical sample of all consumer experiences; CFPB publishes only complaints sent to companies and only after the company responds or after 15 days, and it excludes complaints referred to other regulators.
- Company response is self-reported and not independently audited; 'monetary relief' may be recorded inconsistently across firms.
- The snapshot is current-vintage (2026-11-08); CFPB updates records, so this analysis does not establish what was operationally available to a 2019-2023 analyst in real time.
- Product, issue and sub-issue taxonomies changed in 2017 and 2023; historical rows preserve the wording selected at submission, so category drift is present, especially in the 2023 holdout.
- Consumers self-select into the complaint channel; complaint volume and company mix reflect market share and awareness rather than incidence.
- Company names are not entity-resolved; mergers and rebrands can split or merge the same institution across the time window.
- The binary target collapses 'Closed with explanation', 'Closed with non-monetary relief' and 'Untimely response' into 0, although these are substantively different outcomes.
- No consumer identifier is used; repeat complaints by the same consumer cannot be linked, so rows are treated as independent although some correlation may remain.
- Narratives, ZIP code, tags and all post-intake fields are excluded; no sensitive personal information or narrative text is used.
- Company response is self-reported and not independently audited; monetary relief may be recorded inconsistently across firms.
- The snapshot is current-vintage; CFPB updates records, so this analysis does not establish what was operationally available to a 2019-2023 analyst in real time, and training_label_timing is unverified.
- The binary target collapses 'Closed with explanation', 'Closed with non-monetary relief', and 'Untimely response' into 0 although these are substantively different outcomes.
- No full-row availability ledger or historical publication dates were supplied; timing observations are empty and period/lag fields are null rather than invented.
- No uncertainty interval; observed differences do not establish universal headroom

## Loading and reproduction

Verify all downloaded files with `verify_dataset.py`. CSV splits live under `extract/`; `meta.json` declares the target and identifiers. `MANIFEST.json` binds data, source snapshot, code and measured evidence.

To reconstruct without network source drift, extract `source-snapshot.tar.gz` into a new cache directory, install `reproduction/requirements.txt` in an isolated Python environment, then run `python reproduction/dataset_run.py --output NEW_EMPTY_DIRECTORY --source-cache CACHE_DIRECTORY`. Compare the qualification artifact digest with this manifest. This performs full validation and baseline evaluation and may need worker resources.

See `LICENSE.md` for source terms and preserved attribution.
