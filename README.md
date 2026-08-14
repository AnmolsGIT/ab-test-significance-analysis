# A/B Test Significance Analysis — Ad Campaign vs. PSA Control

**Decision: Ship it.** Users exposed to the ad converted at 2.55% vs. 1.79% for the PSA control group — a statistically significant +43% relative lift (p < 0.0000000001), backed by a tight, entirely-positive 95% confidence interval and 100% statistical power.

📄 **[Read the one-page executive memo →](./memo/AB_Test_Executive_Memo.docx)**
📓 **[See the full analysis notebook →](./notebooks/ab_test_analysis.ipynb)**

---

## Problem

Did the tested marketing change (showing users an ad vs. a placebo PSA) actually move conversion — or could the observed difference be explained by chance? The goal was to answer this rigorously enough to make a ship / don't-ship call, not just report a p-value.

**Hypotheses:**
- **H0:** No difference in conversion rate between the ad-exposed (treatment) group and the PSA (control) group.
- **H1:** The ad-exposed group converts at a different rate than the PSA group.
- **α = 0.05**

## Approach

1. **Sanity-checked the data** — confirmed no duplicate users and reviewed the group split (96% ad / 4% psa — imbalanced but not a randomization bug, common in real ad tests that hold out a small control).
2. **Computed conversion rates and lift** per group, then ran a **two-proportion z-test** (not a t-test — conversion is a binary outcome, so this is a proportions comparison, not a means comparison).
3. **Calculated a 95% confidence interval** for the lift, since a p-value alone doesn't convey effect size.
4. **Ran a retrospective power analysis** to check whether the sample size was actually sufficient to detect an effect this size (it was — the test was over-powered).
5. **Segmented the result** by day-of-week and time-of-day to check the effect wasn't being driven by a single subgroup (a Simpson's paradox check).
6. **Wrote the recommendation as a business memo** — decision, confidence, risk, and next steps — rather than stopping at the stats output.

## Findings

| Metric | Control (PSA) | Treatment (Ad) |
|---|---|---|
| Sample size (n) | 23,524 | 564,577 |
| Conversion rate | 1.79% | 2.55% |

- **Absolute lift:** +0.77 percentage points · **Relative lift:** +43.1%
- **Z-statistic:** 7.37 · **p-value:** < 0.0000000001
- **95% CI (relative lift):** [33.3%, 52.8%] — entirely positive, tight range
- **Achieved power:** 100% (only ~2,910 control users were actually needed for 80% power — 23,524 were used, meaning future tests in this program can run with a smaller control allocation)
- **Segmentation:** the lift holds in the same direction across every day of the week and every time-of-day bucket tested — no subgroup reversal. Strongest on Tuesdays and during morning/night hours; more modest (but still positive and mostly significant) on Sundays, Thursdays, and afternoons/evenings.

**Risk if wrong:** Low. The main risk isn't that the ad doesn't work — it's overstating the lift if future traffic shifts toward the weaker-performing segments. Recommend monitoring conversion by day/time post-launch.

**What to test next:** ad creative variants (now that "ad vs. no ad" is settled), a smaller control allocation in future tests, and why morning/night and Tuesday show the strongest lift (to inform scheduling/budget allocation).

## Tech Stack

- **Python** — pandas, NumPy
- **SciPy / statsmodels** — `proportions_ztest`, power analysis (`NormalIndPower`, `proportion_effectsize`)
- **Jupyter Notebook** — full workflow and narrative
- **Matplotlib / Seaborn** — supporting visualizations

## How to Reproduce

1. Download the [Marketing A/B Testing dataset](https://www.kaggle.com/datasets/faviovaz/marketing-ab-testing) from Kaggle and place it in `data/`.
2. Install dependencies:
   ```bash
   pip install pandas numpy scipy statsmodels matplotlib seaborn jupyter
   ```
3. Open and run `notebooks/ab_test_analysis.ipynb` top to bottom.
4. The executive memo (`memo/AB_Test_Executive_Memo.docx`) summarizes the notebook's conclusions in business language.

## Repo Structure

```
ab-test-significance-analysis/
├── data/                              # dataset (not committed — see step 1 above)
├── notebooks/
│   └── ab_test_analysis.ipynb         # full statistical workflow
├── memo/
│   └── AB_Test_Executive_Memo.docx    # one-page ship/no-ship recommendation
└── README.md
```