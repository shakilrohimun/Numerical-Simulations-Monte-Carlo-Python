[![Language](https://img.shields.io/badge/Language-Python-blue.svg)](https://www.python.org/)

# Numerical Methods & Probabilistic Simulation — Practical Project

> Master’s level practicals in numerical methods and probability, implemented in Python (NumPy/Matplotlib).

## Executive Summary

This project explores core ideas in stochastic simulation and numerical estimation:

- **Random Number Generation (RNG)** and empirical checks of uniformity (histograms, empirical mean/variance).
- **Law of Large Numbers (LLN)**: convergence of empirical averages toward the true expectation.
- **Central Limit Theorem (CLT)**: asymptotic normality of the re‑scaled sample mean.
- **Monte Carlo (MC) Integration**: estimating deterministic integrals via sampling (e.g., area/volume estimates such as \\(\\pi\\)).
- **Rejection Sampling**: sampling uniformly on complex sets and from general densities using envelopes.
- **Mixture Distributions**: sampling from mixtures by introducing a latent categorical variable.

**Conclusions**

- Empirical experiments confirm **LLN** (bias → 0) and **CLT** (Gaussian fluctuations of the mean), with MC error scaling as **\\(\\mathcal{O}(1/\\sqrt{N})\\)**.
- **Monte Carlo** provides flexible estimators for integrals and geometric quantities; confidence intervals follow from estimated variance.
- **Rejection sampling** is correct by construction; its **efficiency equals the acceptance probability** (e.g., area ratio for uniform subsets, or **\\(1/C\\)** with an envelope constant \\(C\\)).
- **Mixtures** are naturally sampled by first drawing the component index and then the corresponding component; resulting histograms match the target density.

---

## Technical Deep Dive

### 1) Random Number Generation (Uniform \\([0,1]\\))

- Assume access to a pseudo‑RNG \\(\\texttt{rand()}\\) producing i.i.d. \\(U\\sim\\mathcal{U}(0,1)\\).
- **Empirical checks:** histograms, empirical mean \\(\\hat{\\mu}\\approx 1/2\\), variance \\(\\hat{\\sigma}^2\\approx 1/12\\).
- **Transformation principle (inverse CDF):** if \\(U\\sim\\mathcal{U}(0,1)\\) and \\(X=F^{-1}(U)\\), then \\(X\\) has CDF \\(F\\).

### 2) Law of Large Numbers (LLN)

For i.i.d. \\(X_1,\\ldots,X_N\\) with \\(\\mathbb{E}[|X|]<\\infty\\), the sample mean \\(\\bar{X}_N=\\tfrac{1}{N}\\sum X_i\\) satisfies
\\[
\\bar{X}_N \\xrightarrow{\\text{a.s.}} \\mathbb{E}[X].
\\]
**Diagnostics:** trajectories of \\(\\bar{X}_N\\) vs. \\(N\\), envelope plots, running error \\(|\\bar{X}_N-\\mu|\\).

### 3) Central Limit Theorem (CLT)

If additionally \\(\\mathbb{V}[X]=\\sigma^2<\\infty\\), then
\\[
\\sqrt{N}\\,\\frac{\\bar{X}_N-\\mu}{\\sigma}\\ \\xrightarrow{\\ d\\ }\\ \\mathcal{N}(0,1).
\\]
**Practical consequences:** asymptotic \\(100(1-\\alpha)\\%\\) confidence interval (CI) for \\(\\mu\\):
\\[
\\bar{X}_N \\pm z_{1-\\alpha/2}\\,\\frac{\\hat{\\sigma}}{\\sqrt{N}}.
\\]

### 4) Monte Carlo Integration

To approximate \\(I=\\mathbb{E}[g(X)]\\) with \\(X\\sim p\\), use
\\[
\\hat{I}_N = \\frac{1}{N}\\sum_{i=1}^{N} g(X_i),\\qquad \\mathbb{E}[\\hat{I}_N]=I,\\quad
\\mathbb{V}[\\hat{I}_N]=\\frac{\\mathbb{V}[g(X)]}{N}.
\\]
- **Geometric examples:** area/volume by uniform sampling (e.g., estimate of \\(\\pi\\) via quarter‑circle; volume of a sphere by proportion of points under the surface).
- **Error rate:** RMSE \\(\\sim \\mathcal{O}(N^{-1/2})\\). Use CLT to build CIs for integrals.

### 5) Rejection Sampling

**Uniform subset rejection.** Let \\(A\\subset D\\). Draw \\(X_i\\sim\\mathcal{U}(D)\\) until \\(X_i\\in A\\), return \\(X_\\tau\\).
Then \\(X_\\tau\\sim\\mathcal{U}(A)\\). The number of draws \\(\\tau\\) is geometric with success prob. \\(p=|A|/|D|\\).  
**General rejection with envelope.** Suppose target density \\(f\\) and proposal \\(g\\) with \\(f(x)\\le C\\,g(x)\\) for all \\(x\\).  
Algorithm: sample \\(Y\\sim g\\), \\(U\\sim\\mathcal{U}(0,1)\\). Accept \\(Y\\) if \\(U\\le f(Y)/(C g(Y))\\); else repeat.  
- **Correctness:** accepted samples have density \\(f\\).
- **Efficiency:** acceptance rate \\(=1/C\\). Choosing tight \\(C\\) or better \\(g\\) improves performance.

### 6) Mixture Distributions

Let \\(\\{f_k\\}_{k=1}^K\\) be component densities with weights \\(\\{p_k\\}\\), \\(\\sum p_k=1\\). The mixture density is
\\(f(x)=\\sum_{k=1}^K p_k f_k(x)\\).  
**Sampling:** draw component index \\(Z\\sim\\text{Categorical}(p_1,\\dots,p_K)\\), then draw \\(X\\sim f_Z\\).  
**Example:** mixture of exponentials; histograms should reveal a heavier head/two‑slope log‑pdf.

