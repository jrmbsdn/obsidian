---
title: Entropy-Gradient Grounding: Training-Free Evidence Retrieval in Vision-Language Models
authors: [[Marcel Gröpl]], [[Jaewoo Jung]], [[Seungryong Kim]], [[Marc Pollefeys]], [[Sunghwan Hong]]
year: 2026
journal: arXiv
tags: [#research/review, #status/processed]
cite_key: gropl2026entropy
---

# [[Entropy-Gradient Grounding: Training-Free Evidence Retrieval in Vision-Language Models]]

> [!ABSTRACT] Overview
> This paper addresses the persistent failure of Vision-Language Models (VLMs) in tasks requiring fine-grained visual details or evidence spread across disjoint regions. The authors propose a "novel," training-free, and model-intrinsic grounding method that frames inference as test-time evidence acquisition guided by the model's own uncertainty. By backpropagating the Shannon entropy of the next-token distribution to visual embeddings, the method identifies and zooms into decision-relevant regions, ultimately achieving consistent performance improvements across seven benchmarks and four VLM architectures.

## 1. Detailed Technical Analysis

*The study treats visual grounding not as a post-hoc explanation but as an active perception task where the model identifies where to look next to resolve predictive ambiguity.*

- **Theoretical Framework:** The authors build upon **active perception** and **gradient-based attribution** theories. They utilize the **Shannon entropy** of the model's next-token distribution as a model-intrinsic signal, measuring the sensitivity of predictive uncertainty to specific visual features.
- **Detailed Methodology:**
    - **Study Design:** A test-time evidence retrieval loop involving iterative "zoom-and-reground" steps.
    - **Sample Size ($N$):** Evaluation conducted on seven standard benchmarks (TextVQA, V*, DocVQA, POPE, InfoQA, GQA, RWQA) across four backbones: LLaVA-1.5 7B, LLaVA-1.6 Mistral 7B, InternVL-3.5 8B, and Qwen2.5-VL 7B.
    - **Measurement Tools:** Grounding is derived from the gradient of the entropy objective $\mathcal{L}_{\mathrm{ent}}$ with respect to visual embeddings $\mathbf{V}$. An adaptive **elbow method** is used for parameter-free thresholding of saliency maps.
- **Process Logic:**
    1.  **Entropy Calculation:** Compute Shannon entropy $H_t(I, P)$ for the first generated token.
    2.  **Backpropagation:** Calculate the entropy-gradient $\mathbf{G} = \frac{\partial \mathcal{L}_{\mathrm{ent}}}{\partial \mathbf{V}}$ to identify tokens that most affect uncertainty.
    3.  **Multi-Region Extraction:** Convert gradients into saliency scores $s_i = \left \lVert \mathbf{G}_i \right \rVert _2$, smooth with a Gaussian filter, and extract connected components as Regions of Interest (ROIs).
    4.  **Iterative Refinement:** Re-ground and re-crop selected regions, regulated by a **spatial-entropy** stopping criterion to prevent over-refinement.

## 2. Exhaustive Data & Findings

> [!SUCCESS] Evidence & Metrics
> - **Performance Gains:** The method yielded the largest gains on detail-critical benchmarks:
>     - **V***: Significant improvements across all backbones (e.g., $+15.71$ for LLaVA-1.6, $+19.89$ for InternVL 3.5).
>     - **DocVQA**: Substantial ANLS score increases (e.g., $+11.38$ for LLaVA-1.5, $+20.81$ for InternVL 3.5).
>     - **TextVQA**: Notable accuracy boosts (e.g., $+6.56$ for LLaVA-1.5, $+14.82$ for InternVL 3.5).
> - **Qualitative Accuracy:** Quantitative localization metrics (IoU) on TextVQA showed $0.29$ for the proposed method vs. $0.14$ for ViCrop.
> - **Statistical Trends:** The authors observe that newer/stronger backbones produce "strikingly" cleaner and more accurately localized entropy-gradient maps.
> - **Ablation Results:** Performance peaks at $K=2$ salient regions, suggesting that multi-region aggregation is "crucial" for complex document and compositional queries.

## 3. The Author's Perspective: Significance & Innovation

> [!QUOTE] What the Authors Find Notable
> - **Novelty:** The authors claim a "**novel**," "**model-intrinsic**" signal that is "tightly coupled to the model's prediction behavior" without requiring auxiliary detectors or attention heuristics.
> - **Unexpected Results:** They describe the finding that probability-based confidence measures are "**inconsistent**" for stopping control, whereas spatial entropy provides a "**stable convergence signal**."
> - **Impact:** The authors state this approach "**changes the current understanding**" by demonstrating that VLMs already possess the internal signals necessary for high-precision grounding, which can be unlocked through test-time optimization.

## 4. Limitations & Institutional Constraints

> [!FAILURE] Acknowledged Weaknesses
> - **Interpretation Failure:** Correct localization is "**not sufficient**" if the downstream model still misinterprets the visual content (e.g., errors persist even with detailed crops).
> - **Backbone Dependency:** The method "ultimately relies on the spatial signals produced by the underlying model"; if the backbone ignores a region, the grounding cannot recover it.
> - **Computational Overhead:** Iterative refinement increases inference time (e.g., from $\sim 1.00s$ to $\sim 3.10s$ on LLaVA-1.5).
> - **Future Research:** The authors recommend investigating why models still fail to reason correctly even when provided with high-resolution evidence.

## 5. Metadata & Connections
- **Key Concepts:** [[Vision-Language Models]], [[Visual Grounding]], [[Active Perception]], [[Shannon Entropy]], [[Test-Time Optimization]]
- **Related Work:** [[ViCrop]] (precursor/baseline), [[SEAL]] (training-based comparison), [[TEVA]] (training-based baseline), [[LLaVA-1.5]], [[LLaVA-1.6]].