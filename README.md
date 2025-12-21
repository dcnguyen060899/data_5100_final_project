# Aircraft Fuel Efficiency Optimization
## A Statistical Analysis of Cruise Phase Operations for Commercial Aviation

**DATA 5100: Foundation of Data Science — Seattle University**  
**December 2025**

### Author
- Duy Nguyen

### Repository
[https://github.com/dcnguyen060899/data_5100_final_project](https://github.com/dcnguyen060899/data_5100_final_project)

---

## Project Summary

This project analyzes NASA DASHlink flight recorder data from 312 commercial flights (1.88 million measurements) to identify the primary drivers of fuel consumption during cruise operations. Using variance decomposition and sequential regression modeling, we quantify the relative importance of altitude, speed, engine performance, and meteorological conditions.

**Key Finding:** Engine fan speed accounts for **64.4%** of explainable variance in fuel consumption—approximately **2.2 times more** than altitude and speed combined (29.7%). This suggests airlines may achieve greater returns from engine health monitoring programs than from sophisticated flight planning optimization systems.

---

## Research Question

> Which operational parameters have the greatest impact on fuel consumption, and how should airlines prioritize improvement initiatives?

---

## Key Results

| Factor | % Explained Variance | Priority |
|--------|---------------------|----------|
| Engine Fan Speed (N1) | 64.4% | **1st — Engine Monitoring** |
| Altitude + Mach Number | 29.7% | 2nd — Flight Planning |
| Headwind + Weight | 5.8% | 3rd — Environmental |
| Interaction Terms | <0.1% | Negligible |

**Model Performance:** Adjusted R² = 0.959 (95.9% of variance explained)

---

## Business Implications

1. **Priority 1 — Engine Performance (64% variance)**
   - Real-time N1/EGT monitoring systems
   - Predictive maintenance scheduling
   - Optimal power band operational protocols

2. **Priority 2 — Flight Planning (29% variance)**
   - Simplified altitude-speed guidelines
   - Treat factors as independent (no complex conditional logic needed)

3. **Priority 3 — Environmental Factors (6% variance)**
   - Standard wind optimization practices
   - Weight management through existing procedures

**Challenges Conventional Wisdom:** Many carriers may be overinvesting in sophisticated flight management systems while underinvesting in engine condition monitoring and predictive maintenance.

---

## Data Source

**Source:** NASA DASHlink Aviation Safety Reporting System  
**URL:** [https://c3.ndc.nasa.gov/dashlink/resources/664/](https://c3.ndc.nasa.gov/dashlink/resources/664/)

| Metric | Value |
|--------|-------|
| Total Measurements | 1,878,441 |
| Flights Analyzed | 312 (filtered from 651) |
| Flight Hours | ~130 hours of cruise operations |
| Sampling Rate | 4 Hz |
| Aircraft | Tail 687 (wide-body, 4 engines) |

### Key Variables
- **Outcome:** Total fuel flow (lbs/hr)
- **Predictors:** Altitude, Mach number, engine fan speed (N1), headwind component, cumulative fuel burned (weight proxy)

---

## Methodology

1. **Data Cleaning:** Filtered to cruise phase only (vertical velocity < 500 ft/min), applied physical bounds, removed instrument anomalies (~0.4%)

2. **Key Discovery:** Identified "slow cruise" operations (5.4% of data) — a fuel-optimization strategy used under heavy weight conditions, validating weight as a critical predictor

3. **Statistical Analysis:**
   - One-way and two-way ANOVA (effect sizes via η²)
   - Sequential regression (6 nested models)
   - Nested F-tests with practical significance criteria
   - Variance decomposition

4. **Practical Significance Thresholds:** ΔR² < 0.01% trivial | 0.01-0.1% marginal | 0.1-1% moderate | >1% substantial

---

## Analysis

### One-Way ANOVA Results

We conducted one-way ANOVA tests to assess how much variance each categorical factor explains independently. Altitude band selection explains 9.7% of total variance in fuel consumption (F = 100,273, η² = 0.0965), confirming that higher altitudes reduce fuel burn as expected from aerodynamic theory. Weight category explains 7.7% of variance (F = 78,581, η² = 0.0772), with heavier aircraft consuming more fuel. Wind category contributes only 2.5% of variance (F = 24,343, η² = 0.0253), indicating limited impact from meteorological conditions.

### Two-Way ANOVA: Interaction Effects

The two-way ANOVA incorporating altitude band, wind category, and their interaction revealed that while the interaction term achieves statistical significance (F = 5,478, p < 0.001), it contributes only 1.0% additional explained variance beyond main effects. This modest contribution suggests that the relationship between altitude and fuel consumption does not depend strongly on wind conditions, supporting treatment of these factors as independent in operational guidelines.

### Sequential Regression Results

We estimated six nested regression models to quantify incremental variance explained by each predictor set.

| Model | Variables | R² | Interpretation |
|-------|-----------|-----|----------------|
| M1: Full (interactions) | 8 | 0.9586 | All predictors + interactions |
| M2: −alt×mach | 7 | 0.9585 | Remove altitude-Mach interaction |
| M3: Main effects only | 5 | 0.9581 | No interactions |
| M4: −N1 (engine) | 4 | 0.3404 | **Critical drop: 61.8 percentage points** |
| M5: Core (alt, mach) | 2 | 0.2850 | Altitude and speed only |
| M6: Null (intercept) | 0 | 0.0000 | Baseline |

The critical transition occurs between Model 3 and Model 4. Removing engine fan speed causes R² to plummet from 0.958 to 0.340, establishing N1 as the dominant predictor. This 61.8 percentage point decrease represents the largest single-variable contribution in the model sequence.

### Nested F-Test Results

Each model comparison was evaluated using nested F-tests to assess whether added complexity provides statistically and practically significant improvement.

| Comparison | F-statistic | ΔR² | Practical Significance |
|------------|-------------|-----|------------------------|
| M1 vs M2 | 2,085 | 0.0046% | Trivial |
| M2 vs M3 | 10,766 | 0.05% | Marginal |
| **M3 vs M4** | **27,700,000** | **61.8%** | **Massive** |
| M4 vs M5 | 78,806 | 5.5% | Substantial |
| M5 vs M6 | 374,399 | 28.5% | Substantial |

Despite enormous F-statistics for interaction terms (a consequence of n = 1.88 million), their practical contributions are negligible. The M3 vs M4 comparison demonstrates that engine fan speed alone explains more variance than all other predictors combined.

### Variance Decomposition

The decomposition isolates unique contributions from each predictor set, independent of model-building order.

| Predictor Set | % Total Variance | % Explained Variance |
|---------------|------------------|----------------------|
| Engine Fan Speed (N1) | 61.8% | 64.4% |
| Altitude + Mach Number | 28.5% | 29.7% |
| Headwind + Weight | 5.5% | 5.8% |
| Interaction Terms | 0.05% | <0.1% |
| Unexplained | 4.1% | — |

Engine performance management offers approximately **2.2 times** the fuel savings potential of altitude-speed optimization (61.8% ÷ 28.5% = 2.17). The trivial 0.05% contribution from interaction terms confirms that operational guidelines can treat altitude and speed as independent factors without meaningful accuracy loss.

### Model Diagnostics

The final model exhibits strong diagnostic properties. Residuals have mean = 0.00 and standard deviation = 86.6 lbs/hr (less than 2% of mean fuel flow). Skewness = 0.014 indicates near-perfect symmetry. Excess kurtosis = 0.904 suggests slightly heavier tails than normal but within acceptable bounds. While formal tests (D'Agostino-Pearson, Breusch-Pagan) reject perfect normality and homoskedasticity, this reflects the extreme statistical power of n = 1.88 million rather than practically meaningful violations. Visual inspection of residual plots confirms adequate model specification.

---

## Repository Structure

```
├── README.md                          # This file
├── requirements.txt                   # Python dependencies
├── code/
│   └── aviation_optimization_group_project.ipynb  # Main analysis notebook
├── data/
│   ├── cleaned_cruise_dataset.csv     # Processed dataset (578 MB, Git LFS)
│   ├── cruise_flight_summary.csv      # Flight metadata
│   └── flight_parameters_dictionary.csv  # Variable definitions
└── reports/
    └── Group_Project_Communication_Report.pdf  # Final research paper
    └── SU_DATA_5100_Final_Presentation.pdf  # Final presentation slide decks
```

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
statsmodels
scipy
tqdm
```

**Installation:**
```bash
pip install -r requirements.txt
```

---

## Reproducibility

This repository uses **Git Large File Storage (Git LFS)** for the large dataset file.

### Setup Instructions

1. **Install Git LFS:**
   ```bash
   # macOS
   brew install git-lfs
   
   # Ubuntu/Debian
   sudo apt-get install git-lfs
   
   # Windows: Download from https://git-lfs.github.com/
   ```

2. **Initialize and clone:**
   ```bash
   git lfs install
   git clone https://github.com/dcnguyen060899/data_5100_02_group_project
   ```

3. **Verify dataset:**
   ```bash
   ls -lh data/cleaned_cruise_dataset.csv
   # Should be ~551-578 MB (not a few KB)
   ```

---

## Limitations

| Limitation | Impact | Mitigation |
|------------|--------|------------|
| Single aircraft type | Results specific to Tail 687 | Replicate for fleet generalization |
| Cruise-only focus | Excludes climb/descent phases | Extend to complete flight profiles |
| Observational design | Associations, not causal effects | Validate via intervention studies |
| Weight proxy | Cumulative fuel ≠ actual weight | Acknowledged in methodology |
| 2012 data | Procedures may have evolved | Valid operational baseline |

---

## Future Research

- Extend analysis to heterogeneous fleets
- Incorporate climb and descent phases
- Develop predictive maintenance models linking engine condition to fuel consumption
- Prospective intervention studies to validate the 2.2× engine advantage

---

## References

1. Boeing. (2024). *ecoDemonstrator Program.* Boeing Sustainability. https://www.boeing.com/sustainability/ecodemonstrator

2. Boeing Commercial Airplanes. (2024). *The Boeing ecoDemonstrator Program* [Backgrounder]. https://www.boeing.com/content/dam/boeing/boeingdotcom/principles/environment/pdf/BKG-ecoDemonstrator_2024_Oct.pdf

3. Matthews, B. (2012). *Flight Data for Tail 687.* NASA DASHlink. https://c3.ndc.nasa.gov/dashlink/resources/664/

---

## License

## License

MIT License - Copyright (c) 2025 Duy Nguyen

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

This project is part of Duy Nguyen's academic portfolio for the MS Data Science program at Seattle University. While the code is open source under MIT License, **attribution is required** if you use or adapt this work. Please credit the original author and link back to this repository.



## Author Contacts:

- [Email](mailto:dnguyen44@seattleu.edu)  
- [LinkedIn](https://www.linkedin.com/in/duwe-ng/)
- [Portfolio](https://duyng-portfolio.com/docs/index_portfolio.html)

---

*Seattle University — Master of Science in Data Science — DATA 5100*

**Copyright © 2025 Duy Nguyen. Licensed under the MIT License.**
