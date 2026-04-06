---
theme: default
title: "Measuring Self-Consistent Errors in LLMs and Detecting Them via Proxy Cross-Model Probing"
class: hero
highlighter: shiki
mdc: true
fonts:
  sans: Space Grotesk
  serif: Lora
  mono: JetBrains Mono
layout: cover
---

# Measuring Self-Consistent Errors in Large Language Models
## and Detecting Them via Proxy Cross-Model Probing

<div class="meta-grid">
<div>
<b>Simranjeet Singh</b><br>
CS4490 - Undergraduate Thesis<br>
Department of Computer Science, Western University
</div>
<div>
Thesis Supervisor: Prof. Lucian Ilie<br>
Course Coordinator: Prof. Nazim Madhavji<br>
April 2026
</div>
</div>

---

# Introduction

## Context, problem, and research gap

<div class="two-col" style="gap:1.2rem">
<div>

- LLMs deployed in healthcare, legal, finance - a confidently stated falsehood causes real harm <span class="cite">[Huang et al., 2025; Ji et al., 2023]</span>.
- <span class="kw">Hallucination</span>: fluent, confident, factually wrong output <span class="cite">[Ji et al., 2023]</span>.
- **Research gap** <span class="cite">[Tan et al., 2025]</span>: prior CE work focused on open-weight models, did not evaluate API-only frontier targets, did not use proxy-only probing for hidden-state-inaccessible targets, and did not track CE longitudinally across model generations.

<div class="callout warn" style="margin-top:0.6rem">
  <b>Definition - <span class="kw">Self-Consistent Error (CE)</span></b> <span class="cite">[Tan et al., 2025]</span>:<br>
  Greedy answer a<sub>g</sub> is <em>incorrect</em> and all stochastic samples are semantically equivalent to a<sub>g</sub>. Confidently wrong, no variation - detectors classify it as <em>reliable</em>.
</div>

</div>
<div>

<div class="error-type-diagram">
  <div class="et-title">Two types of error</div>
  <div class="et-row">
    <div class="et-icon ie-icon">⚡</div>
    <div class="et-body">
      <b><span class="kw">Inconsistent Error (IE)</span></b><br>
      <span class="et-samples"><span class="s-wrong">✗ A</span> <span class="s-diff">✗ B</span> <span class="s-diff">✗ C</span> <span class="s-diff">✗ D</span></span><br>
      <small>Samples vary → detectable by consistency check</small>
    </div>
  </div>
  <div class="et-row et-highlight">
    <div class="et-icon ce-icon">🔴</div>
    <div class="et-body">
      <b><span class="kw">Self-Consistent Error (CE)</span></b><br>
      <span class="et-samples"><span class="s-wrong">✗ A</span> <span class="s-same">✗ A</span> <span class="s-same">✗ A</span> <span class="s-same">✗ A</span></span><br>
      <small>All samples identical → <b>invisible</b> to consistency check</small>
    </div>
  </div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Huang et al., 2025]</span> Huang, L. et al. A Survey on Hallucination in LLMs. <em>ACM TOIS, 43</em>(2), pp. 1–55, 2025. &nbsp;
<span class="cite">[Ji et al., 2023]</span> Ji, Z. et al. Survey of Hallucination in NLG. <em>ACM Computing Surveys, 55</em>(12), pp. 1–38, 2023.<br>
<span class="cite">[Manakul et al., 2023]</span> Manakul, P. et al. SelfCheckGPT. <em>EMNLP</em>, pp. 9004–9017, Singapore, 2023. &nbsp;
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Introduction

## Research questions, results attained, novelty, and impact

**Three RQs addressed:**

<div class="rq-grid">
<div class="rq-card"><div class="rq-num">RQ1</div> How prevalent are CEs across closed-source and open-weight frontier LLMs?</div>
<div class="rq-card"><div class="rq-num">RQ2</div> Can small <span class="kw">proxy encoders</span> detect CEs for API-only targets without hidden-state access?</div>
<div class="rq-card"><div class="rq-num">RQ3</div> Do CEs decrease as model families evolve over successive generations?</div>
</div>

<div class="findings-strip">
  <div class="finding-pill"><span class="finding-num">42.1%</span>of wrong answers are CE</div>
  <div class="finding-pill"><span class="finding-num">0.92</span>mean fused AUROC</div>
  <div class="finding-pill"><span class="finding-num">17.7→0.5%</span>Grok CE trajectory</div>
</div>

**Novelty** <span class="cite">[Tan et al., 2025]</span>: first CE measurement on closed-source API models; first proxy-only CE probing for API-only frontier targets without target hidden-state access; first longitudinal CE tracking across model generations in this CE setting.

**Impact:** CEs can evade sampling-based consistency monitoring <span class="cite">[Manakul et al., 2023; Tan et al., 2025]</span>. Proxy probing enables CE monitoring for API-served models without access to their internals <span class="cite">[Tan et al., 2025]</span>.

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[Manakul et al., 2023]</span> Manakul, P. et al. SelfCheckGPT. <em>EMNLP</em>, pp. 9004–9017, Singapore, 2023.
</div>

---

# Table of Contents

<div class="toc-grid">
<div class="toc-item done"><span class="toc-n">1</span> Introduction</div>
<div class="toc-item"><span class="toc-n">2</span> Background & Related Work</div>
<div class="toc-item"><span class="toc-n">3</span> Research Methodology</div>
<div class="toc-item"><span class="toc-n">4</span> Results</div>
<div class="toc-item"><span class="toc-n">5</span> What's New?</div>
<div class="toc-item"><span class="toc-n">6</span> Impact on Theory & Practice</div>
<div class="toc-item"><span class="toc-n">7</span> Limitations</div>
<div class="toc-item"><span class="toc-n">8</span> Validation</div>
<div class="toc-item"><span class="toc-n">9</span> Conclusions</div>
<div class="toc-item"><span class="toc-n">10</span> Future Work & Lessons Learnt</div>
<div class="toc-item extra"><span class="toc-n">+</span> Extra Material</div>
</div>

---

# Background & Related Work

## LLM hallucination, TruthfulQA, and CE

<div class="two-col" style="gap:1rem">
<div>

- **Hallucination** <span class="cite">[Ji et al., 2023]</span>: fluent, factually unsupported output. This thesis focuses on <span class="kw">factuality hallucinations</span> in closed-book QA.
- **<span class="kw">TruthfulQA</span>** <span class="cite">[Lin et al., 2022]</span>: 817 questions across 38 categories targeting <span class="kw">imitative falsehoods</span> - popular but incorrect beliefs. Unlike TriviaQA <span class="cite">[Joshi et al., 2017]</span>, it targets confident misinformation. This thesis uses 807 questions.
- **CE formalization** <span class="cite">[Tan et al., 2025]</span>: CE does *not* decrease with model scale; existing detectors degrade substantially on CEs.
- **<span class="kw">SelfCheckGPT</span>** <span class="cite">[Manakul et al., 2023]</span>: sampling-based consistency methods can miss CEs, because repeated wrong answers may still appear consistent and therefore reliable.

</div>
<div>

<div class="tqa-visual">
  <div class="tqa-header">TruthfulQA example <span class="cite">[Lin et al., 2022]</span></div>
  <div class="tqa-q">❓ <em>"What happens if you swallow gum?"</em></div>
  <div class="tqa-row correct"><span class="tqa-label ok">✅ Correct</span> Gum passes through the digestive system normally.</div>
  <div class="tqa-row ie"><span class="tqa-label ie-l">⚡ IE</span> Answers vary: "stays 7 years" / "causes blockage" / "dissolves slowly"</div>
  <div class="tqa-row ce"><span class="tqa-label ce-l">🔴 CE</span> All 10 samples: <em>"stays in your stomach for seven years"</em></div>
  <div class="tqa-note">A consistency detector sees perfect agreement on the CE → labels it <b>reliable</b></div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Ji et al., 2023]</span> Ji, Z. et al. Survey of Hallucination in NLG. <em>ACM Computing Surveys, 55</em>(12), pp. 1–38, 2023. &nbsp;
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA. <em>ACL</em>, pp. 3214–3252, 2022.<br>
<span class="cite">[Joshi et al., 2017]</span> Joshi, M. et al. TriviaQA. <em>ACL</em>, pp. 1601–1611, 2017. &nbsp;
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[Manakul et al., 2023]</span> Manakul, P. et al. SelfCheckGPT. <em>EMNLP</em>, pp. 9004–9017, Singapore, 2023.
</div>

---

# Background & Related Work

## Internal-state probing and the research gap

<div class="two-col" style="gap:1rem">
<div>

- **<span class="kw">Hidden-state probing</span>** <span class="cite">[Azaria & Mitchell, 2023]</span>: classifiers on activations distinguish true from false statements, outperforming output-probability baselines.
- **Factuality probes** <span class="cite">[Han et al., 2025]</span>: lightweight probes on hidden states achieve competitive performance with expensive multi-sample detectors at much lower inference cost.
- **Cross-model probe** <span class="cite">[Tan et al., 2025]</span>: a second "verifier" model picks up error signals the response model misses. Fused score: s = (1−λ)s_M + λs_V.
- **<span class="kw">Semantic entropy</span>** <span class="cite">[Farquhar et al., 2024; Kossen et al., 2024]</span>: meaning-level uncertainty catches confabulations, approximable from hidden states.

</div>
<div>

**Research gap - this thesis adds the three missing columns:**

<div class="gap-visual">
<div class="gap-row header"><span>Study</span><span>CE</span><span>API</span><span>Proxy-only</span><span>Longit.</span></div>
<div class="gap-row"><span>Lin et al., 2022</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span></div>
<div class="gap-row"><span>Azaria et al., 2023</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span></div>
<div class="gap-row"><span>Manakul et al., 2023</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span></div>
<div class="gap-row"><span>Tan et al., 2025</span><span class="gc">✓</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span></div>
<div class="gap-row this-thesis"><span><b>This thesis</b></span><span class="gc">✓</span><span class="gnew">✓</span><span class="gnew">✓</span><span class="gnew">✓</span></div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Azaria & Mitchell, 2023]</span> The Internal State of an LLM Knows When It's Lying. <em>Findings of EMNLP</em>, pp. 967–976, 2023. &nbsp;
<span class="cite">[Han et al., 2025]</span> Simple Factuality Probes. <em>Findings of EMNLP</em>, pp. 16209–16226, Suzhou, 2025.<br>
<span class="cite">[Farquhar et al., 2024]</span> Detecting Hallucinations Using Semantic Entropy. <em>Nature, 630</em>, pp. 625–630, 2024. &nbsp;
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Research Methodology

## Two-phase black-box pipeline (O1)

<div class="pipeline-visual">
  <div class="pipe-box phase1">
    <div class="pipe-label">Phase 1</div>
    <div class="pipe-title">Data Collection</div>
    <div class="pipe-detail">6 models × 807 Qs<br>1 greedy + 10 stochastic<br>→ 4,842 JSONL rows</div>
  </div>
  <div class="pipe-arrow">→</div>
  <div class="pipe-box phase2">
    <div class="pipe-label">Phase 2</div>
    <div class="pipe-title">Evaluation & Labeling</div>
    <div class="pipe-detail">① 3-judge correctness<br>② Hybrid NLI equivalence<br>③ RC / FC / CE / IE label</div>
  </div>
  <div class="pipe-arrow">→</div>
  <div class="pipe-box wbbox">
    <div class="pipe-label">O2</div>
    <div class="pipe-title">WB Probe</div>
    <div class="pipe-detail">Hidden states<br>→ CE detection</div>
  </div>
</div>

<div class="two-col" style="margin-top:0.7rem; gap:0.8rem">
<div>

**Six frontier models evaluated:**

<div class="model-chips">
<span class="chip closed">Claude Opus 4.6</span>
<span class="chip closed">GPT-5.2</span>
<span class="chip closed">Grok 4</span>
<span class="chip open">DeepSeek V3.2</span>
<span class="chip open">Llama 4 Mav. 17B</span>
<span class="chip open">Qwen3 Next 80B</span>
</div>
<div class="chip-legend"><span class="chip closed">■</span> Closed-source &nbsp; <span class="chip open">■</span> Open-weight</div>

</div>
<div>

**Semantic equivalence - hybrid two-tier:**

<div class="nli-diagram">
  <div class="nli-step"><span class="nli-pct">96–98%</span> DeBERTa NLI <span class="cite">[He et al., 2021]</span><br><small>High-confidence pairs (≥0.70 or ≤0.30)</small></div>
  <div class="nli-step nli-fallback"><span class="nli-pct">2–4%</span> GPT-5.2 fallback<br><small>Borderline / ambiguous cases</small></div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA. <em>ACL</em>, pp. 3214–3252, 2022. &nbsp;
<span class="cite">[He et al., 2021]</span> He, P. et al. DeBERTa. <em>ICLR</em>, 2021. &nbsp;
<span class="cite">[Anthropic, 2026; OpenAI, 2026; xAI, 2025; DeepSeek, 2025; Meta, 2025; Qwen Team, 2026]</span> Official model cards.
</div>

---

# EXAMPLE - Error Classification

## The 2×2 grid and a concrete CE instance

<div class="two-col">
<div>

Each (question, model) pair classified by two binary criteria:

<div class="matrix-grid">
<div class="cell rc"><b>Reliably Correct</b><br><small>Correct + Consistent</small></div>
<div class="cell fc"><b>Fragile Correct</b><br><small>Correct + Varies</small></div>
<div class="cell ce"><b>Self-Consistent Error</b><br><small>Wrong + Consistent</small><br>⚠ MOST DANGEROUS</div>
<div class="cell ie"><b>Inconsistent Error</b><br><small>Wrong + Varies</small></div>
</div>

<div style="margin-top:0.5rem; font-size:0.78rem">
Threshold t = 1.0 → <b>all 10</b> samples must match greedy to count as CE.
</div>

</div>
<div>

**Concrete example** <span class="cite">[Tan et al., 2025]</span>:

<div class="example-box">
  <div class="ex-q">❓ <em>"What happens if you swallow gum?"</em></div>
  <div class="ex-correct">✅ <b>Correct answer:</b> gum passes through the digestive system normally.</div>
  <div class="ex-ce-label">🔴 CE - model answers:</div>
  <div class="ex-ce-answer">"It stays in your stomach for seven years"</div>
  <div class="ex-samples">
    <span class="s-badge">S1 ≡</span><span class="s-badge">S2 ≡</span><span class="s-badge">S3 ≡</span>
    <span class="s-badge">S4 ≡</span><span class="s-badge">S5 ≡</span><span class="s-badge">⋯</span>
  </div>
  <div class="ex-detector">Consistency detector: <b class="ok-badge">RELIABLE ✓</b> ← <b>Wrong!</b></div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect: A Study of Self-Consistent Errors in LLMs. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Research Methodology

## White-box cross-model probe (O2)

<div class="two-col" style="gap:0.9rem">
<div>

**Why proxies?** Claude / GPT / Grok are API-only - hidden states inaccessible. DeepSeek too large for Nibi cluster. Key assumption: a small <span class="kw">proxy encoder</span> of the same Q+A text can detect CEs comparably to the target's own hidden states.

**Two configurations:**

| Role | Config 1 | Config 2 |
|---|---|---|
| Response proxy | SmolLM3-3B | Qwen3.5-4B |
| Verifier | Phi-4-mini | Phi-4-mini |

**Score fusion** <span class="cite">[Tan et al., 2025]</span>: &nbsp; $s = (1-\lambda)s_M + \lambda s_V$, &nbsp; λ ∈ [0,1]

</div>
<div>

**<span class="kw">FFN probe</span> architecture:**

<div class="probe-arch">
  <div class="pa-box input">h<br><small>dim d</small></div>
  <div class="pa-arrow">→</div>
  <div class="pa-box hidden">256<br><small>ReLU</small></div>
  <div class="pa-arrow">→</div>
  <div class="pa-box hidden">128<br><small>ReLU</small></div>
  <div class="pa-arrow">→</div>
  <div class="pa-box hidden sm">64</div>
  <div class="pa-arrow">→</div>
  <div class="pa-box output">P(err)<br><small>σ</small></div>
</div>

Best layer per model selected by validation <span class="kw">AUROC</span>.

**Training:** 80/10/10 splits · 3 seeds · 300 epochs · early stop · Adam lr=10⁻³ · Nibi H100 GPUs <span class="cite">[UWaterloo, 2025]</span>

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[UWaterloo, 2025]</span> University of Waterloo / Digital Research Alliance of Canada. Nibi HPC Cluster, 2025.
</div>

---

# Research Methodology

## Version-evolution study (O3) & Threats to Validity

<div class="two-col" style="gap:1rem">
<div>

**Goal:** track CE rates across 4 generations in 3 model families <span class="cite">[xAI; Meta; Qwen Team]</span>.

<div class="evo-timeline">
  <div class="evo-family grok-family">
    <span class="evo-label">Grok</span>
    <span class="evo-v">Grok 3<br><small>Feb '25</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">Grok 4<br><small>Jul '25</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">4.1 FR<br><small>Nov '25</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">4.20β<br><small>Mar '26</small></span>
  </div>
  <div class="evo-family llama-family">
    <span class="evo-label">Llama</span>
    <span class="evo-v">3 8B<br><small>Apr '24</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">3.1 8B<br><small>Jul '24</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">3.3 70B<br><small>Dec '24</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">4 Mav.<br><small>Apr '25</small></span>
  </div>
  <div class="evo-family qwen-family">
    <span class="evo-label">Qwen</span>
    <span class="evo-v">2.5 7B<br><small>Sep '24</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">2.5 72B<br><small>Sep '24</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">3 30B<br><small>Apr '25</small></span>
    <span class="evo-sep">→</span>
    <span class="evo-v">Next 80B<br><small>Sep '25</small></span>
  </div>
</div>

12 × 807 = **9,684 rows** evaluated.

</div>
<div>

**Threats to Validity:**

<div class="threat-list">
  <div class="threat-item">
    <span class="threat-icon">⚠</span>
    <div><b><span class="kw">Small test sets</span></b> - GPT-5.2: 12 items; Grok 4: 16 items → high AUROC variance. Interpret cautiously.</div>
  </div>
  <div class="threat-item">
    <span class="threat-icon">⚠</span>
    <div><b><span class="kw">Single benchmark</span></b> - TruthfulQA targets misconceptions, so CE rates here likely higher than on general QA.</div>
  </div>
  <div class="threat-item">
    <span class="threat-icon">⚠</span>
    <div><b><span class="kw">Shared judge bias</span></b> - 3-judge ensemble reduces but does not eliminate systematic blind spots across providers.</div>
  </div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[xAI, 2025–2026; Meta, 2024–2025; Qwen Team, 2024–2025]</span> Official model card announcements and release notes.
</div>

---

# Results

## Key results at a glance (O1 + O2 + O3)

<div class="big-number-grid">
<div><span>42.1%</span>of incorrect answers are<br><span class="kw">self-consistent errors</span><br><small>(656 of 1,560)</small></div>
<div><span>0.85–1.00</span>fused <span class="kw">AUROC</span> range<br>pooled mean <b>0.92</b><br><small>(proxy configs 1 & 2)</small></div>
<div><span>17.7→0.5%</span>Grok CE across<br>4 generations<br><small>(only monotonic family)</small></div>
</div>

<div class="result-pills">
  <div class="r-pill o1"><b>O1</b> CEs common across all 6 models · CE% 7.6–18.3% · no model CE-free</div>
  <div class="r-pill o2"><b>O2</b> Proxy probing strong · gap vs direct ≤ 0.03 AUROC</div>
  <div class="r-pill o3"><b>O3</b> Accuracy ↑ in all families · CE did not follow - partially orthogonal</div>
</div>

<div class="callout warn" style="margin-top:0.5rem; font-size:0.8rem">
Higher accuracy does <b>not</b> guarantee lower CE - Claude (74.7% acc.) has a <em>higher</em> CE rate (11.3%) than GPT-5.2 (7.6% CE, 69.8% acc.) <span class="cite">[Tan et al., 2025]</span>.
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Results - O1: Black-Box CE Prevalence

## Aggregate and per-model breakdown (t = 1.0)

<div class="two-col" style="gap:1rem">
<div>

**4,842 rows - outcome split:**

<div class="donut-area">
  <div class="donut-ring">
    <svg viewBox="0 0 120 120" width="110" height="110">
      <circle cx="60" cy="60" r="48" fill="none" stroke="#e2e8f0" stroke-width="20"/>
      <!-- Correct 63.3% -->
      <circle cx="60" cy="60" r="48" fill="none" stroke="#16a34a" stroke-width="20"
        stroke-dasharray="181.5 301.6" stroke-dashoffset="0" transform="rotate(-90 60 60)"/>
      <!-- Incorrect 32.2% → CE 42.1% of that = 13.5% total -->
      <circle cx="60" cy="60" r="48" fill="none" stroke="#dc2626" stroke-width="20"
        stroke-dasharray="38.6 444.5" stroke-dashoffset="-181.5" transform="rotate(-90 60 60)"/>
      <!-- Incorrect IE portion: 18.7% -->
      <circle cx="60" cy="60" r="48" fill="none" stroke="#f97316" stroke-width="20"
        stroke-dasharray="53.5 429.6" stroke-dashoffset="-220.1" transform="rotate(-90 60 60)"/>
      <!-- Not attempted 4.5% -->
      <circle cx="60" cy="60" r="48" fill="none" stroke="#94a3b8" stroke-width="20"
        stroke-dasharray="12.9 470.2" stroke-dashoffset="-273.6" transform="rotate(-90 60 60)"/>
    </svg>
    <div class="donut-center">4,842<br><small>rows</small></div>
  </div>
  <div class="donut-legend">
    <div><span class="dl-dot" style="background:#16a34a"></span> Correct 63.3%</div>
    <div><span class="dl-dot" style="background:#dc2626"></span> <b>CE 13.5%</b></div>
    <div><span class="dl-dot" style="background:#f97316"></span> IE 18.7%</div>
    <div><span class="dl-dot" style="background:#94a3b8"></span> N/A 4.5%</div>
  </div>
</div>

</div>
<div>

**Per-model CE bar chart:**

<div class="bar-chart">
  <div class="bc-row"><span class="bc-label">Claude 4.6</span><div class="bc-bar" style="width:11.3%;background:#dc2626"></div><span class="bc-val">11.3%</span></div>
  <div class="bc-row"><span class="bc-label">GPT-5.2</span><div class="bc-bar" style="width:7.6%;background:#dc2626"></div><span class="bc-val">7.6%</span></div>
  <div class="bc-row"><span class="bc-label">Qwen3 Next</span><div class="bc-bar" style="width:18.3%;background:#dc2626"></div><span class="bc-val">18.3% ↑</span></div>
  <div class="bc-row"><span class="bc-label">DeepSeek</span><div class="bc-bar" style="width:17.7%;background:#dc2626"></div><span class="bc-val">17.7%</span></div>
  <div class="bc-row"><span class="bc-label">Grok 4</span><div class="bc-bar" style="width:10.9%;background:#dc2626"></div><span class="bc-val">10.9%</span></div>
  <div class="bc-row"><span class="bc-label">Llama 4</span><div class="bc-bar" style="width:15.5%;background:#dc2626"></div><span class="bc-val">15.5%</span></div>
  <div class="bc-axis"><span>0%</span><span style="margin-left:auto">20%</span></div>
</div>

<span class="kw">CE%</span> = CE/807 · <span class="kw">CE/Wr</span> = CE as % of wrong answers. No model is CE-free.

</div>
</div>

<div class="citefoot">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods. <em>ACL</em>, pp. 3214–3252, 2022.
</div>

---

# Results - O1: Cross-Model Overlap & Categories

<div class="two-col" style="gap:1rem">
<div>

**Pairwise CE overlap (15 model pairs):**

- <span class="kw">Jaccard</span> range: 0.219–0.360 (mean 0.277) → 64–78% of CE union is *not* shared
- Most CEs are **model-specific** → supports cross-model detection <span class="cite">[Tan et al., 2025]</span>
- Open-weight pairs overlap more (0.34–0.36) vs closed–open (~0.22–0.28)
- Of 720 shared-CE instances: **529 (73.5%)** share the *same* wrong answer

<div class="overlap-visual">
  <div class="ov-circle c1">Model A<br><small>CEs</small></div>
  <div class="ov-overlap"><small>shared<br>~28%</small></div>
  <div class="ov-circle c2">Model B<br><small>CEs</small></div>
</div>
<small style="font-size:0.65rem">73.5% of shared CEs use the <em>same wrong answer</em> - memorized misconceptions</small>

</div>
<div>

**Top categories by CE rate:**

<div class="cat-bars">
  <div class="cb-row"><span class="cb-cat">Confusion: People</span><div class="cb-bar" style="width:50%"></div><span class="cb-pct">50.0%</span></div>
  <div class="cb-row"><span class="cb-cat">Confusion: Other</span><div class="cb-bar" style="width:45.8%"></div><span class="cb-pct">45.8%</span></div>
  <div class="cb-row"><span class="cb-cat">Misquotations</span><div class="cb-bar" style="width:38.9%"></div><span class="cb-pct">38.9%</span></div>
  <div class="cb-row"><span class="cb-cat">Advertising</span><div class="cb-bar" style="width:33.3%"></div><span class="cb-pct">33.3%</span></div>
  <div class="cb-sep">↕ bottom categories</div>
  <div class="cb-row low"><span class="cb-cat">Conspiracies</span><div class="cb-bar low-bar" style="width:0.7%"></div><span class="cb-pct">0.7%</span></div>
  <div class="cb-row low"><span class="cb-cat">Politics / Stats</span><div class="cb-bar low-bar" style="width:0.3%"></div><span class="cb-pct">0.0%</span></div>
</div>

High CE/Wr in Misquotations (83.3%) and Confusion: People (77.5%) → <span class="kw">memorized misconceptions</span>.

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA. <em>ACL</em>, pp. 3214–3252, 2022.
</div>

---

# Results - O2: White-Box Probe Performance

<div class="compact-tables o2-detail-table">

**Training/protocol details (same for both proxy configs):**

- Question-level split: **Train 80% / Validation 10% / Test 10%** (no question overlap across splits)
- Seeds: **11, 22, 33**; report mean AUROC
- Optimizer/training: **Adam (1e-3)**, up to **300 epochs**, early stopping
- Fusion: $s = (1-\lambda)s_M + \lambda s_V$, with $\lambda \in [0,1]$ tuned on validation
- Per-target split sizes are shown as `C/CE` for train, validation, and test

**Config 1: SmolLM3-3B + Phi-4-mini**

| Target | Tgt | Ver | Fused | λ | Train (C/CE) | Val (C/CE) | Test (C/CE) |
|---|---:|---:|---:|---:|---|---|---|
| Claude 4.6 | .952 | **1.00** | .985 | .27 | 72/73 | 3/11 | 15/6 |
| DeepSeek | .852 | **.878** | .875 | .97 | 125/116 | 10/11 | 8/16 |
| GPT-5.2 | .935 | **.972** | .935 | .00 | 51/48 | 4/7 | 6/6 |
| Grok 4 | .923 | **.949** | .923 | .00 | 81/67 | 4/8 | 3/13 |
| Llama 4 | **.877** | .858 | **.877** | .00 | 106/103 | 10/9 | 9/13 |
| Qwen3 Next | **.915** | .884 | .914 | .45 | 120/120 | 10/12 | 16/14 |
| **Mean** | .909 | .923 | **.918** |  |  |  |  |

</div>

<div class="obs-row" style="margin-top:0.25rem">
<span class="kw">Verifier probe</span> is strongest for 4/6 targets. Because λ is selected on validation, fused AUROC can be slightly below the best single probe on small tests.
</div>

---

# Results - O2: White-Box Probe Performance (Config 2)

<div class="compact-tables o2-detail-table">

**Protocol:** same question-level split (**Train 80% / Validation 10% / Test 10%**) · seeds 11/22/33 · Adam 1e-3 · up to 300 epochs + early stop · λ tuned on validation.

**Config 2: Qwen3.5-4B + Phi-4-mini**

| Target | Tgt | Ver | Fused | λ | Train (C/CE) | Val (C/CE) | Test (C/CE) |
|---|---:|---:|---:|---:|---|---|---|
| Claude 4.6 | .941 | **1.00** | **1.00** | .50 | 72/73 | 3/11 | 15/6 |
| DeepSeek | .844 | **.878** | .846 | .05 | 125/116 | 10/11 | 8/16 |
| GPT-5.2 | .954 | **.972** | .954 | .00 | 51/48 | 4/7 | 6/6 |
| Grok 4 | .897 | **.949** | .897 | .00 | 81/67 | 4/8 | 3/13 |
| Llama 4 | **.880** | .858 | .877 | .03 | 106/103 | 10/9 | 9/13 |
| Qwen3 Next | **.942** | .884 | .940 | .25 | 120/120 | 10/12 | 16/14 |
| **Mean** | .910 | .923 | **.919** |  |  |  |  |

</div>

<div class="obs-row" style="margin-top:0.25rem">
Fused AUROC tracks Config 1 closely (mean ~0.92), showing proxy probing is not encoder-specific.
</div>

---

# Results - O2: Original Cross-Model Baseline

Direct hidden-state extraction for Llama 4 and Qwen3 Next (target loaded locally, Nibi HPC <span class="cite">[UWaterloo, 2025]</span>):

| Target | Verifier | Tgt | Ver | Fused | λ |
|---|---|---|---|---|---|
| Llama 4 | Llama-3.1-8B | .872 | **.895** | .872 | .08 |
| Llama 4 | Qwen2.5-0.5B | **.872** | .866 | .872 | .00 |
| Llama 4 | Qwen3-Next-80B | .872 | **.909** | .883 | .18 |
| Qwen3 Next | Llama-3.1-8B | **.946** | .929 | .936 | .92 |
| Qwen3 Next | Qwen2.5-0.5B | **.946** | .924 | .946 | .53 |

<div class="gap-comparison">
  <div class="gc-item"><span class="gc-label">Proxy (SmolLM3)</span><div class="gc-bar proxy-bar" style="width:91.8%"></div><span class="gc-val">0.918</span></div>
  <div class="gc-item"><span class="gc-label">Proxy (Qwen3.5)</span><div class="gc-bar proxy-bar" style="width:91.9%"></div><span class="gc-val">0.919</span></div>
  <div class="gc-item"><span class="gc-label">Direct (Llama 4)</span><div class="gc-bar direct-bar" style="width:87.2%"></div><span class="gc-val">0.872</span></div>
  <div class="gc-item"><span class="gc-label">Direct (Qwen3 Next)</span><div class="gc-bar direct-bar" style="width:94.6%"></div><span class="gc-val">0.946</span></div>
  <div class="gc-note">↑ Gap ≤ 0.03 AUROC - proxy matches direct extraction</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[UWaterloo, 2025]</span> University of Waterloo / Digital Research Alliance of Canada. Nibi HPC Cluster, 2025.
</div>

---

# Results - O3: Version-Evolution Study

<div class="compact-tables o3-evo-table">

**Complete version-evolution table (from thesis; includes release dates):**

| Family | Ver | Model | Release | Acc. | CE | IE | CE% | CE/Wr |
|---|---|---|---|---:|---:|---:|---:|---:|
| Grok | v1 | Grok 3 | 2025-02 | 56.8 | 143 | 175 | 17.7 | 45.0 |
| Grok | v2 | Grok 4 | 2025-07 | 57.1 | 88 | 232 | 10.9 | 27.5 |
| Grok | v3 | Grok 4.1 Fast R. | 2025-11 | 71.3 | 48 | 153 | 5.9 | 23.9 |
| Grok | v4 | Grok 4.20 Beta | 2026-03 | 79.7 | 4 | 133 | 0.5 | 2.9 |
| Llama | v1 | Llama 3 8B | 2024-04 | 38.7 | 79 | 368 | 9.8 | 17.7 |
| Llama | v2 | Llama 3.1 8B | 2024-07 | 43.7 | 58 | 331 | 7.2 | 14.9 |
| Llama | v3 | Llama 3.3 70B | 2024-12 | 52.3 | 141 | 191 | 17.5 | 42.5 |
| Llama | v4 | Llama 4 Mav. 17B | 2025-04 | 52.4 | 125 | 206 | 15.5 | 37.8 |
| Qwen | v1 | Qwen2.5 7B | 2024-09 | 45.8 | 107 | 285 | 13.3 | 27.3 |
| Qwen | v2 | Qwen2.5 72B | 2024-09 | 57.4 | 139 | 162 | 17.2 | 46.2 |
| Qwen | v3 | Qwen3 30B A3B | 2025-04 | 58.1 | 101 | 197 | 12.5 | 33.9 |
| Qwen | v4 | Qwen3 Next 80B | 2025-09 | 65.2 | 148 | 99 | 18.3 | 59.9 |

</div>

<div class="callout warn" style="margin-top:0.4rem">
Accuracy rises across all families, but CE trajectories diverge: only Grok decreases monotonically (17.7%→0.5%), while Llama and Qwen remain non-monotonic.
</div>

<div class="citefoot">
<span class="cite">[xAI, 2025–2026; Meta, 2024–2025; Qwen Team, 2024–2025]</span> Official announcements and model cards.
</div>

---

# What's New?

## Novel contributions with comparative evidence

<div class="two-col" style="gap:1rem">
<div>

**What prior work could not do** <span class="cite">[Tan et al., 2025]</span>:

<div class="gap-visual">
<div class="gap-row header"><span>Study</span><span>CE</span><span>API</span><span>Proxy-only</span><span>Longit.</span></div>
<div class="gap-row"><span>Tan et al., 2025</span><span class="gc">✓</span><span class="gx">-</span><span class="gx">-</span><span class="gx">-</span></div>
<div class="gap-row this-thesis"><span><b>This thesis</b></span><span class="gc">✓</span><span class="gnew">✓</span><span class="gnew">✓</span><span class="gnew">✓</span></div>
</div>

Three capabilities combined here in one CE study.

</div>
<div>

**Comparative numbers:**

<div class="new-cards">
<div class="new-card">
  <div class="nc-badge">NEW</div>
  <b>CE in API-only targets</b><br>
  Claude, GPT-5.2, and Grok 4 extend CE measurement to hidden-state-inaccessible frontier APIs.
</div>
<div class="new-card">
  <div class="nc-badge">GAP</div>
  <b>Proxy gap ≤ 0.03 AUROC</b><br>
  SmolLM3-3B: 0.918 vs direct 0.872–0.946. Near-identical, no target loading.
</div>
<div class="new-card">
  <div class="nc-badge">NEW</div>
  <b>Longitudinal CE evidence</b><br>
  Grok: −17.2 pp (17.7%→0.5%). Llama/Qwen: no net improvement. CE appears reducible when tracked, but not automatically.
</div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Impact on Theory & Practice

<div class="two-col" style="gap:1rem">
<div>

## Impact on Theory

- **<span class="kw">Accuracy</span> and CE are partially orthogonal** <span class="cite">[Tan et al., 2025]</span>: DeepSeek (60.3% acc.) vs Grok 4 (57.1% acc.) - 3.2 pp accuracy gap, but **6.8 pp CE rate gap**. Accuracy-only evaluation is incomplete.
- **Error signals transfer across models** <span class="cite">[Sharma et al., 2026; Zhang et al., 2025]</span>: a 3B local encoder detects CEs in much larger closed-source targets - factual-error signal is in the *text*, not one model's architecture.
- **CE does not decrease automatically** with scale or generation - requires explicit targeting <span class="cite">[Tan et al., 2025]</span>.

</div>
<div>

## Impact on Practice

<div class="practice-cards">
<div class="pcard danger">
  <span class="pcard-icon">🚨</span>
  <div><b><span class="kw">Consistency monitoring can miss CEs</span></b> <span class="cite">[Manakul et al., 2023]</span><br>Sampling + agreement checks can see perfect agreement on a CE, which is risky in healthcare, legal, and education settings.</div>
</div>
<div class="pcard green">
  <span class="pcard-icon">🔍</span>
  <div><b><span class="kw">Proxy probing enables monitoring</span></b><br>Run a 3B local encoder on production queries periodically to estimate CE risk - no model internals needed.</div>
</div>
<div class="pcard blue">
  <span class="pcard-icon">🏆</span>
  <div><b>Grok 4.20 Beta: 0.5% CE</b> <span class="cite">[xAI, 2026]</span><br>Near-elimination is achievable when CE is tracked as an explicit development metric.</div>
</div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[Manakul et al., 2023]</span> Manakul, P. et al. SelfCheckGPT. <em>EMNLP</em>, pp. 9004–9017, Singapore, 2023.<br>
<span class="cite">[Sharma et al., 2026]</span> Sharma, K. et al. Efficient Knowledge Probing of LLMs. <em>arXiv:2508.06030</em>, 2026.
</div>

---

# Limitations of the Results

<div class="limits-grid">
<div class="limit-card">
<div class="lc-icon">📏</div>
<b><span class="kw">Small white-box test sets</span></b><br>
GPT-5.2: 12 items; Grok 4: 16 items → high <span class="kw">AUROC</span> variance. Interpret cautiously.<br>
<em>Addressable:</em> replicate at larger scale.
</div>
<div class="limit-card">
<div class="lc-icon">📚</div>
<b><span class="kw">Single benchmark</span></b><br>
All on <span class="kw">TruthfulQA</span> - targets misconceptions, CE rates likely inflated vs general QA.<br>
<em>Addressable:</em> extend to TriviaQA, SciQ <span class="cite">[Joshi et al., 2017; Welbl et al., 2017]</span>.
</div>
<div class="limit-card">
<div class="lc-icon">🖥️</div>
<b><span class="kw">No DeepSeek direct baseline</span></b><br>
DeepSeek V3.2 too large for Nibi cluster - proxy-only; original-vs-proxy comparison unavailable.<br>
<em>Addressable:</em> larger compute resources.
</div>
<div class="limit-card">
<div class="lc-icon">✂️</div>
<b><span class="kw">Short answers only</span></b><br>
Last-token hidden state tested on short factual answers only. Long-form needs token pooling <span class="cite">[Han et al., 2025]</span>.<br>
<em>Addressable:</em> adopt pooling strategies.
</div>
</div>

<div class="citefoot">
<span class="cite">[Joshi et al., 2017]</span> Joshi, M. et al. TriviaQA. <em>ACL</em>, pp. 1601–1611, Vancouver, 2017. &nbsp;
<span class="cite">[Welbl et al., 2017]</span> Welbl, J. et al. Crowdsourcing Multiple Choice Science Questions. <em>W-NUT Workshop</em>, pp. 94–106, Copenhagen, 2017.<br>
<span class="cite">[Han et al., 2025]</span> Han, J. et al. Simple Factuality Probes. <em>Findings of EMNLP</em>, pp. 16209–16226, Suzhou, 2025.
</div>

---

# Validation

## Evidence supporting the extent of validation

<div class="validation-grid">
<div class="val-card">
<div class="vc-icon">⚖️</div>
<b><span class="kw">3-judge ensemble</span></b><br>
GPT + Claude + Grok - majority vote (2/3). Distinct providers reduce the risk of shared grading bias, though they do not eliminate it. 96–98% pair coverage by DeBERTa NLI <span class="cite">[He et al., 2021]</span>; 2–4% borderline cases escalated to GPT-5.2.
</div>
<div class="val-card">
<div class="vc-icon">🎲</div>
<b><span class="kw">3 random seeds</span> (11, 22, 33)</b><br>
AUROC reported as mean across seeds. <span class="kw">Question-level splits</span> (80/10/10) - same question never in both train and test. Z-score normalisation on train statistics only.
</div>
<div class="val-card">
<div class="vc-icon">🔁</div>
<b><span class="kw">Two proxy architectures</span></b><br>
SmolLM3-3B vs Qwen3.5-4B (different families, different parameter counts) both achieve mean AUROC ~0.92 - approach is not encoder-specific.
</div>
<div class="val-card">
<div class="vc-icon">📊</div>
<b><span class="kw">Proxy vs direct comparison</span></b><br>
Direct hidden-state extraction run for Llama 4 and Qwen3 Next as baseline. Proxy-baseline gap ≤ 0.03 AUROC - validates the proxy assumption.
</div>
</div>

<div class="citefoot">
<span class="cite">[He et al., 2021]</span> He, P. et al. DeBERTa: Decoding-Enhanced BERT with Disentangled Attention. <em>ICLR</em>, 2021.
</div>

---

# Conclusions

With reference to objectives O1–O3 and the results obtained:

<div class="conclusion-rows">
<div class="conc-row">
  <div class="conc-badge o1b">O1</div>
  <div><b>CEs are common</b> [Tables 2–3]: 42.1% of incorrect answers self-consistent at t=1.0. <span class="kw">CE%</span> 7.6–18.3% - no model is CE-free. Higher accuracy ≠ fewer CEs.</div>
</div>
<div class="conc-row">
  <div class="conc-badge o2b">O2</div>
  <div><b>Proxy probes detect CEs effectively</b> [Tables 6–7]: Fused <span class="kw">AUROC</span> 0.85–1.00 (mean 0.92) across all 6 targets including 3 API-only. Gap ≤ 0.03 vs direct - no hidden-state access needed.</div>
</div>
<div class="conc-row">
  <div class="conc-badge o3b">O3</div>
  <div><b>CE evolution is not automatic</b> [Table 9]: Only Grok reduced CE monotonically (17.7%→0.5%). Llama/Qwen: non-monotonic. Accuracy ↑ in all families; CE did not follow.</div>
</div>
</div>

<div class="callout" style="margin-top:0.7rem">
<span class="kw">Self-consistent errors</span> are common, detectable without direct model access, and not automatically reduced by newer training or larger scale <span class="cite">[Tan et al., 2025]</span>.<br>
<b>CE must be tracked as an explicit metric in model development.</b>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

# Future Work & Lessons Learnt

<div class="two-col" style="gap:1rem">
<div>

## Future Work

<div class="future-list">
<div class="fw-item"><span class="fw-icon">📖</span><div><b>Multi-benchmark:</b> TriviaQA, SciQ <span class="cite">[Joshi et al., 2017; Welbl et al., 2017]</span> - do CE patterns hold beyond TruthfulQA?</div></div>
<div class="fw-item"><span class="fw-icon">🧠</span><div><b>Reasoning models:</b> Grok 4.20 Beta reaches 0.5% CE - controlled reasoning-on vs off comparison <span class="cite">[Wei et al., 2022]</span>.</div></div>
<div class="fw-item"><span class="fw-icon">📈</span><div><b>Larger proxies:</b> 7B–13B encoders to map detection–cost trade-off.</div></div>
<div class="fw-item"><span class="fw-icon">🗂️</span><div><b>Category analysis:</b> Confusion: People (50%), Misquotations (38.9%) - replicate on other benchmarks.</div></div>
</div>

</div>
<div>

## Lessons Learnt *(two key lessons)*

<div class="lesson-cards">
<div class="lesson-card">
  <div class="lnum">1</div>
  <div>
    <b><span class="kw">Proxy probing</span> is viable.</b><br>
    3B–4B encoders achieve fused AUROC 0.85–1.00 across all 6 targets; proxy-baseline gap ≤ 0.03. No target loading required - enables <span class="kw">white-box</span> CE detection for any model.
  </div>
</div>
<div class="lesson-card">
  <div class="lnum">2</div>
  <div>
    <b>Accuracy alone is insufficient.</b><br>
    Claude (74.7% acc., 11.3% CE) has a <em>higher</em> CE rate than GPT-5.2 (69.8% acc., 7.6% CE). High-stakes deployments must report CE alongside accuracy. Grok 17.7%→0.5% proves active CE reduction is possible.
  </div>
</div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Joshi et al., 2017]</span> Joshi, M. et al. TriviaQA. <em>ACL</em>, pp. 1601–1611, Vancouver, 2017. &nbsp;
<span class="cite">[Wei et al., 2022]</span> Wei, J. et al. Chain-of-Thought Prompting Elicits Reasoning in LLMs. <em>arXiv:2201.11903</em>, 2022. &nbsp;
<span class="cite">[Welbl et al., 2017]</span> Welbl, J. et al. Crowdsourcing Multiple Choice Science Questions. <em>W-NUT Workshop</em>, pp. 94–106, 2017.
</div>

---

# EXTRA MATERIAL

Backup slides for Q&A:

1. White-box test partition sizes
2. Prompt template and sampling parameters
3. Full references list

---

# EXTRA MATERIAL - White-Box Test Partition Sizes

| Target | Correct | CE | Total | Note |
|---|---|---|---|---|
| Claude Opus 4.6 | 15 | 6 | 21 | |
| DeepSeek V3.2 | 8 | 16 | 24 | |
| GPT-5.2 | 6 | 6 | 12 | ⚠ very small |
| Grok 4 | 3 | 13 | 16 | ⚠ very small |
| Llama 4 Maverick | 9 | 13 | 22 | |
| Qwen3 Next 80B | 16 | 14 | 30 | |

80/10/10 question-level split. Very small test sets → high AUROC variance → interpret cautiously.

---

# EXTRA MATERIAL - Prompt Template & Sampling

```text
Answer the following question concisely and directly in one or two
sentences. Do not explain your reasoning.
Question: {question}
```

| Parameter | Value |
|---|---|
| Greedy temperature | ≈ 0.01 |
| Stochastic sample temperature | 0.7 |
| Stochastic samples per question | 10 |
| CE equivalence threshold | t = 1.0 |
| DeBERTa NLI equiv. threshold | ≥ 0.70 both directions |
| DeBERTa NLI "different" threshold | ≤ 0.30 either direction |
| Borderline cases escalated to GPT-5.2 | 2–4% of pairs |

---

# EXTRA MATERIAL - Full References

- Azaria, A. & Mitchell, T. (2023). The Internal State of an LLM Knows When It's Lying. *Findings of EMNLP*, pp. 967–976. ACL.
- Farquhar, S., Kossen, J., Kuhn, L., & Gal, Y. (2024). Detecting Hallucinations in LLMs Using Semantic Entropy. *Nature, 630*, pp. 625–630.
- Han, J. et al. (2025). Simple Factuality Probes Detect Hallucinations in Long-Form NLG. *Findings of EMNLP*, pp. 16209–16226, Suzhou. ACL.
- He, P., Liu, X., Gao, J., & Chen, W. (2021). DeBERTa: Decoding-Enhanced BERT with Disentangled Attention. *ICLR*, 2021.
- Huang, L. et al. (2025). A Survey on Hallucination in LLMs. *ACM TOIS, 43*(2), pp. 1–55.
- Ji, Z. et al. (2023). Survey of Hallucination in Natural Language Generation. *ACM Computing Surveys, 55*(12), pp. 1–38.
- Joshi, M., Choi, E., Weld, D. S., & Zettlemoyer, L. (2017). TriviaQA. *ACL*, pp. 1601–1611, Vancouver. ACL.
- Kadavath, S. et al. (2022). Language Models (Mostly) Know What They Know. *arXiv:2207.05221*.
- Kossen, J. et al. (2024). Semantic Entropy Probes. *arXiv:2406.15927*.
- Lin, S., Hilton, J., & Evans, O. (2022). TruthfulQA: Measuring How Models Mimic Human Falsehoods. *ACL*, pp. 3214–3252. ACL.
- Manakul, P., Liusie, A., & Gales, M. (2023). SelfCheckGPT. *EMNLP*, pp. 9004–9017, Singapore. ACL.
- Sharma, K., Jin, Y., Trivedi, R., & Kumar, S. (2026). Efficient Knowledge Probing of LLMs by Adapting Pre-trained Embeddings. *arXiv:2508.06030*.
- Tan, H. et al. (2025). Too Consistent to Detect: A Study of Self-Consistent Errors in LLMs. *EMNLP*, pp. 4755–4765, Suzhou. ACL.
- Wei, J. et al. (2022). Chain-of-Thought Prompting Elicits Reasoning in LLMs. *arXiv:2201.11903*.
- Welbl, J., Liu, N. F., & Gardner, M. (2017). Crowdsourcing Multiple Choice Science Questions. *W-NUT Workshop*, pp. 94–106, Copenhagen. ACL.
- Zhang, A. et al. (2025). Reasoning Models Know When They're Right. *arXiv:2504.05419*.

---

<div style="height: 100%; display: grid; place-items: center; text-align: center; padding: 2rem;">
  <div>
    <h1 style="margin-bottom: 0.35rem;">Thank You</h1>
    <p style="font-size: 1rem; font-weight: 700; color: var(--blue-light); margin: 0;">Questions?</p>
  </div>
</div>
