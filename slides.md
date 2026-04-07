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

<style>
.method-grid {
  display: grid;
  grid-template-columns: 1.02fr 0.98fr;
  gap: 1rem;
  align-items: start;
}
.method-note-o2 {
  font-size: 0.76rem;
  line-height: 1.35;
  margin-top: 0.45rem;
  color: #475569;
}
.compare-mini-o2 {
  margin-top: 0.55rem;
  border: 1px solid #d7dde7;
  border-radius: 12px;
  overflow: hidden;
  font-size: 0.68rem;
}
.compare-row-o2 {
  display: grid;
  grid-template-columns: 1.35fr 0.7fr 0.7fr 0.7fr;
}
.compare-row-o2 > div {
  padding: 0.34rem 0.42rem;
  border-right: 1px solid #e5e7eb;
  border-top: 1px solid #e5e7eb;
  text-align: center;
}
.compare-row-o2 > div:first-child {
  text-align: left;
  font-weight: 600;
}
.compare-row-o2 > div:last-child {
  border-right: 0;
}
.compare-row-o2.header-o2 {
  background: #f8fafc;
  font-weight: 700;
}
.compare-row-o2.header-o2 > div {
  border-top: 0;
}
.cm-ok { color: #15803d; font-weight: 800; }
.cm-na { color: #64748b; font-weight: 700; }

.flow-card-o2 {
  border: 1px solid #d8dee8;
  border-radius: 18px;
  background: #fcfcfd;
  padding: 0.75rem 0.85rem;
}
.flow-tag-o2 {
  display: inline-block;
  font-size: 0.64rem;
  font-weight: 800;
  letter-spacing: 0.04em;
  color: #1f2937;
  background: #e5e7eb;
  padding: 0.18rem 0.5rem;
  border-radius: 999px;
  margin-bottom: 0.45rem;
}
.flow-title-o2 {
  font-size: 0.9rem;
  font-weight: 700;
  margin-bottom: 0.55rem;
}
.flow-diagram-o2 {
  display: grid;
  justify-items: center;
  gap: 0.28rem;
}
.fd-qa {
  border: 1px solid #b9c6d8;
  border-radius: 14px;
  padding: 0.35rem 0.7rem;
  font-weight: 700;
  background: #f8fafc;
}
.fd-split-arrows-down {
  width: 55%;
  display: flex;
  justify-content: space-between;
  color: #64748b;
  font-weight: 800;
  font-size: 1rem;
}
.fd-split-o2 {
  width: 100%;
  display: grid;
  grid-template-columns: 1fr 0.78fr;
  gap: 0.9rem;
  align-items: start;
}
.fd-branch-o2 {
  display: grid;
  justify-items: center;
  gap: 0.18rem;
}
.fd-box-o2 {
  width: 100%;
  border: 1px solid #c7d2e3;
  border-radius: 14px;
  padding: 0.45rem 0.55rem;
  text-align: center;
  line-height: 1.25;
  font-size: 0.76rem;
  background: #f8fafc;
}
.fd-box-o2.resp { background: rgba(59,130,246,0.10); }
.fd-box-o2.ver { background: rgba(34,197,94,0.10); }
.fd-box-o2.fuse { background: rgba(239,68,68,0.08); font-weight: 700; }
.fd-out-score { font-weight: 700; }
.fd-arrow-v {
  color: #64748b;
  font-weight: 800;
  font-size: 1rem;
  line-height: 1;
}
.fd-score-row-o2 {
  width: 56%;
  display: flex;
  justify-content: space-between;
  gap: 0.8rem;
}
.fd-score-pill {
  min-width: 3.1rem;
  border: 1px solid #bcc8d8;
  border-radius: 999px;
  padding: 0.12rem 0.55rem;
  background: white;
  text-align: center;
  font-size: 0.74rem;
  font-weight: 700;
}
.fd-converge-arrows {
  width: 42%;
  display: flex;
  justify-content: space-between;
  color: #64748b;
  font-weight: 800;
  font-size: 1rem;
}
.fd-fusion-wide {
  width: 100%;
}
</style>

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
- **Research gap** <span class="cite">[Tan et al., 2025]</span>: CE studied only on open-source models; no proxy probing; no longitudinal tracking.

<div class="callout warn" style="margin-top:0.6rem">
  <b>Definition - <span class="kw">Self-Consistent Error (CE)</span></b> <span class="cite">[Tan et al., 2025]</span>:<br>
  Greedy answer a<sub>g</sub> is <em>incorrect</em> and all stochastic samples are semantically equivalent to a<sub>g</sub>. Confidently wrong, no variation - detectors classify it as <em>reliable</em>.
</div>

</div>
<div>

<div class="error-type-diagram">
  <div class="et-title">Two types of error</div>
  <div class="et-row">
    <div class="et-icon ie-icon">IE</div>
    <div class="et-body">
      <b><span class="kw">Inconsistent Error (IE)</span></b><br>
      <span class="et-samples"><span class="s-wrong">✗ A</span> <span class="s-diff">✗ B</span> <span class="s-diff">✗ C</span> <span class="s-diff">✗ D</span></span><br>
      <small>Samples vary → detectable by consistency check</small>
    </div>
  </div>
  <div class="et-row et-highlight">
    <div class="et-icon ce-icon">CE</div>
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
<div class="rq-card"><div class="rq-num">RQ2</div> Can small <span class="kw">proxy models</span> detect CEs for API-only targets without hidden-state access?</div>
<div class="rq-card"><div class="rq-num">RQ3</div> Do CEs decrease as model families evolve over successive generations?</div>
</div>

<div class="findings-strip">
  <div class="finding-pill"><span class="finding-num">42.1%</span>of wrong answers are CE</div>
  <div class="finding-pill"><span class="finding-num">0.92</span>mean fused AUROC</div>
  <div class="finding-pill"><span class="finding-num">17.7→0.5%</span>Grok CE trajectory</div>
</div>

**Novelty** <span class="cite">[Tan et al., 2025]</span>: first CE measurement on closed-source API models; first proxy-based CE probing; first longitudinal CE tracking across model generations.

**Impact:** CE is invisible to standard monitoring <span class="cite">[Manakul et al., 2023]</span>. Proxy probing enables CE monitoring for any API-served model without access to its internals <span class="cite">[Tan et al., 2025]</span>.

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

- **Hallucination** <span class="cite">[Ji et al., 2023]</span>: fluent but factually unsupported output. This thesis focuses on <span class="kw">factuality hallucinations</span> in closed-book question answering.
- **<span class="kw">TruthfulQA</span>** <span class="cite">[Lin et al., 2022]</span>: a benchmark of 817 questions across 38 categories designed to elicit <span class="kw">imitative falsehoods</span> — common but incorrect beliefs repeated in human text. Unlike TriviaQA <span class="cite">[Joshi et al., 2017]</span>, it targets confident misinformation. This thesis uses 807 questions.
- **CE formalization** <span class="cite">[Tan et al., 2025]</span>: self-consistent errors do not reliably decrease with model scale, and existing detectors perform substantially worse on them.
- **<span class="kw">SelfCheckGPT</span>** <span class="cite">[Manakul et al., 2023]</span>: sampling-based consistency methods can miss CEs, because repeated wrong answers may still appear reliable.

</div>
<div>

<div class="tqa-visual">
  <div class="tqa-header">TruthfulQA example <span class="cite">[Lin et al., 2022]</span></div>
  <div class="tqa-q">Q? <em>"What happens if you swallow gum?"</em></div>
  <div class="tqa-row correct"><span class="tqa-label ok">[Correct]</span> Gum passes through the digestive system normally.</div>
  <div class="tqa-row ie"><span class="tqa-label ie-l">[IE]</span> Answers vary: "stays 7 years" / "causes blockage" / "dissolves slowly"</div>
  <div class="tqa-row ce"><span class="tqa-label ce-l">[CE]</span> All 10 samples: <em>"stays in your stomach for seven years"</em></div>
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

- **<span class="kw">Hidden-state probing</span>** <span class="cite">[Azaria & Mitchell, 2023]</span>: classifiers trained on internal activations can distinguish true from false statements, outperforming output-probability baselines.
- **Factuality probes** <span class="cite">[Han et al., 2025]</span>: lightweight hidden-state probes achieve competitive performance with expensive multi-sample detectors at much lower inference cost.
- **Cross-model probing** <span class="cite">[Tan et al., 2025]</span>: a second <span class="kw">verifier model</span> can capture error signals that the response model misses. The two scores are then fused as <span class="kw">s = (1−λ)s<sub>M</sub> + λs<sub>V</sub></span>.
- **<span class="kw">Semantic entropy</span>** <span class="cite">[Farquhar et al., 2024; Kossen et al., 2024]</span>: meaning-level uncertainty can reveal confabulations and can also be approximated from hidden states.

</div>
<div>

**Research gap - this thesis adds the three missing columns:**

<div class="gap-visual">
<div class="gap-row header"><span>Study</span><span>CE</span><span>API</span><span>Proxy</span><span>Longit.</span></div>
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
<div class="cell ce"><b>Self-Consistent Error</b><br><small>Wrong + Consistent</small><br>[MOST DANGEROUS]</div>
<div class="cell ie"><b>Inconsistent Error</b><br><small>Wrong + Varies</small></div>
</div>

<div style="margin-top:0.5rem; font-size:0.78rem">
Threshold t = 1.0 → <b>all 10</b> samples must match greedy to count as CE.
</div>

</div>
<div>

**Concrete example** <span class="cite">[Tan et al., 2025]</span>:

<div class="example-box">
  <div class="ex-q">Q? <em>"What happens if you swallow gum?"</em></div>
  <div class="ex-correct">[Correct] <b>Correct answer:</b> gum passes through the digestive system normally.</div>
  <div class="ex-ce-label">[CE] CE - model answers:</div>
  <div class="ex-ce-answer">"It stays in your stomach for seven years"</div>
  <div class="ex-samples">
    <span class="s-badge">S1 ≡</span><span class="s-badge">S2 ≡</span><span class="s-badge">S3 ≡</span>
    <span class="s-badge">S4 ≡</span><span class="s-badge">S5 ≡</span><span class="s-badge">⋯</span>
  </div>
  <div class="ex-detector">Consistency detector: <b class="ok-badge">RELIABLE [OK]</b> ← <b>Wrong!</b></div>
</div>

</div>
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect: A Study of Self-Consistent Errors in LLMs. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

<div class="proxy-adapt-slide">

# Research Methodology

## Original setup <span class="cite">[Tan et al., 2025]</span>

<div class="method-grid">
<div>

**Original setup**

- The same <span class="kw">question + greedy response</span> pair is processed by two models: the <span class="kw">response model</span> <em>M</em> and an external <span class="kw">verifier model</span> <em>V</em>.
- The response model is the model that originally produced the greedy response.
- A probe is trained on each model’s hidden representation, producing a <span class="kw">response-side score</span> and a <span class="kw">verifier-side score</span>.
- The two scores are then <span class="kw">fused</span> into a single score used for error detection.

<div class="callout warn" style="margin-top:0.45rem">
<b>Key assumption:</b> both models expose usable <span class="kw">hidden states</span> for probing, so the method requires local or open-weight access.
</div>

<div class="method-note-o2">
<b>Fusion:</b> s = (1−λ)s<sub>M</sub> + λs<sub>V</sub>, where λ ∈ [0, 1] is tuned on the validation set.
</div>

</div>

<div class="flow-card-o2">
  <div class="flow-tag-o2">ORIGINAL</div>
  <div class="flow-title-o2">Tan et al.'s direct cross-model fusion</div>
  <div class="flow-diagram-o2">
    <div class="fd-qa">Question + Greedy Response</div>
    <div class="fd-split-arrows-down"><span>↓</span><span>↓</span></div>
    <div class="fd-split-o2">
      <div class="fd-branch-o2">
        <div class="fd-box-o2 resp">Response model <em>M</em><br><small>hidden states from the model that produced the response</small></div>
        <span class="fd-arrow-v">↓</span>
      </div>
      <div class="fd-branch-o2">
        <div class="fd-box-o2 ver">Verifier model <em>V</em><br><small>separate hidden-state signal</small></div>
        <span class="fd-arrow-v">↓</span>
      </div>
    </div>
    <div class="fd-score-row-o2">
      <span class="fd-score-pill">response score: s<sub>M</sub></span>
      <span class="fd-score-pill">verifier score: s<sub>V</sub></span>
    </div>
    <div class="fd-converge-arrows"><span>↓</span><span>↓</span></div>
    <div class="fd-box-o2 fuse fd-fusion-wide">Fusion: s = (1−λ)s<sub>M</sub> + λs<sub>V</sub></div>
    <div class="fd-arrow-v">↓</div>
    <div class="fd-box-o2 fd-out-score">Final fused error score</div>
  </div>
</div>
</div>

</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

<div class="proxy-adapt-slide">

# Research Methodology

## My proxy adaptation for API-only targets

<div class="method-grid">
<div>

**What I changed**

- Claude, GPT-5.2, and Grok are <span class="kw">API-only</span>, so their hidden states are not directly accessible.
- I keep the same <span class="kw">fusion framework</span> <span class="cite">[Tan et al., 2025]</span>, but the response-side signal now comes from a small local <span class="kw">proxy model</span> applied to the same <span class="kw">question + greedy response</span> pair.
- The verifier-side signal is still produced by a separate verifier model.

**Two proxy configurations:**

<div class="compact-tables">

| Role | Config 1 | Config 2 |
|---|---|---|
| Response proxy | SmolLM3-3B | Qwen3.5-4B |
| Verifier | Phi-4-mini | Phi-4-mini |

</div>

<div class="callout warn" style="margin-top:0.35rem">
<b>Main idea:</b> keep Tan-style <span class="kw">two-score fusion</span>; only the <em>source</em> of the response-side hidden-state signal changes.
</div>

<div class="method-note-o2">
<b>Fusion unchanged:</b> s = (1−λ)s<sub>M</sub> + λs<sub>V</sub>, where s<sub>M</sub> now comes from the proxy model rather than the API target.
</div>

</div>

<div class="flow-card-o2">
  <div class="flow-tag-o2">MY ADAPTATION</div>
  <div class="flow-title-o2">Proxy-based fusion for API-only targets</div>
  <div class="flow-diagram-o2">
    <div class="fd-qa">Question + Greedy Response</div>
    <div class="fd-split-arrows-down"><span>↓</span><span>↓</span></div>
    <div class="fd-split-o2">
      <div class="fd-branch-o2">
        <div class="fd-box-o2 resp">Response proxy model<br><small>SmolLM3-3B or Qwen3.5-4B</small></div>
        <span class="fd-arrow-v">↓</span>
      </div>
      <div class="fd-branch-o2">
        <div class="fd-box-o2 ver">Verifier model<br><small>Phi-4-mini</small></div>
        <span class="fd-arrow-v">↓</span>
      </div>
    </div>
    <div class="fd-score-row-o2">
      <span class="fd-score-pill">proxy score: s<sub>M</sub></span>
      <span class="fd-score-pill">verifier score: s<sub>V</sub></span>
    </div>
    <div class="fd-converge-arrows"><span>↓</span><span>↓</span></div>
    <div class="fd-box-o2 fuse fd-fusion-wide">Fusion: s = (1−λ)s<sub>M</sub> + λs<sub>V</sub></div>
    <div class="fd-arrow-v">↓</div>
    <div class="fd-box-o2 fd-out-score">Final fused error score</div>
  </div>
</div>
</div>

</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[UWaterloo, 2025]</span> Nibi HPC, UWaterloo / Alliance Canada, 2025.
</div>
---

# Research Methodology

## Version-evolution study (O3)

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
  <div class="r-pill o2"><b>O2</b> Strong fused CE detection · proxy models for API-only targets</div>
  <div class="r-pill o3"><b>O3</b> Accuracy ↑ in all families · CE did not follow - partially orthogonal</div>
</div>

<div class="callout warn" style="margin-top:0.5rem; font-size:0.8rem">
Higher accuracy does <b>not</b> guarantee lower CE - Claude (74.7% acc.) has a <em>higher</em> CE rate (11.3%) than GPT-5.2 (7.6% CE, 69.8% acc.) <span class="cite">[Tan et al., 2025]</span>.
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025.
</div>

---

<div class="o1-thesis-tables-slide">

# Results - O1: Black-Box CE Prevalence

## Aggregate and per-model breakdown

<div class="two-col o1-thesis-two" style="gap:0.65rem;align-items:start">
<div>

**Aggregate across all six models** — counts and percentages from the thesis results table.

|  | Count | % Total | % Wrong |
|:---|---:|---:|---:|
| Total rows | 4,842 |  |  |
| Correct | 3,063 | 63.3% |  |
| Incorrect | 1,560 | 32.2% |  |
| Not attempted | 219 | 4.5% |  |
| CE | 656 | 13.5% | 42.1% |
| IE | 904 | 18.7% | 57.9% |

<small style="font-size:0.62rem;color:#64748b">
Among the 1,560 incorrect answers, 656 are self-consistent errors, meaning <b>42.1%</b> of wrong answers are CEs.
</small>

</div>
<div>

**Per-model breakdown** — accuracy shown as a percentage; Wrong, CE, and IE are counts out of 807 questions.

| Model | Acc. | Wrong | CE | IE | CE% | CE/Wr |
|:---|---:|---:|---:|---:|---:|---:|
| Claude Opus 4.6 | 74.7 | 169 | 91 | 78 | 11.3 | 53.8 |
| GPT-5.2 | 69.8 | 217 | 61 | 156 | 7.6 | 28.1 |
| Qwen3 Next 80B | 65.2 | 247 | 148 | 99 | 18.3 | 59.9 |
| DeepSeek V3.2 | 60.3 | 276 | 143 | 133 | 17.7 | 51.8 |
| Grok 4 | 57.1 | 320 | 88 | 232 | 10.9 | 27.5 |
| Llama 4 Maverick | 52.4 | 331 | 125 | 206 | 15.5 | 37.8 |

<small style="font-size:0.62rem;color:#64748b">
<span class="kw">CE%</span> = CE ÷ 807 questions · <span class="kw">CE/Wr</span> = CE as a percentage of wrong answers
</small>

</div>
</div>

</div>

<div class="citefoot">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods. <em>ACL</em>, pp. 3214–3252, 2022.
</div>
---
---

# Results - O1: Pairwise CE Overlap

<div style="font-size:0.84em; line-height:1.1;">

| Model A | Model B | A CE | B CE | Shared CE (Both) | Same wrong | SW% |
|:--|:--|--:|--:|--:|--:|--:|
| DeepSeek | Qwen3 Next | 143 | 148 | 77 | 59 | 76.6 |
| DeepSeek | Llama 4 | 143 | 125 | 71 | 53 | 74.6 |
| Llama 4 | Qwen3 Next | 125 | 148 | 69 | 56 | 81.2 |
| Claude | DeepSeek | 91 | 143 | 60 | 41 | 68.3 |
| DeepSeek | Grok 4 | 143 | 88 | 51 | 33 | 64.7 |
| Grok 4 | Qwen3 Next | 88 | 148 | 50 | 39 | 78.0 |
| Grok 4 | Llama 4 | 88 | 125 | 45 | 32 | 71.1 |
| Claude | Llama 4 | 91 | 125 | 43 | 28 | 65.1 |

</div>

<div class="obs-row" style="margin-top:0.3rem; font-size:0.8em; line-height:1.12;">
CE overlap is <span class="kw">limited</span>. Across model pairs, only about <span class="kw">21.9% to 36.0%</span> of the CE union is shared, so roughly <span class="kw">64% to 78%</span> is not shared.
</div>

<div class="obs-row" style="margin-top:0.12rem; font-size:0.8em; line-height:1.12;">
The <span class="kw">open-weight pairs</span> show the highest overlap overall, with shared CE rates around <span class="kw">34% to 36%</span>.
</div>

<div class="citefoot" style="font-size:0.72em; margin-top:0.18rem;">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods. <em>ACL</em>, pp. 3214–3252, 2022.
</div>

---

# Results - O1: Pairwise CE Overlap

<div style="font-size:0.84em; line-height:1.1;">

| Model A | Model B | A CE | B CE | Shared CE (Both) | Same wrong | SW% |
|:--|:--|--:|--:|--:|--:|--:|
| Claude | Qwen3 Next | 91 | 148 | 43 | 30 | 69.8 |
| GPT-5.2 | Qwen3 Next | 61 | 148 | 39 | 31 | 79.5 |
| Claude | Grok 4 | 91 | 88 | 38 | 25 | 65.8 |
| DeepSeek | GPT-5.2 | 143 | 61 | 38 | 28 | 73.7 |
| GPT-5.2 | Llama 4 | 61 | 125 | 36 | 26 | 72.2 |
| Claude | GPT-5.2 | 91 | 61 | 30 | 22 | 73.3 |
| GPT-5.2 | Grok 4 | 61 | 88 | 30 | 26 | 86.7 |
| **Overall** | — | — | — | **720** | **529** | **73.5** |

</div>

<div class="obs-row" style="margin-top:0.3rem; font-size:0.8em; line-height:1.12;">
When two models share a CE, they often produce the <span class="kw">same wrong answer</span>. Overall, the same-wrong rate is <span class="kw">73.5%</span>.
</div>

<div class="obs-row" style="margin-top:0.12rem; font-size:0.8em; line-height:1.12;">
This suggests that many CEs are model-specific, but shared CEs often reflect the <span class="kw">same misconception</span>.
</div>

<div class="citefoot" style="font-size:0.72em; margin-top:0.18rem;">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods. <em>ACL</em>, pp. 3214–3252, 2022.
</div>

---

# Results - O1: Category-Level CE Vulnerability

<div class="compact-tables o1-category-full">

**Category table (top 10 and bottom 5 by CE rate):**

| Category | Rows | CE | IE | CE% | CE/Wr% |
|:--|--:|--:|--:|--:|--:|
| **Top 10 by CE rate** |  |  |  |  |  |
| Confusion: People | 138 | 69 | 20 | 50.0 | 77.5 |
| Confusion: Other | 48 | 22 | 7 | 45.8 | 75.9 |
| Misquotations | 90 | 35 | 7 | 38.9 | 83.3 |
| Advertising | 78 | 26 | 20 | 33.3 | 56.5 |
| Education | 60 | 19 | 12 | 31.7 | 61.3 |
| Distraction | 84 | 25 | 30 | 29.8 | 45.5 |
| Proverbs | 102 | 29 | 16 | 28.4 | 64.4 |
| Psychology | 108 | 28 | 36 | 25.9 | 43.8 |
| Science | 54 | 10 | 16 | 18.5 | 38.5 |
| Indexical Error: Location | 66 | 12 | 9 | 18.2 | 57.1 |
| **Bottom 5 by CE rate** |  |  |  |  |  |
| Politics | 60 | 0 | 2 | 0.0 | 0.0 |
| Statistics | 30 | 0 | 1 | 0.0 | 0.0 |
| Conspiracies | 150 | 1 | 13 | 0.7 | 7.1 |
| Logical Falsehood | 78 | 2 | 6 | 2.6 | 25.0 |
| Mandela Effect | 36 | 1 | 1 | 2.8 | 50.0 |

</div>

<div class="obs-row" style="margin-top:0.35rem">
Confusion and misquotation categories are the most CE-prone, while Politics, Statistics, and Conspiracies are close to CE-free in this dataset.
</div>

<div class="citefoot">
<span class="cite">[Lin et al., 2022]</span> Lin, S. et al. TruthfulQA: Measuring How Models Mimic Human Falsehoods. <em>ACL</em>, pp. 3214–3252, 2022.
</div>

---

# Results - O2: White-Box Probe Performance

<div class="compact-tables o2-detail-table">

**Training/protocol details (same for both proxy configs):**

- Question-level split: **Train 80% / Validation 10% / Test 10%**
- Seeds: **11, 22, 33**; report mean AUROC
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

**Protocol:** same question-level split (**Train 80% / Validation 10% / Test 10%**) · seeds 11/22/33 · λ tuned on validation.

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
Fused AUROC is nearly identical across both proxy setups (about 0.92), suggesting that CE detection is not tied to one specific proxy model.
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

<div class="obs-row" style="margin-top:0.35rem">
<span class="kw">Takeaway:</span> proxy fused AUROC (Config 1 mean 0.918, Config 2 mean 0.919) matches direct target-side probing on these two open-weight targets.
</div>

<div class="citefoot">
<span class="cite">[Tan et al., 2025]</span> Tan, H. et al. Too Consistent to Detect. <em>EMNLP</em>, pp. 4755–4765, Suzhou, 2025. &nbsp;
<span class="cite">[UWaterloo, 2025]</span> University of Waterloo / Digital Research Alliance of Canada. Nibi HPC Cluster, 2025.
</div>

---

# Results - O3: Version-Evolution Study

<div class="compact-tables o3-evo-table">

**Version-evolution table:**

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

**What prior work did not combine in one CE study** <span class="cite">[Tan et al., 2025]</span>:

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
  <b> CE in API-only targets</b><br>
Extends Tan et al.’s fusion-based CE detection to API-only frontier models using <span class="kw">proxy-only</span> models.
</div>
<div class="new-card">
  <div class="nc-badge">THESIS</div>
  <b> Proxy vs direct baseline</b><br>
  Proxy results are close to the direct baselines overall. Mean fused AUROC stays around 0.92 on six targets.
</div>
<div class="new-card">
  <div class="nc-badge">NEW</div>
  <b> Longitudinal CE evidence</b><br>
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
- **Error signals transfer across models** <span class="cite">[Sharma et al., 2026; Zhang et al., 2025]</span>: a small local proxy model (3B–4B) can detect CEs in much larger closed-source targets, suggesting that the factual-error signal is carried by the question–answer text and is not tied to a single model’s hidden states.
- **CE does not decrease automatically** with scale or generation - requires explicit targeting <span class="cite">[Tan et al., 2025]</span>.

</div>
<div>

## Impact on Practice

<div class="practice-cards">
<div class="pcard danger">
  <span class="pcard-icon"></span>
  <div><b><span class="kw">Consistency monitoring can miss CEs</span></b> <span class="cite">[Manakul et al., 2023]</span><br>Sampling + agreement checks can see perfect agreement on a CE, which is risky in healthcare, legal, and education settings.</div>
</div>
<div class="pcard green">
  <span class="pcard-icon"></span>
<div><b><span class="kw">Proxy probing enables CE monitoring</span></b><br>A small local proxy model can be run periodically on production queries to estimate CE risk, without access to the target model’s internals.</div>
</div>
<div class="pcard blue">
  <span class="pcard-icon"></span>
  <div><b>Grok 4.20 Beta: 0.5% CE</b> <span class="cite">[xAI, 2026]</span><br>These results suggest very low CE may be achievable when CE is tracked as an explicit development metric.</div>
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
<div class="lc-icon"></div>
<b><span class="kw">Small white-box test sets</span></b><br>
GPT-5.2: 12 items; Grok 4: 16 items → high <span class="kw">AUROC</span> variance. Interpret cautiously.<br>
<em>Addressable:</em> replicate at larger scale.
</div>
<div class="limit-card">
<div class="lc-icon"></div>
<b><span class="kw">Single benchmark</span></b><br>
All on <span class="kw">TruthfulQA</span> - targets misconceptions, CE rates likely inflated vs general QA.<br>
<em>Addressable:</em> extend to TriviaQA, SciQ <span class="cite">[Joshi et al., 2017; Welbl et al., 2017]</span>.
</div>
<div class="limit-card">
<div class="lc-icon"></div>
<b><span class="kw">No DeepSeek direct baseline</span></b><br>
DeepSeek V3.2 too large for Nibi cluster - proxy-only; original-vs-proxy comparison unavailable.<br>
<em>Addressable:</em> larger compute resources.
</div>
<div class="limit-card">
<div class="lc-icon"></div>
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
<div class="vc-icon"></div>
<b><span class="kw">3-judge ensemble</span></b><br>
GPT + Claude + Grok - majority vote (2/3). Distinct providers reduce the risk of shared grading bias, though they do not eliminate it. 96–98% pair coverage by DeBERTa NLI <span class="cite">[He et al., 2021]</span>; 2–4% borderline cases escalated to GPT-5.2.
</div>
<div class="val-card">
<div class="vc-icon"></div>
<b><span class="kw">3 random seeds</span> (11, 22, 33)</b><br>
AUROC reported as mean across seeds. <span class="kw">Question-level splits</span> (80/10/10) - same question never in both train and test. Z-score normalisation on train statistics only.
</div>
<div class="val-card">
<div class="vc-icon"></div>
<div><b><span class="kw">Two proxy model architectures</span></b><br>
SmolLM3-3B and Qwen3.5-4B both achieve mean AUROC of about 0.92, suggesting the method is not tied to one specific proxy model.
</div>
</div>
<div class="val-card">
<div class="vc-icon"></div>
<b><span class="kw">Proxy vs direct comparison</span></b><br>
Direct hidden-state extraction run for Llama 4 and Qwen3 Next as baseline. Proxy results were close to the direct baselines overall.
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
  <div><b>CEs are common</b>: 42.1% of incorrect answers self-consistent at t=1.0. <span class="kw">CE%</span> 7.6–18.3% - no model is CE-free. Higher accuracy ≠ fewer CEs.</div>
</div>
<div class="conc-row">
  <div class="conc-badge o2b">O2</div>
  <div><b>Proxy probes detect CEs effectively</b>: Fused <span class="kw">AUROC</span> 0.85–1.00 (mean 0.92) across all 6 targets including 3 API-only. Proxy results are close to the direct baselines overall, without requiring target hidden-state access.</div>
</div>
<div class="conc-row">
  <div class="conc-badge o3b">O3</div>
  <div><b>CE evolution is not automatic</b>: Only Grok reduced CE monotonically (17.7%→0.5%). Llama/Qwen: non-monotonic. Accuracy ↑ in all families; CE did not follow.</div>
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
<div class="fw-item"><span class="fw-icon"></span><div><b>Multi-benchmark:</b> TriviaQA, SciQ <span class="cite">[Joshi et al., 2017; Welbl et al., 2017]</span> - do CE patterns hold beyond TruthfulQA?</div></div>
<div class="fw-item"><span class="fw-icon"></span><div><b>Reasoning models:</b> Grok 4.20 Beta reaches 0.5% CE - controlled reasoning-on vs off comparison <span class="cite">[Wei et al., 2022]</span>.</div></div>
<div class="fw-item"><span class="fw-icon"></span><div><b>Larger proxy models:</b> Test 7B–13B proxy models to map the trade-off between detection performance and compute cost.</div></div>
<div class="fw-item"><span class="fw-icon"></span><div><b>Category analysis:</b> Confusion: People (50%), Misquotations (38.9%) - replicate on other benchmarks.</div></div>
</div>

</div>
<div>

## Lessons Learnt

<div class="lesson-cards">
<div class="lesson-card">
  <div class="lnum">1</div>
<div>
  <b><span class="kw">Proxy probing</span> is viable.</b><br>
  Small 3B–4B proxy models achieve fused AUROC of 0.85–1.00 across all six targets. On the shared open-weight targets, proxy results remain close to the direct baselines without requiring the target model to be loaded locally.
</div>
</div>
<div class="lesson-card">
  <div class="lnum">2</div>
  <div>
    <b>Accuracy alone is insufficient.</b><br>
    Claude (74.7% acc., 11.3% CE) has a <em>higher</em> CE rate than GPT-5.2 (69.8% acc., 7.6% CE). High-stakes deployments must report CE alongside accuracy. Grok 17.7%→0.5% suggests active CE reduction may be possible.
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

# EXTRA MATERIAL - Sampling

| Parameter | Value |
|---|---|
| Greedy temperature | ≈ 0.01 |
| Stochastic sample temperature | 0.7 |
| Stochastic samples per question | 10 |
| CE equivalence threshold | t = 1.0 |

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