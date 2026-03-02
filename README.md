# Deforestation Logistic: Classifying Countries at Critical Forest Loss Risk

## 📖 Purpose of Study
Can socioeconomic and demographic data alone identify which countries are actively destroying their forests?

This project transitions from the continuous regression approach developed in the previous phase into a **binary classification problem**, using Logistic Regression to predict whether a country is at *critical deforestation risk* or not. The central challenge is not just building a model; it is justifying the construction of a meaningful target variable from a continuous rate, handling a naturally imbalanced class distribution, and choosing an evaluation framework that reflects real ecological stakes. This repo demonstrates the full classification workflow: from defining the problem with scientific grounding, to cross-validating rigorously, to interpreting what the model actually learned about the world.

## 📊 The Data
The dataset (`simplified_df.csv`) is a pre-processed combination of environmental and socioeconomic country-level records (n = 103 countries after filtering non-forested nations), built and cleaned during the linear regression phase of this project series. The target variable `Deforestation_Critical` is derived from the annual deforestation rate.

**Target Variable (constructed):**
* `Deforestation_Critical`: Binary classification label — `1` (High Risk: annual deforestation rate > 0.501% of forested area) or `0` (Low Risk). Threshold sourced from Teo et al., PNAS 2024 [1]. Approximately 70% Class 0 / 30% Class 1.

**Predictor Features (selected):**
* `Agricultural Land (%)`: Share of total land under agricultural use — proxy for land conversion pressure.
* `Birth Rate`: Crude birth rate per 1,000 people — reflects demographic pressure on natural resources.
* `Co2-Emissions`: Annual CO₂ output — correlated with extractive and industrial activity.
* `GDP`: Total economic output — higher-income nations tend to invest in conservation.
* `Life Expectancy`: Average lifespan — proxy for institutional quality and human development.
* `Gross Tertiary Education Enrollment (%)`: University enrollment rate — reflects educated, conservation-aware populations.
* `Infant Mortality`: Deaths per 1,000 live births — development indicator inversely correlated with environmental governance.
* `Physicians per Thousand`: Healthcare access — broader proxy for state institutional capacity.
* `Urban Population`: Share of population in urban areas — urbanization patterns affect land-use pressure.
* `CPI`, `CPI Change (%)`, `Tax Revenue (%)`, `Unemployment Rate`, and others: Macroeconomic context variables.
* `Latitude / Longitude`: Geographic position, captures tropical belt effects not explained by economics alone.
* (the full list is and can be found in the notebook)

## 🛠 Main Conceptual Applications
The core purpose of this notebook is to rigorously apply classification and model evaluation concepts to a real-world ecological problem. The key technical applications shown are:

1. **Target Variable Engineering**: Constructing a binary class from a continuous variable using a literature-validated ecological threshold, with full justification for choosing it over statistical alternatives (median, mean, 75th percentile).
2. **Class Imbalance Handling**: Using `class_weight='balanced'` in the Logistic Regression to counter the natural 70/30 split without resampling.
3. **Leakage-Free Evaluation via Pipeline**: Wrapping `StandardScaler` and `LogisticRegression` into a `make_pipeline()` so the scaler is re-fitted inside each CV fold — not pre-applied to the entire training set.
4. **Stratified K-Fold Cross-Validation** (k=5): Estimating model performance exclusively on training data, preserving class proportions in every fold.
5. **Decision Threshold Analysis**: Simulating model behavior at thresholds of 0.3, 0.4, and 0.6 using out-of-fold CV probabilities to find the ecologically optimal cutoff.
6. **Diagnostic Metrics**:
   - Recall, Precision, F1-Score: Evaluated for the minority class (High Risk).
   - Confusion Matrix: Visualized as heatmaps for both CV and test stages.
   - ROC Curve & AUC: Threshold-independent measure of discriminative quality.
7. **Coefficient Interpretation**: Extracting and visualizing log-odds coefficients from the fitted pipeline to understand feature direction and relative importance.

## 🚀 Key Findings
The full analysis, decisions, and narrative can be found in the notebook. The main findings are:

- **A literature threshold outperforms a statistical one.** Using the ecologically validated 0.501% cutoff — rather than the median — produces a target variable with real-world meaning, even at the cost of class imbalance.
- **The 0.3 probability threshold is the right operational choice.** Lowering the classification boundary from the default 0.5 to 0.3 pushed Class 1 Recall from 0.68 to 0.84, catching dramatically more deforesting nations at a justified precision trade-off. In environmental monitoring, a false alarm costs far less than a missed crisis.
- **Development indicators are the strongest protective signal.** Features like GDP, Life Expectancy, and Tertiary Education Enrollment received the most negative coefficients — the model learned that institutional capacity and human development correlate strongly with lower deforestation risk, consistent with ecological literature.
- **Agricultural pressure and demographic growth are the main risk drivers.** Agricultural Land (%) and Birth Rate carried the largest positive coefficients, aligning with well-documented drivers of tropical forest loss.
- **The model generalizes well.** The AUC of 0.833 on the independent test set — consistent with the CV estimate — indicates the model's discriminative power translates faithfully to unseen countries, not just the training sample.

## Project Files
- [LogisticRegression.ipynb](./LogisticRegression.ipynb): The full classification analysis, cross-validation, threshold study, and coefficient interpretation.
- [LogisicRegression.html](./LogisticRegression.html): The html readable version of the notebook.
- [Simplified_df.csv](./simplified_df.csv): The dataset cleaned and ready from the past deforestation project. 

## References
[1] Teo, H. C., Sarira, T. V., Tan, A. R. P., Cheng, Y., & Koh, L. P. (2024). Charting the future of high forest low deforestation jurisdictions. *Proceedings of the National Academy of Sciences*, 121(37), e2306496121. https://doi.org/10.1073/pnas.2306496121
