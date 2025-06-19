
# Auto Insurance Pricing Model (GLM-Based Frequency & Severity)

This project was built as part of my **actuarial data science portfolio**, showcasing my ability to recreate a complete technical pricing pipeline using Python. Having previously worked on GLMs in the insurance sector, this project represents a modern, reproducible, and well-documented implementation of that experience.

My goal was not only to apply **generalized linear models (GLMs)** for claim frequency and severity but also to challenge myself with a complete, end-to-end actuarial pricing process. I aimed to gain *provable hands-on experience in Python* while replicating logic akin to **Emblem & Radar**-style modeling workflows.

---

## Project Goals

- **Replicate an industry-like pricing process** from raw data through to segmentation and final pricing.
- Develop separate GLMs for **claim frequency** and **claim severity**, and compute the **pure premium**.
- Focus on **interpretability, transparency, and structure**, mirroring actuarial best practices.
- Explored **regularization, zero-inflated** and **discrete distribution models** earlier in the project for comparison purposes. These were excluded from the final version to prioritize interpretability, runtime efficiency, and reproducibility.
- Build a modular and scalable framework across multiple notebooks for real-world use cases.

---

## Dataset Overview

I used the `freMTPL2freq.csv` and `freMTPL2sev.csv` datasets from Kaggle (French Motor Third Party Liability data). These represent:

- **Frequency file**: One row per policy, includes features and number of claims.
- **Severity file**: One row per claim, includes claim amounts for each claim registered.

These datasets were cleaned, filtered, merged, and engineered into:

- `df_freq_sev_v2.csv`: Final dataset for frequency modeling.
- `df_freq_sev_v3.csv`: Final dataset for severity modeling.
- `pricing_df.csv`: Merged dataset with predicted frequency, severity, and final pure premium.

---

## Modeling Overview

### 1. `data_cleaning.ipynb` – Preprocessing, EDA & Feature Engineering

- Loaded, explored, and validated raw frequency/severity datasets.
- Corrected datatypes (e.g., object to categorical).
- Handled outliers (e.g., capped `VehAge`, `DrivAge`, `Exposure`).
- Performed exploratory analysis (e.g., univariate distributions, variable profiling).
- Created `ClaimFrequency`, `ClaimAmount` aggregates, and key flags.
- Binned categorical and numeric variables using domain knowledge and grouped risk logic (e.g., `VehPower_bin`, `BonusMalus_bin`).
- Feature engineering was **customized for frequency and severity modeling**, tailored to each objective (e.g., via bivariate analysis).

### 2. `freq_model.ipynb` – Frequency Modeling (Claim Count)

- Modeled using **GLM Poisson** and **GLM Negative Binomial**.
- Evaluated both with and without **polynomial terms** for `VehAge_log` and `DrivAge_cap`.
- ZIP, ZINB, and Discrete Negative Binomial models were tested during development but removed from the final implementation to improve runtime and simplify the pipeline.
- Selected the **GLM Negative Binomial** as the best-performing model, based on AIC, BIC, and Pearson chi-squared statistics.
- Used an 80/20 train-test split for validation.
- Refitted on full dataset; predictions stored as `freq_pred`.

### 3. `sev_model.ipynb` – Severity Modeling (Claim Cost)

- Used only policies with `ClaimAmount > 0`.
- Modeled with **GLM Log-Normal** and **GLM Gamma** using `ClaimAmount` as target.
- Selected GLM Log-Normal with bias-corrected prediction as final model.
- Used an 80/20 train-test split for validation.
- Refitted on full dataset; predictions stored as `sev_pred`.

### 4. `pricing_estimation.ipynb` – Pure Premium & Segmentation

- Merged `freq_pred` and `sev_pred` into `pricing_df`.
- Computed **pure premium** as:
  $$
  \text{pure\_premium} = \text{freq\_pred} \times \text{sev\_pred}
  $$
- Added log-transformed version for better scaling: `log_pure_premium = log(1 + pure_premium)`.
- Benchmarked pure premium outputs across key variables (`BonusMalus_bin`, `VehPower_bin`, `Area_bin`) to validate discriminatory power and pricing logic.

---

## Key Insights

### Frequency Model

- **VehAge\_log** showed a strong negative effect on frequency (older vehicles = fewer claims).
- **BonusMalus\_bin** was the strongest discriminator, with `Extreme_150plus` increasing frequency by over 1.29 log points.
- Regional interactions further enhanced segmentation power and granularity.

### Severity Model

- Most coefficients were relatively **small**, reflecting the greater volatility and lower predictability of claim severity.
- `VehPower`, `BonusMalus`, and `VehBrand` still provided useful variation.
- GLM Log-Normal outperformed Gamma in both interpretability and fit.

### Pure Premium Segmentation

- **BonusMalus\_bin** segments showed clear differences in pure premium distribution (highly predictive).
- **VehPower\_bin** and **Area\_bin** offered moderate-to-low standalone segmentation but worked well in interaction.

---

## Reproducibility

To reproduce this project:

1. Clone the repository and navigate to the root directory.
2. Open the notebooks in order:
   - `1. data_cleaning.ipynb`
   - `2. freq_model.ipynb`
   - `3. sev_model.ipynb`
   - `4. pricing_estimation.ipynb`
3. Ensure the following CSVs are present in `/data`:
   - `freMTPL2freq.csv`
   - `freMTPL2sev.csv`
4. Execute each notebook in order to replicate the full pipeline and visual outputs.
(Note: Intermediate files like df_freq_sev_v1/v2/v3.csv are automatically generated during notebook execution.)

---

## About Me

I’m an **analytics enthusiast** with 5 years of experience across the **insurance and banking sectors**, and a **Bachelor of Science in Actuarial Science** from the University of Essex. In 2022, I became a certified Data Scientist and have since focused on advancing my Python expertise and actuarial modeling skills.

This project reflects:

- My understanding of **GLMs and pricing strategy**.
- My technical rigor in **data cleaning**, **EDA**, and **segmentation**.
- My ability to structure end-to-end solutions that are **replicable and readable**.

I’m looking to contribute to **data-driven, socially meaningful actuarial or analytics teams**. Let’s connect!

---

## Contact

If you’re interested in discussing this project, potential collaboration, or hiring me, feel free to reach out via:

- [Email](mailto:pamelareintsch.pr@gmail.com)
- [LinkedIn](https://www.linkedin.com/in/analyticspamela/)

