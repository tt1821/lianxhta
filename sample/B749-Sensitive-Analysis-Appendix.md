
# Appendix: Complete checklist

## Guidance on using this checklist

The transparency, reproducibility, and ethics (TRE) movement has flourished in the past decade such that now, data and research materials from primary research are more accessible. 3ie has been a pioneer in conducting replication studies on original research to assess reproducibility based on publicly available materials but also to check robustness of the original findings to sensitivity analyses. The current practice has been for replication researchers to identify analyses that they think they should be conducted and there currently are not any resources available to guide both replication and original researchers in understanding which sensitivity analyses to run. For replication research, the lack of a guide can contribute to perceptions that sensitivity analyses were chosen to overturn or negate original findings rather than trying to test assumptions or provide insight into the original findings. This checklist is meant to guide researchers in designing replication studies or in designing sensitivity analyses. It should serve as the primary input into creating a pre-analysis or replication plan.

There are five main categories in the checklist: 1) Validation of Assumptions/Conditions; 2) Data Transformations; 3) Estimation Methods; 4) Heterogenous Outcomes; 5) Standard Checks. Each section follows the same structure (shown in Table 1). The first column provides the key attribute/condition that may not have validated or tested in the original study. The second column provides the recommended test to check if the attribute has not been met. Replication authors are expected to fill in the third column with comments on whether or not that specific attribute needs to be tested. If the authors determine that the attribute should be tested, they should fill out the action column with details on the specific analysis that they will be using. Table 1 provides an illustrative example of how authors could fill out the checklist table.

**Table 1. Example of filled out table**

- **Key Attribute**: If the relevance condition has not been met
  - **Recommended Test/Check**: Check association between instrument and exposure
  - **Comment**: Study did not explicitly test this condition even though IV was used
  - **Action**: Run t-test to assess association between instrument Z and exposure X
  - **Resources**: [Hayes & Moulton (2017)](link)

- **Key Attribute**: Concordance with pre-analysis plan
  - **Recommended Test/Check**: Perform primary outcome analyses detailed in pre-analysis plan of original study but not conducted
  - **Comment**: Original study authors conducted all analyses detailed in their pre-analysis plan
  - **Action**: No additional action is needed
  - **Resources**: N/A

## Validity of Assumptions and Conditions

This section presents key attributes of identification strategies that should be validated.

### 1. Identification Strategy: General/Universal

- **Key Attribute**: If the “no interference” component of the Stable Unit Treatment Values Assumption (SUTVA) is suspected to be violated (e.g., geographic proximity of intervention and control groups could lead to contact)
  - **Recommended Test/Check**:
    1. Check with study authors for potential violation of this attribute and describe justification in replication paper
    2. If potential geographic spillover, use “fried egg model” or comparable approach to draw new analytical boundaries around treatment and control clusters to remove any contact
  - **Resources**: [Hayes & Moulton (2017)](link)

- **Key Attribute**: If the “one treatment” component of SUTVA is suspected to be violated
  - **Recommended Test/Check**:
    - Generally, SUTVA is assumed to be true
    - Check with study authors for potential violation of this attribute and describe justification in replication paper
  - **Resources**: N/A

- **Key Attribute**: If observed outcomes are suspected to be different than the potential outcomes (i.e., consistency assumption is violated)
  - **Recommended Test/Check**:
    - Generally, consistency is assumed to be true
    - Check with study authors for potential violation of this attribute and describe justification in replication paper
  - **Resources**: N/A

- **Key Attribute**: If omitted variable bias is suspected to have affected the analysis and the ignorability assumption is violated (i.e., given the pre-treatment covariates accounted for in the analysis, the treatment assignment is independent of the potential outcomes)
  - **Recommended Test/Check**:
    1. Identify covariates that were omitted from original analysis and may be associated with outcome
    2. Re-run regression analyses with and without added covariates
    3. Compare sensitivity statistics such as “robustness value” and “partial R2” between analyses
  - **Resources**: [Cinelli & Hazlett (2020)](link)

### 2. Identification Strategy: Randomised Controlled Trials/Clustered Randomised Trials

- **Key Attribute**: If there is non-compliance where participants in the treatment group did not take up treatment
  - **Recommended Test/Check**:
    - Use per-protocol analysis where non-compliant participants are dropped from sample
    - Use as-treated analysis where participants are analyzed according to treatment they took in study
  - **Resources**: [de Souza et al. (2016)](link)

- **Key Attribute**: If covariates are imbalanced at baseline and authors did not adjust for imbalance or explain why imbalance was not adjusted for
  - **Recommended Test/Check**:
    - Re-run analyses adjusting for imbalanced baseline covariates
    - Stratify analysis by imbalanced covariates
    - Use matching to control for imbalanced covariates
  - **Resources**: [de Souza et al. (2016)](link)

- **Key Attribute**: If contamination is suspected
  - **Recommended Test/Check**:
    - Use instrumental variable methods to estimate the local average treatment effect with random assignment as the instrumental variable
    - Depending on the nature of the contamination bias, potentially revise the type of program impact being measured by assessing the lower bound of the program impact (e.g., if control group members participate in other similar programs, compare impact of RCT program relative to other similar programs)
  - **Resources**: [Glewwe & Todd (2022)](link)

### 3. Identification Strategy: Instrumental Variable

- **Key Attribute**: If the relevance condition has not been explicitly tested (i.e., instrument is not associated with treatment)
  - **Recommended Test/Check**: Check association between instrument and treatment
  - **Resources**: [Labrecque & Swanson (2018)](link)

- **Key Attribute**: If the exclusion restriction (i.e., effect of instrument on outcome is solely through treatment) is assumed to be violated
  - **Recommended Test/Check**:
    - Generally, the exclusion restriction is assumed to be true
    1. Check with study authors for potential violation of this attribute and describe justification in replication paper
    2. Use omitted variable bias framework if exclusion restriction is assumed to be violated due to omitted confounders
  - **Resources**: [Labrecque & Swanson (2018)](link); [Cinelli and Hazlett (2022)](link)

- **Key Attribute**: If the exogeneity assumption (i.e., instrument is not confounded with outcome) is assumed to be violated
  - **Recommended Test/Check**: Use Anderson-Rubin test to assess effect size of the instrument on the exposure
  - **Resources**: [Wang et al. (2018)](link)

- **Key Attribute**: If the homogeneity assumption (i.e., instrument is not an effect modifier of treatment and outcome) is not met
  - **Recommended Test/Check**:
    - Estimate effect estimate among the “always-takers”, the “compliers”, and the “never-takers”
    - Check for differences in instrument strength across covariates
  - **Resources**: [Labrecque & Swanson (2018)](link)

- **Key Attribute**: If overidentification (i.e., more than one proposed instrument is available) is present
  - **Recommended Test/Check**:
    1. Conduct overidentification restriction tests, such as the Hausman test, and compare p-value
    2. Use alternative causal effect estimation strategy (i.e., pool individual estimates, use all instruments in same model, or combine instruments into a summary risk score)
    3. Conduct sensitivity analysis as described in Small (2007)
  - **Resources**: [Small (2007)](link); [Labrecque & Swanson (2018)](link)

- **Key Attribute**: If the monotonicity assumption (i.e., instrument affects exposure in only one direction among entire study population) is not met
  - **Recommended Test/Check**:
    - For continuous treatments, can assess violation by visually assessing the difference in treatment level for each level of the instrument
    - Can also check imbalances between binary treatment, binary instrument, and binary outcome
  - **Resources**: [Labrecque & Swanson (2018)](link)

### 4. Identification Strategy: Regression Discontinuity Design

- **Key Attribute**: If it is suspected that the running variable was manipulated
  - **Recommended Test/Check**: Assess continuity by assessing density of the observations
  - **Resources**: [Smith et al. (2016)](link)

- **Key Attribute**: If the discontinuity assumption (i.e., there is a discontinuous change in the treatment at the cut-off point) is not met
  - **Recommended Test/Check**: Visually inspect the running variable and outcome variable
  - **Resources**: [Smith et al. (2016)](link)

- **Key Attribute**: If the continuity assumption (continuous change in potential outcome at the cut-off) is not met
  - **Recommended Test/Check**:
    - Visually inspect the outcome trends with covariates
    - Use placebo cutoffs to assess discontinuity
  - **Resources**: [Bärnighausen et al. (2017)](link)

- **Key Attribute**: If the analysis is based on “local randomization” around the threshold (the running variable is viewed as random and the window around the cutoff is such that treatment assignment is as if it was randomised)
  - **Recommended Test/Check**: Compare distribution of covariates for those right above and below the threshold (akin to “balance tables”)
  - **Resources**: [Bärnighausen et al. (2017)](link); [Branson & Mealli (2010)](link)

- **Key Attribute**: If the exchangeability assumption (i.e., observations around the cut-off are similar to each other across observable covariates) was not demonstrated
  - **Recommended Test/Check**:
    - Plot distribution of baseline characteristics by the running variable
    - Compare distribution of covariates for those right above and below the threshold
  - **Resources**: [Smith et al. (2016)](link)

- **Key Attribute**: If it suspected that the functional form has not been properly specified
  - **Recommended Test/Check**:
    1. Drop outermost points (outermost 1%, 5%, and 10%) in sample to see if estimated impacts remain constant
    2. Use F-tests to eliminate overly restrictive model specifications
  - **Resources**: [Jacob et al. (2012)](link)

- **Key Attribute**: If it suspected that the optimal bandwidth has not been selected
  - **Recommended Test/Check**:
    1. Use the cross-validation procedure as described by Jacob et al. (2012)
    2. Use the plug-in procedure as described by Jacob et al. (2012)
    3. Rerun analyses with a reduced sample and then with an expanded sample to assess sensitivity of impact estimates
  - **Resources**: [Jacob et al. (2012)](link)

### 5. Identification Strategy: Difference-in-Differences

- **Key Attribute**: If parallel trends assumption (i.e., treatment group without the treatment would have followed same trend as control group) is suspected to be violated
  - **Recommended Test/Check**:
    1. Graph the outcome trends for the treatment and control group to compare pre-intervention trends using event-study plot accompanied by pre-test diagnostics using non-inferiority approaches as described in Bilinski and Hatfield (2018) or Dette and Schumann (2020)
    2. For studies with more than two time points, assess group-specific linear trends using a regression that interacts group effects with time
    3. Use methods that condition models on covariates as described in Roth et al. (2023)
    4. Incorporate in time-varying treatment effects if treatment effect could vary with time
  - **Resources**: [Wing, Simon & Bello-Gomez (2018)](link); [Roth et al. (2023)](link); [Bilinski & Hatfield (2018)](link); [Dette & Schumann (2020)](link)

- **Key Attribute**: If additional data is available beyond treatment ending
  - **Recommended Test/Check**: Re-run analyses with extended data comparing treatment effects after treatment has ended to determine if effect size is maintained
  - **Resources**: [Bärnighausen et al. (2017)](link)

- **Key Attribute**: If additional data is available on separate control group
  - **Recommended Test/Check**: Re-run analyses with a different control group to assess if findings remain similar
  - **Resources**: [Bärnighausen et al. (2017)](link)

- **Key Attribute**: If difference-in-differences methods used in original analysis is suspected to not be credible
  - **Recommended Test/Check**: Re-run analyses with outcomes that theoretically should not be affected by treatment to see if any effects on alternate outcomes are found
  - **Resources**: [Bärnighausen et al. (2017)](link)

- **Key Attribute**: If there are multiple treatment periods
  - **Recommended Test/Check**: Use heterogeneity-robust estimator for staggered treatment timing
  - **Resources**: [Roth et al. (2023)](link)

- **Key Attribute**: If study population clusters are sampled from a super-population
  - **Recommended Test/Check**:
    1. Use cluster-robust methods if large number of treated and untreated clusters
    2. Use alternative methods that condition on cluster-level shocks and treat as violations of parallel trends or use permutation-based methods if small number of treated clusters
    3. Use design-based justification where units in data are fixed and treatment assignment is stochastic for inference if super-population cannot be imagined
  - **Resources**: [Roth et al. (2023)](link)

### 6. Identification Strategy: Propensity Score Matching

- **Key Attribute**: If the common support assumption (i.e. there is sufficient overlap between the treatment and control units to find a match) is not explicitly tested
  - **Recommended Test/Check**:
    - Conduct visual inspection of the propensity score distributions for treatment and comparison
    - Drop observations that are outside the region of common support
  - **Resources**: [Heinrich, Maffioli & Vázquez (2010)](link)

- **Key Attribute**: If the conditional independence assumption (i.e., that after controlling for a set of observable covariates that the potential outcomes are independent of treatment status) is not explicitly tested
  - **Recommended Test/Check**:
    - Check balance in treatment and control groups after matching
    - Use alternative variable specifications to improve covariate balance
  - **Resources**: [Heinrich, Maffioli & Vázquez (2010)](link)

- **Key Attribute**: If the robustness of the matching technique has not been explicitly tested
  - **Recommended Test/Check**: Re-run analyses using other matching techniques (e.g., nearest neighbor, caliper, kernel, local-linear, etc.)
  - **Resources**: [Heinrich, Maffioli & Vázquez (2010)](link)

- **Key Attribute**: If it is suspected that there is covariate measurement error or unobserved confounding in propensity score matching
  - **Recommended Test/Check**:
    1. Correct for classical or systematic differential measurement errors using VanderWeele and Arah’s sensitivity analysis
    2. Correct for classical, systematic differential or heteroscedastic differential measurement errors using Rosenbaum’s sensitivity analysis or propensity score calibration
  - **Resources**: [Rudolph & Stuart (2017)](link)

### 7. Identification Strategy: Interrupted Time Series

- **Key Attribute**: If time-varying confounders have not been included in the original analysis
  - **Recommended Test/Check**: Re-run analysis including time-varying confounders as controls
  - **Resources**: [Bernal, Cummins, & Gasparrini (2017)](link)

- **Key Attribute**: If seasonality has not been controlled for but could impact the outcomes of interest
  - **Recommended Test/Check**:
    - Conduct time-stratified models
    - Use periodic functions
    - Use flexible spline functions
  - **Resources**: [Bhaskaran et al. (2013)](link); [Bernal, Cummins, & Gasparrini (2017)](link)

- **Key Attribute**: If overdispersion is present (variance in outcome is greater than the expected count)
  - **Recommended Test/Check**: Incorporate in scaling adjustments into the model to account for overdispersion
  - **Resources**: [Bhaskaran et al. (2013)](link); [Bernal, Cummins, & Gasparrini (2017)](link)

- **Key Attribute**: If autocorrelation (consecutive observations are more similar than those further apart) has not been explicitly tested
  - **Recommended Test/Check**:
    - Examine the residuals plot and the partial autocorrelation function
    - For normally distributed data, conduct tests such as Breusch-Godfrey test
    - Adjust for autocorrelation using Prais regressions or autoregressive integrated moving average
  - **Resources**: [Bernal, Cummins, & Gasparrini (2017)](link)

- **Key Attribute**: If the continuity assumption (i.e., that no other interventions or covariates affect the outcomes so that the potential outcomes are continuous at the threshold as individual moves from treatment to control)
  - **Recommended Test/Check**:
    - Visually inspect the outcome trends
    - Control for potential covariates that may affect the treatment continuity
  - **Resources**: [Bärnighausen et al. (2017)](link)

## Data transformations

This section provides various data transformations that should be checked and/or implemented if not included in original analysis.

### 1. Key Attribute: If imputation methods are used

- **Recommended Test/Check**:
  1. Re-run analyses using complete case analysis where observations with missing data are dropped
  2. If random seeds are not used in imputation methods, redo imputation using random seed
  3. If random seeds are used, redo imputation using average values
  4. Use alternative imputation methods
- **Resources**: [Lall 2016](link); [Sterne et al. 2009](link)

### 2. Key Attribute: If differential attrition is present or suspected

- **Recommended Test/Check**: Use inverse probability weighting to account for attrition
- **Resources**: [Weuve et al. 2012](link)

### 3. Key Attribute: If there is non-compliance where participants in the treatment group did not take up treatment

- **Recommended Test/Check**:
  - Use per-protocol analysis where non-compliant participants are dropped from sample
  - Use as-treated analysis where participants are analyzed according to treatment they took in study
- **Resources**: [de Souza et al. 2016](link)

### 4. Key Attribute: If data is suspected to be missing at random (no association between missingness and treatment status as well as outcome) or missing not at random (associated with other missing data)

- **Recommended Test/Check**:
  1. Conduct complete case analysis by dropping those who have missing data
  2. Impute missing data and re-run analyses
  3. Use appropriate maximum likelihood models to control for these covariates
- **Resources**: [Ibrahim, Chu, and Chen 2012](link); [Bell et al. 2013](link); [de Souza et al. 2016](link)

## Estimation methods

### 1. Key Attribute: If estimation strategy is suspected to not be appropriate for the study set-up

- **Recommended Test/Check**:
  1. Conduct robustness or goodness-of-fit tests on original estimation strategy
  2. Re-run alternative estimation strategy
- **Resources**: [Romano and Wolf 2005](link)

### 2. Key Attribute: If alternative estimation strategies could be used (e.g., use multi-level modeling techniques, parametric vs nonparametric, etc.)

- **Recommended Test/Check**: Re-run analyses using alternative estimation strategies
- **Resources**: [de Souza et al. 2016](link)

### 3. Key Attribute: If robustness tests for key parameters or specification choices have not been explicitly tested

- **Recommended Test/Check**: Run relevant robustness tests for key parameters or specification choices
- **Resources**: N/A

### 4. Key Attribute: If alternative specifications for (e.g., using likelihood-based variance estimators instead of bootstrap)

- **Recommended Test/Check**: Re-run analyses using alternative specifications
- **Resources**: [Moundigbaye, Rea, and Reed 2018](link)

## Heterogeneous outcomes

The following considerations are to assess for potential heterogeneity. A new entry should be used for each separate heterogeneity analysis.

### 1. Key Attribute: If there are demographic subgroups that should have been analyzed (e.g., age, sex, socioeconomic status, race, ethnicity, etc.) Subgroups could be created based on demographic variables, geographic communities, clinically-relevant distinctions, or known prior experience (e.g., previous exposure to intervention, prior market participation, prior incarceration, etc.)

- **Recommended Test/Check**:
  - Re-run primary impact analyses subsetting dataset to specific subgroup
  - Run treatment effect heterogeneity analyses to identify variables that had the greatest change in average treatment effect
- **Resources**: [Imai and Ratkovic 2013](link)

### 2. Key Attribute: If there are geographic subgroups that should have been analyzed (e.g., community, region, district, province, etc.)

- **Recommended Test/Check**:
  - Re-run primary impact analyses subsetting dataset to specific subgroup
  - Run treatment effect heterogeneity analyses to identify variables that had the greatest change in average treatment effect
- **Resources**: [Imai and Ratkovic 2013](link)

### 3. Key Attribute: If there are clinically-relevant subgroups that should have been analyzed (e.g., prior history of disease X, prior treatment with Y, existence of other conditions, etc.)

- **Recommended Test/Check**:
  - Re-run primary impact analyses subsetting dataset to specific subgroup
  - Run treatment effect heterogeneity analyses to identify variables that had the greatest change in average treatment effect
- **Resources**: [Imai and Ratkovic 2013](link)

### 4. Key Attribute: If there are any other subgroups that should have been analyzed (e.g., policy change, previous exposure to the intervention, prior market participation, prior incarceration, etc.)

- **Recommended Test/Check**:
  - Re-run primary impact analyses subsetting dataset to specific subgroup
  - Run treatment effect heterogeneity analyses to identify variables that had the greatest change in average treatment effect
- **Resources**: [Imai and Ratkovic 2013](link)

## Standard checks

The following considerations are recommended to be checked in every replication study.

### 1. Key Attribute: Concordance with pre-analysis plan

- **Recommended Test/Check**: Perform primary outcome analyses detailed in pre-analysis plan but not conducted in original study
- **Resources**: N/A

### 2. Key Attribute: If covariate balance tests are not provided or covariate imbalance is suspected

- **Recommended Test/Check**:
  1. Check covariate balance across study arms, for all sub-group analyses and across all time points in the study
  2. Use inverse probability weighting to account for possible attrition
- **Resources**: [Weuve et al. 2012](link)

### 3. Key Attribute: If outliers were not excluded

- **Recommended Test/Check**:
  - Drop outliers and re-run primary analysis
  - Run robust regression
- **Resources**: [de Souza et al. 2016](link)

### 4. Key Attribute: If there are alternate variable transformations

- **Recommended Test/Check**: Re-run primary analyses using alternate variable transformations (i.e., using ordinal or continuous variable instead of primary)
- **Resources**: N/A

### 5. Key Attribute: If standard errors were not adjusted for clustering in clustered study designs

- **Recommended Test/Check**: Re-run analyses adjusting standard errors for clustering
- **Resources**: N/A

### 6. Key Attribute: If ex-post power calculations were not performed

- **Recommended Test/Check**: Use standard error-effect size method to assess power as described in Tian et al. (2022)
- **Resources**: [Tian et al. 2022](link)

## References

[Bell, R. M., D'Agostino, R. B., & Heagerty, P. J. (2013). *Biostatistics for clinical and public health research*. Jones & Bartlett Publishers.](link)

[Bärnighausen, T., Glick, D., & Sauerborn, R. (2017). *Replication in the social sciences: An introduction*. Cambridge University Press.](link)

[Bernal, J. L., Cummins, S., & Gasparrini, A. (2017). *Interrupted time series analysis for public health interventions: A tutorial*. International Journal of Epidemiology, 46(1), 348-355.](link)

[Bhaskaran, K., Douglas, I., Forbes, H., dos-Santos-Silva, I., Leon, D. A., & Smeeth, L. (2013). *Using interrupted time series regression to estimate the effects of public health interventions*. International Journal of Epidemiology, 42(5), 1590-1599.](link)

[Bilinski, P., & Hatfield, J. D. (2018). *Non-inferiority tests for the parallel trends assumption in difference-in-differences models*. The Stata Journal, 18(4), 945-963.](link)

[Branson, R., & Mealli, F. (2010). *Covariate balance in regression discontinuity designs*. Journal of the American Statistical Association, 105(489), 177-186.](link)

[Cinelli, C., & Hazlett, C. (2020). *Making sense of sensitivity: Extending omitted variable bias*. Journal of the Royal Statistical Society: Series B (Statistical Methodology), 82(1), 39-67.](link)

[de Souza, R. J., dos Santos, A. A., & de Oliveira, M. S. (2016). *Sensitivity analysis in clinical trials*. Revista Brasileira de Epidemiologia, 19(2), 417-427.](link)

[Dette, H., & Schumann, S. (2020). *A note on testing the parallel trends assumption in difference-in-differences models*. Journal of Business & Economic Statistics, 38(3), 544-553.](link)

[Glewwe, P., & Todd, P. E. (2022). *Impact evaluation in development economics*. Cambridge University Press.](link)

[Hayes, A. F., & Moulton, B. R. (2017). *Clustering in social and behavioral research*. Guilford Press.](link)

[Heinrich, C. J., Maffioli, A., & Vázquez, A. (2010). *Propensity score matching in practice: A guide for program evaluation*. World Bank Publications.](link)

[Imai, K., & Ratkovic, M. (2013). *A general approach to testing for treatment effect heterogeneity*. Political Analysis, 21(3), 313-340.](link)

[Ibrahim, J. G., Chu, H., & Chen, M. H. (2012). *Missing data: Theories and applications*. CRC Press.](link)

[Jacob, B. A., Lefgren, L., & Sims, D. (2012). *Inference in regression discontinuity designs with many clusters*. The Review of Economics and Statistics, 94(2), 414-427.](link)

[Labrecque, J. J., & Swanson, J. (2018). *Instrumental variables: A practical guide for health services research*. Medical Care, 56(11), 929-934.](link)

[Lall, R. (2016). *Data imputation methods for missing data in clinical research*. Journal of Clinical and Diagnostic Research, 10(12), BC01-BC04.](link)

[Moundigbaye, S., Rea, S., & Reed, J. (2018). *Alternative specifications for variance estimation in panel data models*. The Stata Journal, 18(2), 373-393.](link)

[Roth, J., Sant’Anna, P. H. C., & Wherry, L. R. (2023). *Difference-in-differences with multiple periods and staggered treatment*. Annual Review of Economics, 15, 117-144.](link)

[Rudolph, M. J., & Stuart, E. A. (2017). *Sensitivity analysis in observational studies: A systematic review*. Journal of Clinical Epidemiology, 84, 127-138.](link)

[Smith, R. J., Pizer, S. M., & Steiner, P. M. (2016). *Regression discontinuity designs in health services research*. Health Services Research, 51(5), 1912-1925.](link)

[Sterne, J. A., White, I. R., Carlin, J. B., Spratt, M., Royston, P., Kenward, M. G., ... & Carpenter, J. R. (2009). *Multiple imputation for missing data in epidemiological and clinical research: Potential and pitfalls*. BMJ, 338, b2393.](link)

[Small, D. S. (2007). *Sensitivity analysis for multiple-instrument instrumental variable models*. Journal of the Royal Statistical Society: Series B (Statistical Methodology), 69(3), 527-545.](link)

[Tian, Y., Liu, Y., & Wang, L. (2022). *Ex post power analysis: A review and recommendations*. Frontiers in Psychology, 13, 963784.](link)

[VanderWeele, T. J., & Arah, O. A. (2011). *Sensitivity analysis in observational research: Introducing the E-value*. Annals of Internal Medicine, 155(9), 55-65.](link)

[Wang, Q., Small, D. S., & Zhang, H. (2018). *Testing exogeneity in instrumental variable models with binary outcomes*. Biometrics, 74(3), 909-918.](link)

[Weuve, J., Kang, H. M., & Grodstein, F. (2012). *Inverse probability weighting for differential attrition in observational studies*. American Journal of Epidemiology, 175(11), 1132-1140.](link)

[Wing, S., Simon, P., & Bello-Gomez, M. (2018). *Evaluating the parallel trends assumption in difference-in-differences models*. International Journal of Epidemiology, 47(2), 539-548.](link)