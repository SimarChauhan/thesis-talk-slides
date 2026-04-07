# Thesis Talk Full Presenter Script (20 Minutes)

## Slide 1 - Title
"Good [morning/afternoon], my name is Simranjeet Singh. Thank you for attending my thesis presentation. The title is *Measuring Self-Consistent Errors in Large Language Models*.  
The core problem I study is simple: some model errors are not random. They are repeated confidently, which makes them difficult to detect with standard uncertainty checks.  
In this talk, I will show three things: how common these errors are, how we can detect them using proxy probing even for API-only models, and how these errors change across model generations."

## Slide 2 - Introduction
"LLMs are now used in domains like healthcare, legal support, and education. In these settings, a fluent but false answer can be harmful.  
The specific failure mode here is *self-consistent error*, where a model gives the same wrong idea repeatedly. That repeated consistency can look like confidence and reliability, but it is still wrong."

## Slide 3 - Introduction (Research Questions)
"This thesis addresses three research questions.  
RQ1: How prevalent are self-consistent errors across frontier models?  
RQ2: Can small proxy models detect these errors without hidden-state access to target models?  
RQ3: Do self-consistent errors naturally decrease as model families evolve?  
I now move to background and what prior work did not yet cover."

## Slide 4 - Table of Contents
"The structure is background, methodology, results for O1 to O3, then impact, limitations, validation, and conclusions.  
I will keep the discussion grounded in the quantitative tables shown in the deck."

## Slide 5 - Background and Related Work
"First, terminology. A CE is not just a wrong answer. It is a wrong answer that remains semantically consistent across stochastic samples.  
This distinction matters because most reliability signals treat variation as uncertainty, but CE has low variation and still fails factually.  
In related work, TruthfulQA gave a strong factuality benchmark, SelfCheckGPT used sampling consistency, and internal-state probing work showed hidden activations carry error signals.  
Tan et al. formally showed CEs are difficult for many existing detectors, which motivates this thesis directly."

## Slide 6 - Background and Related Work (Gap)
"The research gap has three concrete parts.  
First, most CE evidence was on open models, while many production systems rely on closed API-only models where hidden states are unavailable.  
Second, prior probing methods often assume direct access to target internals; that assumption breaks for API-only targets.  
Third, prior studies were mostly cross-sectional snapshots, not version-evolution analyses across multiple generations in a family.  
So, before this thesis, we did not have a unified answer to: how common CEs are in frontier mixed-access models, whether proxy probing works without target internals, and whether newer releases naturally reduce CE.  
This thesis addresses that exact combined gap with one pipeline and one evaluation framework."

## Slide 7 - Methodology
"The pipeline has two phases.  
Phase one collects model outputs: greedy plus sampled responses.  
Phase two evaluates correctness and semantic equivalence, and assigns categories such as CE and inconsistent error.  
This enables consistent measurement across all targets."

## Slide 8 - Example Error Classification
"This example shows how a model can be confidently wrong.  
The key point is semantic stability: if all sampled responses match the same wrong idea, it is a CE.  
So consistency by itself is not a sufficient reliability signal."

## Slide 9 - Methodology (Tan et al. original O2)
"Before my adaptation, here is the original Tan et al. cross-model probe.  
The same question and answer are viewed from two sides: the response model and a verifier model.  
Each side produces a score, and those scores are fused with a lambda weight into one CE risk score.  
Their setup assumes you can access hidden states from both models."

## Slide 10 - Methodology (Proxy adaptation)
"My contribution is not replacing the fusion idea.  
Several of my frontier targets are API-only, so I cannot use their hidden states. DeepSeek was also too large to load locally.  
I therefore use small local proxy models on the same text for the response-side signal, keep the same verifier and fusion rule, and still get a fused CE score.  
The comparison strip shows target hidden states are no longer required for that branch."

## Slide 11 - Methodology (Evolution and Validity)
"For O3, I track four generations in Grok, Llama, and Qwen families.  
I also disclose threats to validity, including smaller test partitions for some targets and benchmark scope limits.  
This keeps interpretation transparent."

## Slide 12 - Results Overview
"At a high level, there are three findings.  
One, CEs are common.  
Two, proxy probing performs strongly.  
Three, CE trends do not automatically follow accuracy improvements."

## Slide 13 - O1 Prevalence
"Main O1 result: 42.1 percent of incorrect answers are self-consistent at t equals 1.0.  
CE rates vary by model, but no model is CE-free.  
So this is not an edge case - it is a systematic reliability issue."

## Slide 14 - O1 Cross-Model Overlap and Categories
"Pairwise shared CE overlap is about 22 to 36 percent Jaccard, mean under 28 percent, so roughly two thirds to three quarters of CE mass is not shared across models.  
Open-weight pairs overlap more than closed-open pairs. Of 720 shared-CE instances, about three quarters repeat the same wrong answer.  
Category analysis shows misconception-heavy categories are especially CE-prone."

## Slide 15 - O2 White-Box Probe Performance (Config 1)
"Config 1 uses SmolLM3-3B with a Phi-4-mini verifier.  
In this table, each row shows target AUROC, verifier AUROC, fused AUROC, lambda, and split composition per model for train, validation, and test.  
The key outcome is strong fused performance, with mean around 0.918."

## Slide 16 - O2 White-Box Probe Performance (Config 2)
"Config 2 replaces the response proxy with Qwen3.5-4B while keeping the same verifier.  
Performance remains very similar, with mean fused AUROC around 0.919.  
This suggests the approach is not tied to one specific small proxy-model family."

## Slide 17 - O2 Baseline Comparison
"Here I compare proxy results against original direct hidden-state extraction where feasible.  
The mean row pools all five baseline runs: mean fused CE AUROC is about 0.90, still close to the proxy means near 0.92.  
That supports proxy probing when direct access is unavailable."

## Slide 18 - O3 Version-Evolution Study
"This is the full version-evolution table, including release dates and all metrics.  
Only Grok shows monotonic CE reduction across versions. Llama and Qwen are non-monotonic.  
So accuracy increases and CE reduction are not equivalent trends."

## Slide 19 - What's New
"What is new is the combined evidence across all three objectives in one framework.  
The thesis extends CE analysis to API-only targets, validates proxy probing, and adds longitudinal tracking."

## Slide 20 - Impact on Theory and Practice
"Theory impact: CE and accuracy are partially orthogonal.  
Practice impact: consistency-only monitoring can miss dangerous failures.  
A deployable alternative is periodic proxy-based CE risk auditing."

## Slide 21 - Limitations
"The main limitations are benchmark scope and smaller test sets for a few targets, which increase AUROC variance.  
These are important caveats, and they motivate broader replication."

## Slide 22 - Validation
"I validate with a multi-judge correctness pipeline, strict split hygiene, and seed-based stability checks.  
I also compare two proxy configurations and direct baselines where possible."

## Slide 23 - Conclusions
"Three conclusions:  
First, CEs are common across frontier models.  
Second, proxy probing can detect CE risk effectively without hidden-state access.  
Third, CE reduction is not automatic and requires explicit targeting."

## Slide 24 - Future Work and Close
"Future work includes multi-benchmark replication, deeper category-level analysis, and intervention strategies to reduce CE directly.  
Thank you for your time. I am happy to take questions."

## References slide
"If needed during Q and A, the next slide lists full bibliography entries in one place."

## Timing Guide
- Slides 1-4: 3 minutes
- Slides 5-11: ~6 minutes (methodology includes Tan original + proxy + O3)
- Slides 12-18: 7 minutes (results)
- Slides 19-24: 4.5 minutes (wrap-up; then references and thank-you)
