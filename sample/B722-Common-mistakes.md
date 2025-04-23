
# Common Mistakes and Suggested Solutions in Research

## A. Design and Data Collection

### 1. Hypothesis-analysis consistency
- **Common mistake**: Hypotheses do not appropriately reflect the measured constructs, are not directly testable, or do not map the empirical methods used.  
- **Suggested solution**:  
  - Ensure consistency between theoretical and empirical constructs.  
  - Clearly state counterfactual treatment (for experiments).  
  - Identify expected directionality and whether an effect or an association is investigated.  
  - Avoid multiple explanatory and multiple dependent variables within single hypotheses.  

### 2. Experimental Design
- **2.1 Confounded experimental manipulations**  
  - Carefully design and empirically pre-test the experimental treatments to ensure manipulation of one variable at a time (so as to isolate treatment effects).  

- **2.2 Use of demand-driven priming studies**  
  - Perform pre-tests to ensure treatments do not trigger asymmetric demand effects.  
  - Use non-priming techniques to manipulate the construct of interest.  

- **2.3 Manipulation checks are placed between manipulation and dependent variable**  
  - If manipulation checks are needed, they should be conducted out-of-sample.  
  - When manipulation checks have been placed before the dependent variable, running an additional “reduced form” experiment can test whether study results were biased by the manipulation check.  

- **2.4 Manipulation checks are confused with attention checks or compliance checks**  
  - Clearly differentiate between the different types of checks in design, terminology, and interpretation.  

- **2.5 Passing a manipulation check and assuming effect goes only via hypothesized channel**  
  - Complement a manipulation check by discriminant manipulation checks showing that the treatment did not also affect plausible alternative channels.  

### 3. Measurement
- **3.1 Use of double-barreled items in scales**  
  - Clearly state any data omissions or exclusions providing details.  

- **3.2 Improperly documented scale adaptations**  
  - Report any scale adaptation, providing all scale items (modified and unmodified).  

- **3.3 Use of Cronbach’s alpha**  
  - Consider alternatives to Cronbach’s alpha like McDonald’s Omega (Raykov’s rho).  

### 4. Qualitative Methods
- **4.1 Ambiguous coding methodology**  
  - Report objective, reproducible analyses (e.g., via topic modeling).  

- **4.2 Use of purposeful sampling without having counterfactuals**  
  - Select representative or random counterfactual or matched cases.  

- **4.3 Data omissions and exclusions are not properly reported**  
  - Clearly state any data omissions or exclusions. Justify omissions or exclusions.  

- **4.4 Leading interview questions and other biased interviewing techniques**  
  - Avoid leading questions, use neutral language, clearly report all measures used in the study to minimize bias.  

### 5. Open Science
- **5.1 Open science procedures and practices are not or are insufficiently followed.**  
  - Where possible, preregister both quantitative and qualitative studies, allowing only minor deviations from preregistered study script.  
  - Transparently report them, share data from published articles.  
  - Clearly distinguish between confirmatory and exploratory analyses.  
  - Provide code/analysis file(s) from published articles to simplify replication.  
  - Share experimental materials or details of data collection/mining.  

### 6. Literature Reviews
- **6.1 Usage of vague or unreproducible procedures in literature reviews.**  
  - Ensure reproducibility of literature reviews.  
  - Clearly describe all procedures of literature search and selection to simplify replication.  
  - Follow PRISMA guidelines.  

## B. Data Analysis

### 7. Data Preprocessing and Transformation
- **7.1 Missing data are removed without appropriate justification.**  
  - Clearly document missing data, justify any missing data deletion; use appropriate estimator to handle missing data.  

- **7.2 Inappropriate or unnecessary use of centering (e.g., mean or scale centering).**  
  - Only mean-center when appropriate, provide clear justification for any centering procedures used in the study.  

- **7.3 Use of unnecessary indices and composites (including hierarchical composites).**  
  - Avoid unnecessary indices and composites. Model hierarchical decisions appropriately.  

- **7.4 Non-linear (e.g., logarithmic) transformations are used inappropriately.**  
  - Any non-linear transformations should have a theoretical underpinning and clear justification based on the functional form of the relationship between variables.  

- **7.5 Log-transforming the dependent variable.**  
  - Consider estimating a generalized linear model (GLM) using quasi-maximum likelihood estimation (QMLE).  

- **7.6 Using ratios in statistical models.**  
  - In general, avoid using ratios in statistical models; instead use the numerator and control for the denominator.  

- **7.7 Unjustified difference scores are used as dependent or explanatory variables.**  
  - Avoid difference scores. If the use of difference scores is necessary, run the relevant tests.  

- **7.8 Continuous variables are arbitrarily categorized (e.g., median splits).**  
  - Always treat continuous variables as such. If theoretical categories may be of interest, consider running a finite mixture model.  

### 8. Qualitative Methods for Text Analysis
- **8.1 Data cleaning stage is not or inappropriately documented.**  
  - Clearly justify and document data cleaning in qualitative text analysis.  

### 9. OLS is Appropriate for Bounded Outcomes
- **9.1 Using GLMs with non-linear links and not considering the use of OLS.**  
  - Consider using linear regression by OLS as a default option if the research interest is estimating average marginal effects (rather than focusing on prediction).  

### 10. Regression Ranking Techniques
- **10.1 Relative weights analysis is used to rank the strength of predictors in a model.**  
  - Only interpret unstandardized partial regression coefficients (obtained from causally identified models) for causal effects.  

### 11. Analyzing Experimental Data
- **11.1 Omission of balance checks when randomization to treatment may have been compromised.**  
  - Balance checks can provide information on whether randomization to treatments was not successful.  

- **11.2 The coefficients from regression results are misinterpreted when analyzing 2x2 experiments.**  
  - Report relevant specifications and results carefully.  
  - Avoid misinterpretation by using correct specifications.  

- **11.3 Deleting observations from participants who failed a post-treatment manipulation check.**  
  - Clearly justify and document any data deletion related to manipulation checks.  

## C. Diagnostics, Inferences, and Reporting

### 18. Diagnostics
- **18.1 Harman’s single-factor test is used to detect common method variance (CMV).**  
  - Reduce the risk of CMV in the study design (e.g., collect dependent and independent variables from different sources) or the study analysis stages (e.g., use instrumental-variable estimation).  

- **18.2 Outliers are ignored or identified without transparent and appropriate procedures.**  
  - Carefully document the procedure used for outlier identification, to ensure replicability of the results.  
  - In general, report results with and without outliers and/or consider outlier-robust regression methods.  

### 19. Statistical Inference
- **19.1 Testing on multiple outcomes without appropriate corrections.**  
  - Mitigate the problem via Bonferroni correction or related procedures.  

- **19.2 Results are interpreted as “marginally significant.”**  
  - Avoid labeling results as “marginally significant.” Focus on significant results based on an a priori alpha level.  

- **19.3 Absence of statistical significance is interpreted as “no effect.”**  
  - Statistically insignificant results simply imply that “no conclusion can be drawn” from the data. To test for “no effect,” use additional analyses (e.g., equivalence tests, Bayes factors).  

- **19.4 Unjustified use of normal standard errors.**  
  - Depending on the properties of the data, rely on heteroskedasticity-robust standard errors.  
  - Cluster-robust standard errors might also be necessary in multilevel models.  

### 20. Reporting
- **20.1 Insufficient details in figures and tables to ensure replicability.**  
  - For regression tables, report (at least in the appendix) all higher order terms, interactions, and control variables used.  
  - Label variables in Figures and Tables clearly and consistently, avoiding misleading scales in graphs’ axes.  

- **20.2 Inconsistent reporting of p-values and other statistics.**  
  - Check reported p-values and other statistics carefully before submission.  

- **20.3 Not reporting uncertainty of estimates in tables and figures.**  
  - Report complete results that clarify the magnitude of the reported effect, as well as a measure of the uncertainty of the estimate.  

- **20.4 Incorrect use of one-tailed tests (especially if estimator is underpowered).**  
  - In general, report two-tailed tests.  

- **20.5 Extrapolating fitted predictions beyond data range.**  
  - Check that all predictions are reported within the range of the analyzed data and only depict this range in your figures.  

- **20.6 Reporting standardized regression coefficients.**  
  - Report unstandardized regression coefficients or correct for measurement error in y before standardizing.  

- **20.7 Reporting only ANOVA tables.**  
  - Report only full regression tables, which provide additional and clearer information compared to ANOVA ones.  

