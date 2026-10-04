# Calibrated Markov Model of Antibiotic Resistance

An end-to-end antibiotic resistance project: surveillance data preparation, a two-state Markov model, calibration with a range of good fits, a comparison of seven prescribing strategies, and a cost and sensitivity analysis.

## Dataset

Built on the [WHO GLASS](https://www.who.int/data/gho/data/themes/topics/global-antimicrobial-resistance-surveillance-system-glass) time series of resistance to antibiotics (bloodstream infections, *Escherichia coli*, ciprofloxacin, 28 countries, 2018 to 2023). The analysis focuses on South Africa: about 4,000 tested samples a year, with resistance rising from 28.4% to 34.6%.

## Workflow

### 1. Data Preparation
- Loaded the country-level table from the GLASS download, which stores two tables in one file
- Selected South Africa as the only country starting below 30% resistance with a clear trend and large samples
- Computed yearly resistant and not-resistant shares, with a 95% margin of error for each year
- Flagged years with fewer than 100 tested samples (none were flagged)
### 2. Model Design
- Built a two-state Markov model (Not resistant / Resistant), since the data contain no Intermediate count
- Wrote functions to simulate any prescribing strategy year by year and to find the long-run resistant share
- Validated every function against known answers, including the exact two-state formula, before using real data
### 3. Calibration
- Fitted the yearly chances of becoming resistant (*a*) and reverting (*b*) by minimising squared error against observed shares
- Used `scipy.optimize.minimize` with bounds and 20 random starts
- Reported the full range of good fits (error within 10% of the best) instead of a single answer
### 4. Analyses
- Projected resistance from 2023 for every strategy, using the best fit and both ends of the good-fit range
- Calculated expected years to resistance and long-run resistant shares
- Built a sensitivity heatmap of resistance by treatment frequency and resistance rate
### 5. Strategy Comparison and Cost
- Compared seven strategies on the first year resistance reaches 40%, resistance in 2033, and cost per 1,000 patients over 10 years
- Tested how results change if resistance fades slowly when the drug is not used, and if resistant infections cost less

## Results

| Parameter | Best Fit | Range of Good Fits |
|---|---|---|
| *a* (chance of becoming resistant per year) | 0.0103 | 0.009 to 0.031 |
| *b* (chance of reverting per year) | 0.000 | 0.000 to 0.050 |
| Long-run resistant share | 100% | 38% to 100% |

The data clearly show resistance rising by about 0.75 percentage points a year, but six yearly points cannot pin down the long-run outcome.

| Strategy | Reaches 40% | Resistance in 2033 | Cost per 1,000 Patients (10 years) |
|---|---|---|---|
| Always use ciprofloxacin | 2032 | 41.0% | 8.14M |
| Rotate with a similar drug | 2038 | 38.5% | 7.88M |
| Combination product | Not within 50 years | 27.8% | 7.64M |
| Treat 75% of cases | Not within 50 years | 25.2% | 6.37M |
| Alternate years | Not within 50 years | 13.7% | 5.17M |
| Treat 50% of cases | Not within 50 years | 14.9% | 5.04M |
| Treat 25% of cases | Not within 50 years | 8.2% | 4.05M |

**Recommendation:** restrict ciprofloxacin to about a quarter of cases. It is the cheapest strategy in every scenario tested, while always using the drug is the worst. The ranking is robust, but the size of the benefit is not: if resistance fades slowly when the drug is not used, restricting to 25% only brings resistance to about 32% by 2033.

## Evaluation Measures

- Yearly resistant share with 95% margin of error
- Sum of squared errors between model and data
- First year resistance reaches the 40% threshold
- Expected years to resistance and long-run resistant share
- Cost per 1,000 patients over 10 years

## Limitations

- **Calibration, not observation:** the data are yearly national counts, not followed patients. The fitted transition chances are a calibrated assumption, not observed patient transitions.
- **Few data points:** six yearly values cannot separate *a* from *b*, so the long-run outcome (38% to 100%) is effectively unknown.
- **Model misfit:** the smooth Markov model cannot reproduce the sharp 2022 to 2023 rise; it overshoots 2021 and undershoots 2023.
- **Key assumption:** the benefit of restricting the drug depends mainly on how fast resistance fades when it is not used (assumed 20% a year, tested at 2%).
- **Assumed inputs:** the other drugs' transition chances are scaled from the calibrated drug, all costs are placeholders, and drug rotation ignores cross-resistance.

## Tools

Python · pandas · NumPy · SciPy · Matplotlib · Jupyter · Google Colab
