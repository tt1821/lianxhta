# B518-Stata：manyweakiv-多个弱IV的推断和检验

写一篇推文，介绍 `manyweakiv` 命令的原理和使用方法。 

> <https://github.com/lsun20/manyweakiv>

- Mikusheva, A. and Sun, L., 2022. [Inference with Many Weak Instruments](https://doi.org/10.1093/restud/rdab097), The Review of Economic Studies, 89(5):2663–2686. [Preprint](https://arxiv.org/abs/2004.12445), [Github](https://github.com/lsun20/manyweakiv)
- Mikusheva, A. and Sun, L. 2023. [Weak Identification with Many Instruments](https://arxiv.org/abs/2308.09535). arXiv:2308.09535, [Github](https://github.com/lsun20/manyweakiv)

Inside this repository, you'll find two main commands:

1. `manyweakivpretest`: This command is designed to evaluate the strength of instruments. Jack-knife IV estimator is reliable only if instruments are strong.
2. `manyweakiv`: This command conducts weak identification-robust inference.

Both methods draw from Mikusheva and Sun (2022, 2023). Importantly, they are valid in settings with many instruments and a general form of heteroscedasticity.
