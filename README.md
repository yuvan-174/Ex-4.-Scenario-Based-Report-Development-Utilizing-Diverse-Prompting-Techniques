# Ex-4.-Scenario-Based-Report-Development-Utilizing-Diverse-Prompting-Techniques
### YUVAN SUNDAR S 
### 21223040250

## Objective: 
The goal of this experiment is to design and develop an AI-powered chatbot that can handle customer inquiries, provide support, and improve customer experience in a retail environment. Create prompts using various AI prompting techniques to guide your experiment, data collection, analysis, and report creation.
## Aim: 
To evaluate how diverse AI prompting techniques can be applied to create an effective scenario-based customer service chatbot for a retail environment, and to compare the quality of responses generated through these different prompting methods.
## Algorithm: 
1. Set Objectives – Decide what to measure (Accuracy, Relevance, CSAT, Resolution rate, Latency).

2. Choose Techniques – Select prompt types (Zero-Shot, Few-Shot, Role-Based, Chain-of-Thought).

3. Prepare Data – Collect FAQs, policies, product info, and sample customer queries.

4. Run Experiment – Let users interact with the chatbot, each session assigned to one prompt type.

5. Collect Data – Log questions, responses, tool usage, and user feedback (thumbs/ratings).

6. Evaluate Responses – Human reviewers check correctness, relevance, and policy compliance.

7. Calculate Metrics – Compute Accuracy, Relevance, CSAT, Resolution rate, and Latency for each prompt type.

8. Compare Results – Identify which prompt type performs best.

9. Analyze Errors – Note common mistakes (hallucination, wrong policy, missing info).

10. Report Findings – Summarize results, improvements, and recommend the best approach.
## Prompt:
Design an AI-powered chatbot for a retail environment that can handle customer inquiries, provide support, and enhance overall customer experience. Use different AI prompting techniques (zero-shot, few-shot, chain-of-thought, role-based, etc.) to guide the chatbot’s responses. Collect and analyze interaction data to measure accuracy, relevance, and customer satisfaction. Finally, prepare a structured report documenting the experiment, methodology, findings, and improvements.
## Output:
## 1) Project Overview
 ### Goal: Design and evaluate an AI‑powered chatbot for retail that answers product questions, handles support (orders/returns/warranty), and improves customer experience.
 ### Primary Use Cases - Product discovery: availability, specs, comparisons, size/fit, compatibility - Order
 ### lifecycle: status, modify/cancel, invoice, delivery ETA - Post‑purchase: returns, exchanges, warranty,
 ### troubleshooting - Store info: hours, location, services, promotions/loyalty
 ### Key Success Metrics - Accuracy (Intent + Factual Correctness) - Relevance (Contextual, Up‑to‑date, Personalized) - Customer Satisfaction (CSAT) - Resolution Rate / First Contact Resolution (FCR) -Average Handle Time (AHT) & Latency - Escalation Rate

## 2) System Architecture (High‑Level)
 ### Frontend: Web widget / mobile SDK (chat UI with forms, quick replies, and feedback controls)
 ### Gateway: API layer for auth, rate limiting, observability
 ### NLP Layer: LLM with retrieval (RAG), tools (order API, inventory API), and policy guardrails
 ### Orchestrator: Dialogue policy, prompt routing (selects prompt type), memory (short‑term convo +long‑term prefs), fallbacks
 ### Data Layer: Vector store (product FAQs, policy docs), analytics store (events), warehouse forevaluation
 ### Monitoring: Prompt/version registry, evaluation jobs, dashboard (metrics/KPIs)

## 3) Prompting Strategy (Production‑Ready Templates)
All prompts follow safety rules: never leak secrets/PII, respect content policy, and use
concise final answers. For deliberate reasoning, think step‑by‑step internally but output
only the final answer.
### 3.1 System Prompt (Global)
""" You are RetailAssist, a helpful, concise retail chatbot. - Goals: answer product & order questions, resolve
issues, escalate when needed. - Tools: {TOOLS_LIST}. If a tool is required, call it; otherwise answer directly
Knowledge: use provided documents & retrieval results only. If unsure, say so and offer next steps. - Style:
friendly, clear, and action‑oriented. Use bullets, short sentences. Confirm key details before acting. - Safety:
do not reveal chain‑of‑thought; do not invent policies; never fabricate prices/ETAs; protect PII. """
### 3.2 Zero‑Shot Prompt (General Queries)
Template """ User question: {USER_QUERY} Context (optional): {RETRIEVED_SNIPPETS} Constraints: concise
answer; if missing details, ask at most 1 clarifying question; cite source names (e.g., "From Return Policy
doc"). Action: Provide final answer only (no reasoning steps). """
Example "Do the AeroMax sneakers run true to size?" + policy snippet about sizing.
### 3.3 Few‑Shot Prompt (FAQ/Policy Adherence)
Template """ Follow the format shown in the examples.
Examples: Q: "What’s your return window?" A: "30 days from delivery. Items must be unused with tags. See
Return Policy."
### Q: "How do I exchange a size?" A: "Start a return, select 'Exchange', choose new size. Exchanges ship after
carrier scans the return."
User question: {USER_QUERY} Context: {RETRIEVED_SNIPPETS} Output style: 2–4 bullet points, then an
optional CTA. """
### 3.4 Role‑Based Prompt (Persona Switching)
Templates - Product Expert: "Act as a product specialist. Compare options objectively, highlight trade‑offs,
and end with a short recommendation for {USER_GOAL}."
Order Support Agent: "Act as a support agent. Verify identity minimally, summarize the issue, and
present the next action with a confirmation prompt."
Policy Guardian: "Act as a policy guardian. Check the proposed answer against policy snippets and
flag any contradictions before finalizing."
### 3.5 Chain‑of‑Thought / Deliberate Reasoning (Hidden)
Template (for internal use) """ Think through the steps privately (do not reveal). Verify facts against
{RETRIEVAL_RESULTS} and tool outputs. If uncertainty > 30%, propose safe alternatives or escalate. Return
only the final answer. """

## 4) Conversational Patterns (Reusable Templates)
### 4.1 Product Recommendation (RAG + Few‑Shot)
1) Ask a single clarifying question if key constraints are missing (budget/size/use).
2) Retrieve top 5 products; filter by constraints.
3) Output: 2–3 options with pros/cons + CTA to add to cart or compare.
Prompt Snippet "Given {USER_NEEDS} and {RETRIEVED_PRODUCTS}, propose 2–3 items. Use bullets; add
1‑line why each fits; end with a CTA."
### 4.2 Order Status (Tool‑Use + Role)
Prompt Snippet "Verify lightweight identity. Call Order API. Summarize status in 2 bullets + ETA. Offer
modify/cancel if policy allows."
### 4.3 Returns/Exchange Flow (Policy Guardian)
Prompt Snippet "Check return eligibility from policy. If eligible, present label instructions and refund
timeline. If ineligible, provide nearest alternative."
### 4.4 Store Info (Store Locator)
Prompt Snippet "Find nearest stores to {PINCODE}. Provide today’s hours, phone, services, and directions
link."

## 5) Data Collection & Logging Schema
Event Model 
<img width="767" height="464" alt="image" src="https://github.com/user-attachments/assets/a4c77267-67c6-4ebc-b9ba-547a7e243810" />
Metadata to Capture - Model version & prompt version - Retrieval corpus version - Tool versions &
response codes - Policy checks (pass/fail reasons)
## 6) Annotation & Ground Truth
### Intent labels: {catalog, order, returns, store, other}
### Entity slots: product, size, color, storeId, orderId
### Policy compliance: compliant / partial / violated
### Answer quality rubric (0–3):
### 3 = correct, complete, concise, cites doc/tool
### 2 = mostly correct, minor omission
### 1 = partly correct, key gap
### 0 = incorrect/off‑policy

## 7) Metrics & Formulas
### Accuracy: correct_turns / labeled_turns
### Relevance: % turns citing correct doc/tool when needed
### CSAT: avg 1–5 per session; report distribution
### FCR: % sessions with resolution without escalation within N turns
### Escalation Rate: escalated_sessions / total_sessions
### Latency (p50/p90): ms from user_msg to assistant_msg
### Hallucination Rate: % answers with unsupported claims

## 8) Experiment Methodology
### A/B/N Prompt Routing - Randomly assign sessions to prompt variants: ZS (zero‑shot), FS (few‑shot), RB
(role‑based), ZS+Delib (hidden reasoning), FS+Tools. - Keep model, tools, and corpus constant per batch.

### Procedure 1. Define hypotheses (e.g., FS improves accuracy on policy queries by ≥8%). 2. Run for a fixed
window (≥1,000 labeled turns per arm or until power reached). 3. Collect implicit signals (thumbs, CSAT) +
explicit human labels (subset). 4. Analyze: two‑proportion z‑test for Accuracy/FCR; Mann‑Whitney U for CSAT;
bootstrap CIs for latency. 5. Decide winners; promote; archive prompts with versioning.

### Failure & Safety Handling - Confidence < 0.5 → retrieve again or switch to safer prompt; if still low,
escalate. - PII requests or off‑policy → refuse with reason and provide alternatives.

## 9) Analytics & Dashboards (Fields)
### Overview: sessions, messages, CSAT dist, FCR, escalations
### Quality: accuracy by intent, hallucination rate, policy compliance
### Operations: latency p50/p90, tool success/fail, API error map
### Experiment: arm comparison with CIs; win rate by intent
### Drilldowns: conversation transcripts with annotations

## 10) Implementation Hints (Pseudocode)
```
# pseudo: prompt router
if intent in {"policy", "returns"}:
prompt = FEW_SHOT_POLICY
elif intent in {"order"}:
prompt = ROLE_ORDER_SUPPORT
elif missing_key_details(user_msg):
prompt = ZERO_SHOT_WITH_SINGLE_CLARIFIER
else:
prompt = ZERO_SHOT
# pseudo: safety
if detects_pii_leakage(user_msg):
refuse_and_offer_safe_path()
# pseudo: evaluation
for turn in labeled_turns:
score = rubric(turn)
```
## 11) Ready‑Made Prompt Library (Copy‑Paste)
### A) Zero‑Shot (General) "Answer the user’s question using only the provided context. If unknown, say you’reunsure and offer next steps. Output 3–6 short sentences max. No reasoning steps. Context: {CTX}.Question: {Q}."
### B) Few‑Shot (Policy) "Follow the examples to mirror tone and content. Cite the doc title in parentheses.Examples: [insert 3–5 of your own]. Context: {CTX}. Question: {Q}. Output bullets + CTA."
### C) Role‑Based (Order Support) "You are a retail support agent. Verify identity lightly, then call tools as needed. Summarize status + ETA, propose next action, and ask for confirmation. Keep it under 5 short lines."
### D) Deliberate (Hidden) + Final‑Only Output "Privately reason step‑by‑step and fact‑check with context/ tools. Return only the final answer, concise. If confidence < 0.5, ask one clarifying question or escalate."
### E) Refusal/Safety Template "I can’t help with that request. Here’s what I can do instead: [allowed options].Would you like me to connect you to a human agent?"
### F) Recovery Template (Low Confidence) "I might be missing a detail. Could you confirm: [one key detail]? Meanwhile, here’s what I can do now: [safe action]."

## 12) Sample Interaction Scripts (For Testing)
### Product fit: "I’m between sizes 8 and 9—what do you suggest for AeroMax?" → Sizing advice +
return policy CTA.
### Order status: "Where’s order #A1234?" → Verify + tool call + ETA / reroute.
### Return: "These headphones are faulty." → Eligibility, label, refund timeline.
### Store hours: "Is Koramangala store open today?" → Locator + today’s hours.
### Policy challenge: "Can you price match this?" → Policy check + alternatives.

## 13) Data Analysis Plan (Step‑by‑Step)
### Aggregate sessions by experiment arm & intent.
### Compute Accuracy, FCR, CSAT mean & distribution, Latency p50/p90.
### Run significance tests; compute 95% CIs.
### Plot lift vs control for each metric.
### Error taxonomy: bucket failures (retrieval miss, tool error, policy confusion, ambig

## 14) Findings Template (Fill‑In):
Summary: FS improved accuracy on policy queries by +9.7% (p=0.01). ZS fastest latency (‑18% p90).
RB reduced escalations by ‑6.3%.
Drivers: Examples of wins/losses with transcripts (redacted).
Errors: Top 3 root causes and their frequencies.
Customer Impact: CSAT +0.4 avg; FCR +7% overall.

## 15) Improvement Plan
Add 20 new FAQ exemplars to FS prompt for returns/exchanges.
Tighten refusal templates for price‑match exceptions.
Expand retrieval corpus with size/fit reviews; re‑embed weekly.
Tool hardening: circuit‑break Order API on 5xx; retry with backoff.
Personalization: remember user sizes and preferred stores (opt‑in).

## 16) Final Report Structure (Ready to Submit)
Title: RetailAssist – Prompting Experiments & Customer Experience Evaluation
Authors/Date/Version
1. Introduction
Problem, scope, objectives, KPIs.
2. Methodology
Datasets/corpus, prompt variants, routing, tools, safety, experiment design.
3. Results
Tables/figures for Accuracy, Relevance, CSAT, FCR, Latency; statistical tests; error taxonomy.
4. Discussion
Interpretation, limitations, ablations.
5. Improvements
Prompt refinements, tool/corpus changes, roadmap.
6. Conclusion
Key takeaways and next steps.
Appendices
A. Prompt texts (versioned)
B. Policy snippets
C. Annotation rubric
D. Sample transcripts (redacted)

## 17) Deliverables Checklist
[ ] Prompt library (ZS, FS, RB, Delib, Guardrails) versioned
[ ] Test scripts & seed conversations
[ ] Logging + schemas implemented
[ ] Labeling guidelines + tooling
[ ] Dashboard with KPIs & experiment views
[ ] Final report using section 16

## 18) Optional: Quick Start (Minimal Viable Setup)
Start with ZS + single clarifier; add FS for returns/policy.
RAG over: product catalog, returns policy, shipping policy, store hours.
Tools: Inventory, Order Status, Store Locator.
Feedback: thumbs + 1‑click CSAT after resolved turns.
Weekly evaluation on 300 labeled turns; promote winners.
## Result:
Thus the experiment completed successfully
