# B760：ChatGPT助手系列：CClaRA-Causal Claims Research Assistant

> <https://www.causal.claims/cclara-causal-claims-ra>

- Garg, P., & Fetzer, T. (2025). Causal Claims in Economics (Version 1). arXiv. [Link](https://doi.org/10.48550/arXiv.2501.06873) (rep), [PDF](https://arxiv.org/pdf/2501.06873.pdf), [Google](<https://scholar.google.com/scholar?q=Causal Claims in Economics (Version 1)>).

A literature review tool grounded on the knowledge graph.

-   Search for papers by causal claims (`where X causes Y`)
-   Search for paper by concept nodes (e.g., `effects of X, causes of X`)
-   Search by claims made in specific journal (e.g. `the top five`)
-   Search by authors working on specific fields, and so on.

## About CClaRA

CClaRA is a customised GPT-4o model fine-tuned with access to our knowledge graph dataset.
This means each response is based on a combination of ChatGPT -4o 's training data and the corpus.
CClaRA searches across multiple files of structured data, ensuring that every query you make provides accurate and comprehensive results. 

训练语料：6000 多篇 NBER 和 SSRN 的工作论文，以及 Top 5 Journal 中的论文 (具体数字要看一下论文原文中的表述)

Garg, P., & Fetzer, T. (2025). Causal Claims in Economics (Version 1). arXiv. [Link](https://doi.org/10.48550/arXiv.2501.06873) (rep), [PDF](https://arxiv.org/pdf/2501.06873.pdf), [Google](<https://scholar.google.com/scholar?q=Causal Claims in Economics (Version 1)>).
  - [Short-Review](https://cepr.org/voxeu/columns/leveraging-large-language-models-large-scale-information-retrieval-economics)

## How to Use CClaRA

1.  Go to <https://chatgpt.com/g/g-QE4aPgEVQ-cclara> and sign in.

2.  Simply type in your query. 


## Use cases

Here are some common use cases

1.  Exact Cause and Effect Match

    -   Example Prompt: "Find papers where fiscal policy causes economic growth."

    -   This search will look for papers that contain the specified cause-effect relationship and return exact matches, if available.

2.  Cause-only Search

    -   Example Prompt: "Find papers with 'job mobility' as a cause."

    -   When only a cause is specified, the assistant will return all papers that mention this cause, along with a list of effects associated with it.

3.  Effect-only Search

    -   Example Prompt: "Find papers with 'earnings growth' as an effect."

    -   If only an effect is provided, the assistant will list papers where this effect is documented, identifying the variety of causes related to this effect.

4.  Semantic and Related Suggestions

    -   Example Prompt: "Find papers where 'government spending' influences 'GDP growth'."

    -   If an exact match isn't found, the assistant will suggest papers with related terms or semantically similar causal claims, helping to broaden the scope when needed.

5.  Combining Searches with Broadening Queries

    -   Example Prompt: "Find papers where 'education' impacts 'wage growth'."

    -   If too few results are found, the assistant may prompt you to expand the query to include related terms, such as including other measures of education or wage outcomes.

## Our Approach

Leveraging a custom Artificial Intelligence (AI) pipeline, we process vast amounts of text to extract and structure causal relationships. Here's how we build the causal graph:

1.  Data Collection: Gathering a comprehensive corpus of working papers from NBER and CEPR.

2.  AI-Powered Extraction: Using our AI model to identify causal claims, empirical methods, and key economic concepts within each paper.

3.  Standardization of Concepts: Mapping extracted variables to official Journal of Economic Literature (JEL) codes for consistency.

4.  Construction of the Causal Graph: Connecting economic concepts through identified causal relationships to form a detailed causal graph.

5.  Visualization: Creating graphical representations that illustrate how economic ideas are causally linked over time.
