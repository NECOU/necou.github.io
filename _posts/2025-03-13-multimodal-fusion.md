---
layout: post
title: "Optimizing Multimodal Fusion: Selective Parameter Merging between Vision-Language and Language Models"
date: 2025-3-13
categories: research
---
## Key Findings

In our exploration of merging Vision-Language Models (VLMs) with traditional Language Models (LLMs), we've uncovered fascinating insights that challenge conventional wisdom about model fusion. Rather than indiscriminately combining all parameters from both models, we found that a more nuanced, selective approach yields dramatically better results. The most effective strategy preserves the VLM's original embedding layers while only merging the self-attention mechanisms—a finding that aligns with our understanding of how these models process visual information.

Perhaps even more intriguing is how the optimal merging strategy changes throughout the network's depth. Our experiments reveal that early layers benefit most from maintaining the VLM's original parameters, while deeper layers can incorporate more influence from the LLM's specialized language capabilities. This layer-wise progressive approach consistently outperformed uniform merging strategies across our benchmarks.


## Background: Model Fusion in Multimodal Systems

When a VLM and LLM share the same base architecture (e.g., Qwen2.5-VL-7B and Qwen2.5-7B-Instruction), they often have compatible language processing components. A VLM typically consists of a vision encoder plus the LLM architecture, making partial parameter merging possible.

Conventional approaches to model merging include techniques like parameter averaging, task arithmetic, and adapter fusion. However, these methods often treat all parameters equally or focus on task-specific adaptation. Our research investigates a more nuanced approach based on how different components of these models process multimodal information.

![Layer Weights Distribution](/assets/images/p0/5.layer_weights_distribution.png)
*Figure 1: Illustration of the three different weight distribution strategies across model layers. Method 1 applies a constant weight, while Methods 2 and 3 progressively increase LLM influence in deeper layers.*

## Research Hypotheses

Based on our understanding of how VLMs and LLMs process information, we formulated two key hypotheses:

**Hypothesis 1: Selective Component Fusion**  
We hypothesized that merging only self-attention tensors while preserving the VLM's original embedding tensors would yield better results than merging all language model components. This is because embedding tensors in the VLM are specifically optimized to handle the joint distribution of text and visual tokens, while the LLM's embeddings have only been exposed to text.

**Hypothesis 2: Layer-wise Progressive Merging**  
We expected that applying different merging weights across layers (with greater influence from the VLM in early layers and more LLM influence in deeper layers) would outperform uniform merging. This is based on the observation that early layers process visual tokens closer to their raw form, while deeper layers work with more abstract representations that might benefit from the LLM's language understanding capabilities.

## Experimental Setup

### Models and Datasets
- Base VLM: Qwen2.5-VL-7B-Instruct
- Component LLM: Qwen2.5-7B-Instruct
- Evaluation Datasets: MATH-V and Math-Visita mini (both involving visual-textual reasoning)

### Merging Settings
We compared two different approaches to parameter selection:
- **Setting 1**: Merging all LLM tensors including embedding tensors, layer norms, and MLPs
- **Setting 2**: Merging only self-attention tensors in each layer

### Merging Methods
We explored three different strategies for determining the merging weights:
- **Method 1**: Global Weight Merging - applying a single global weight ratio across all merged parameters
- **Method 2**: Linear Layer-wise Progressive Merging - linearly increasing the LLM's influence from early to deeper layers
- **Method 3**: Loglinear Layer-wise Progressive Merging - increasing the LLM's influence on a logarithmic scale across layers

### Weight Notation
For our experiments, we used a standard interpolation formula for model merging:

$(1-\alpha)\theta_\text{VLM} + \alpha\theta_\text{LLM} = \theta_\text{Merged VLM}$

Where α represents the weight given to the LLM parameters:
- α = 0.4: 60% VLM + 40% LLM influence
- α = 0.5: 50% VLM + 50% LLM influence 
- α = 0.6: 40% VLM + 60% LLM influence

This formula is applied to the respective tensors being merged. For example, with α = 0.4, each merged tensor is a weighted combination where the original VLM tensor contributes 60% and the LLM tensor contributes 40% to the final value.

## Results and Analysis

### Overall Performance Comparisons

Our experiments revealed several key patterns across different configurations of settings, methods, and weight ratios:

![Performance Heatmap](/assets/images/p0/4.performance_heatmap.png)
*Figure 2: Heatmap visualization of performance across all experimental configurations. The color intensity represents accuracy, with darker blues indicating higher performance.*

### Hypothesis 1: Selective Component Fusion

Setting 2 (self-attention only) consistently outperformed Setting 1 (all tensors) across most method and weight combinations, with particularly strong improvements on the Math-Vista mini dataset.

![Settings Comparison](/assets/images/p0/1.settings_comparison.png)
*Figure 3: Direct comparison of Setting 1 vs Setting 2 across different method-weight combinations. Setting 2 generally achieves higher accuracy, especially on the Math-Vista mini dataset.*

The improvement is especially clear when we look at the direct performance delta between Settings 2 and 1:

![Delta Improvement](/assets/images/p0/2.delta_improvement.png)
*Figure 4: Performance improvement of Setting 2 over Setting 1 for each configuration. Positive values (blue) indicate where Setting 2 outperforms Setting 1.*

As shown in Figure 4, Setting 2 outperforms Setting 1 in 16 out of 18 comparisons across both datasets. For MATH-V, Setting 2 shows improvements in 7 out of 9 cases with an average improvement of 0.94 percentage points overall. The improvement is particularly strong and consistent for Math-Vista mini, where Setting 2 outperforms Setting 1 in all 9 comparisons with an average gain of 3.93 percentage points.

This strongly supports our first hypothesis that preserving the VLM's embedding tensors while only merging self-attention components leads to better performance. The magnitude of improvement is especially notable in configurations that require stronger visual reasoning capabilities (Math-Vista mini), suggesting that preserving the VLM's embedding tensors is indeed critical for maintaining its ability to process visual information effectively.

### Hypothesis 2: Layer-wise Progressive Merging

Our second hypothesis predicted that layer-wise increasing methods (Methods 2 and 3) would outperform global weight fusion (Method 1). The results show substantial support for this hypothesis, with a 79.2% success rate across all configurations and datasets:

![Methods by Weight](/assets/images/p0/3.methods_by_weight.png)
*Figure 5: Performance trends across different weight values for each method. Methods 2 and 3 often outperform Method 1 and show more graceful degradation at higher weights.*

The advantage of layer-wise methods is even more pronounced in Setting 2 (the more effective setting overall), with an 83.3% success rate. This trend is particularly notable at higher LLM weight values (0.5 and 0.6), where Methods 2 and 3 consistently outperform Method 1 across both datasets.

The data reveals three important patterns that support Hypothesis 2:

1. Methods 2 and 3 show better resilience at higher weight values (0.6), degrading more gracefully than Method 1, which aligns with our theoretical understanding.

2. In Setting 2, Method 3 (loglinear layer-wise) achieves the best overall performance, suggesting that a more gradual introduction of LLM influence in deeper layers is optimal.

3. The layer-wise approaches generally perform better on Math-Vista mini, suggesting they better preserve visual reasoning capabilities when incorporating more LLM influence.

### Optimal Weight Balance

Across most methods and settings, a weight value of 0.4 (60% VLM + 40% LLM influence) produces the best results. The best overall configuration is Setting 2, Method 3, with weight 0.4, which achieves:
- 25.46% on MATH-V (1.51 points above baseline)
- 69.8% on Math-Vista mini (closest to the baseline of 70.9%)

This confirms our finding that maintaining stronger VLM influence while selectively incorporating LLM capabilities yields optimal performance for multimodal reasoning tasks.

## Discussion

Our findings demonstrate that intelligent, selective parameter merging between VLMs and LLMs can produce better multimodal models than naive merging approaches. The results support several key insights:

1. **Embeddings Matter**: Preserving the VLM's embedding tensors is crucial for maintaining the model's ability to process visual information effectively.

2. **Component-Specific Merging**: Not all model components benefit equally from fusion; self-attention mechanisms appear to be the most promising candidates for merging.

3. **Layer-Dependent Fusion**: The optimal merging strategy varies across layers, with early layers benefiting from stronger VLM influence and deeper layers accommodating more LLM contribution.

4. **Moderation in Merging**: Even in the best-performing configurations, the VLM's parameters should maintain greater influence (approximately 60%) for optimal multimodal performance.

5. **Task-Dependent Effects**: The two datasets show different sensitivity patterns to the merging strategies, suggesting that visual reasoning (Math-Vista mini) benefits more consistently from Setting 2 and Methods 2/3 than mathematical reasoning (MATH-V).

## Limitations and Future Work

While our initial results are promising, several limitations and areas for future research remain:

1. **Task Diversity**: Our evaluation currently focuses on mathematical reasoning with visual context. Testing on a broader range of multimodal tasks would strengthen the generalizability of our findings.

2. **Architecture Exploration**: Extending these experiments to different model architectures could reveal whether these patterns hold across different model families.

3. **Fine-Tuning Comparison**: Comparing our fusion approach with fine-tuning strategies would provide a more comprehensive understanding of the trade-offs involved.

4. **Parameter-Specific Analysis**: A more granular investigation of which specific attention components benefit most from merging could further refine our approach.

## Conclusion

Our research demonstrates that selectively merging self-attention components while preserving VLM embeddings, combined with a layer-wise progressive merging strategy, offers a promising approach to creating more capable multimodal AI systems. By understanding the distinct roles of different components in processing visual and textual information, we can design more effective fusion strategies that leverage the strengths of both specialized and multimodal models.

This work represents a step toward more principled approaches to model merging in the multimodal domain, with potential applications in fields requiring sophisticated visual-language understanding like medical imaging analysis, educational technology, and multimodal reasoning systems.

