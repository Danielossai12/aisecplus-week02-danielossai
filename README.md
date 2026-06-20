# 🔐 AISec Plus — Week 2: AI Model Risk Assessment

**Researcher:** Daniel Ossai
**Repo:** `aisecplus-week02-danielossai`
**Model Assessed:** Mistral 7B v0.1 (mistralai/Mistral-7B-v0.1)
**Submitted:** Week 2 · AISec Plus Bootcamp

---

## 1. Executive Summary

This report is a structured risk assessment of **Mistral 7B v0.1**, a 7-billion-parameter open-weights large language model developed by Mistral AI and released in October 2023. The model is assessed for use as a general-purpose text generation backend in an enterprise productivity application. While Mistral 7B offers strong performance and a permissive Apache 2.0 license, it carries significant undisclosed risk: Mistral AI has explicitly refused to reveal its training dataset composition, citing competitive sensitivity. This silence is itself the primary finding. The model is assessed as **Approve with Conditions** — suitable for non-sensitive, low-risk use cases only, with mandatory output controls in place.

---

## 2. Model Overview

| Field | Detail |
|---|---|
| **Model name** | Mistral 7B v0.1 |
| **Developer** | Mistral AI (France) |
| **Type** | Decoder-only transformer, 7 billion parameters |
| **Release date** | October 2023 |
| **Architecture** | Grouped-Query Attention (GQA) + Sliding Window Attention (SWA) |
| **License** | Apache 2.0 — unrestricted commercial use |
| **Use case assessed** | General-purpose text generation backend for enterprise productivity tool |
| **HuggingFace link** | https://huggingface.co/mistralai/Mistral-7B-v0.1 |

---

## 3. Data Supply Chain

```
[Unknown Web Sources]
        ↓
  Web Scraping
  (undisclosed composition)
        ↓
  Data Filtering & Preprocessing
  (methods not disclosed)
        ↓
  Training on 2.4 Trillion Tokens
  (hardware: undisclosed)
        ↓
  Mistral 7B v0.1 Weights
  (released under Apache 2.0)
        ↓
  Fine-tuning / Deployment
  (by downstream users — inherits all upstream unknowns)
```

**Key observations:**
- Mistral AI confirmed training data was "extracted from the open Web" but refused to disclose dataset composition, citing competitive sensitivity
- No dataset card, no data provenance documentation, no breakdown of sources published
- Training used approximately **2.4 trillion tokens** — scale confirmed in third-party research, not by Mistral directly
- No disclosure of data filtering methodology, deduplication approach, or quality controls
- Hyperparameter configurations (learning rate, batch size, RLHF pipeline) are entirely undisclosed

> **Finding:** The data supply chain is almost entirely opaque. Every risk in this report flows from this single fact.

---

## 4. Risk Register

| Risk | Category | Likelihood | Impact | Level |
|---|---|---|---|---|
| Training data composition completely undisclosed | Data Provenance | High | High | 🔴 Critical |
| PII or personal data memorised from web scrape | Embedded Sensitive Data | High | High | 🔴 Critical |
| Copyrighted content baked into model weights | Embedded Sensitive Data | High | Medium | 🟠 High |
| No bias evaluation or fairness testing published | Bias & Fairness | High | Medium | 🟠 High |
| No moderation mechanism in base model | Security Threats | High | Medium | 🟠 High |
| Fine-tuners inherit all upstream data unknowns | The Inheritance Problem | High | Medium | 🟠 High |
| Model card has major transparency gaps | Transparency / Black Box | High | Medium | 🟠 High |
| Susceptible to adversarial prompt injection | Security Threats | Medium | High | 🟠 High |
| Legal grey area: copyright of web-scraped training data | Licensing & Legal | Medium | High | 🟠 High |
| Vendor dependency on Mistral AI for future updates | Operational | Low | Medium | 🟡 Medium |
| Inference cost and latency at enterprise scale | Operational | Low | Low | 🟢 Low |

---

## 5. Risk Matrix

```
         │  LOW IMPACT  │ MEDIUM IMPACT │ HIGH IMPACT
─────────┼──────────────┼───────────────┼─────────────
HIGH     │              │ • No bias eval │ • Training data
LIKELI-  │              │ • No moderation│   undisclosed  ← CRITICAL
HOOD     │              │ • Inheritance  │ • PII memorised ← CRITICAL
         │              │ • Card gaps    │
─────────┼──────────────┼───────────────┼─────────────
MEDIUM   │              │               │ • Copyright risk
LIKELI-  │              │               │ • Prompt injection
HOOD     │              │               │ • Legal grey area
─────────┼──────────────┼───────────────┼─────────────
LOW      │ • Inference  │ • Vendor      │
LIKELI-  │   cost       │   dependency  │
HOOD     │              │               │
```

🔴 **Critical** = 2 risks | 🟠 **High** = 7 risks | 🟡 **Medium** = 1 risk | 🟢 **Low** = 1 risk

---

## 6. Recommended Controls

### 🔴 Critical Risks

**Training data undisclosed / PII memorisation**
- Deploy a PII scanning layer (e.g., Microsoft Presidio, AWS Comprehend) on all model outputs before they reach users
- Never use Mistral 7B for use cases involving personal, medical, financial, or legally sensitive data
- Document the residual risk formally and get sign-off from your legal/compliance team before deployment
- Monitor for membership inference attacks — where adversaries probe the model to extract training data

**Copyrighted content in weights**
- Restrict output use to internal tools; avoid publishing Mistral-generated content verbatim at scale
- Add terms of service language acknowledging potential copyright exposure
- Track emerging court decisions on LLM training data copyright (this legal area is actively evolving)

### 🟠 High Risks

**No bias evaluation published**
- Run your own bias benchmarks (e.g., BBQ, WinoBias) before production deployment
- Test systematically across demographic groups relevant to your use case
- Do not deploy in hiring, lending, healthcare, or legal contexts without independent bias audit

**No moderation mechanism**
- Implement a content moderation layer (e.g., Llama Guard, OpenAI Moderation API, custom classifier) on top of the model
- Never expose the base model directly to end users without a guardrail wrapper

**Inheritance problem for fine-tuners**
- Any fine-tuned version inherits all of the above risks — document this in your fine-tuning project
- Do not assume fine-tuning on clean data neutralises upstream data risks

**Prompt injection vulnerability**
- Implement input sanitisation and output validation
- Separate system instructions from user inputs at the architecture level
- Use structured prompting formats that reduce injection surface

**Legal grey area**
- Get a legal opinion before deploying commercially in a regulated industry
- Track the EU AI Act compliance timeline — Mistral AI's lack of transparency may create compliance exposure

---

## 7. Risk Verdict

> ### ⚠️ APPROVE WITH CONDITIONS

Mistral 7B v0.1 is a high-performing, commercially permissive model with real production utility. However, its near-total training data opacity creates irreducible, undisclosed risk across data provenance, privacy, bias, and legal categories.

**Approved for:**
- Internal tooling and developer experimentation
- Low-sensitivity text generation (summaries, drafts, code assistance)
- Research and prototyping where data sensitivity is low

**Not approved for (without further controls):**
- Any use case involving personal, medical, financial, or legally regulated data
- Customer-facing applications without a moderation and PII-scanning layer
- Fine-tuned deployments in regulated industries without independent audit
- Use cases where copyright exposure could create material legal liability

**The single most important lesson from this assessment:**
> When a model's documentation does not answer a question, that silence IS a finding. Mistral AI's refusal to disclose training data is not a minor gap — it is the root cause of the majority of risks in this report.

---

## 8. Sources

1. Mistral AI — Announcing Mistral 7B (official blog)
   https://mistral.ai/news/announcing-mistral-7b/

2. HuggingFace — mistralai/Mistral-7B-v0.1 model card
   https://huggingface.co/mistralai/Mistral-7B-v0.1

3. HuggingFace Community Discussion — Training data question (Mistral AI response)
   https://huggingface.co/mistralai/Mistral-7B-v0.1/discussions/8

4. arXiv — Comprehensive Analysis of Transparency and Accessibility of SoTA LLMs
   https://arxiv.org/pdf/2502.18505

5. arXiv — Safety and Security Analysis of Large Language Models
   https://arxiv.org/html/2509.10655v2

6. arXiv — Mistral 7B technical paper (Jiang et al., 2023)
   https://arxiv.org/pdf/2310.06825

---

*AISec Plus · Week 2 · We are all growing. Show up. Build. Improve.*

