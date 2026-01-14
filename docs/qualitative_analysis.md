### Qualitative Analysis - Criteria

Thank you for the comment. We agree that an in-depth qualitative analysis of where and why LLMs fail will provide valuable insights for readers. We have now conducted a thorough qualitative analysis on Azure and Google Cloud datasets using the top 3 LLM baselines (GPT-4.1, Qwen3-32B, and DeepSeek-R1-Distill-Qwen-32B). For each of the datasets, we randomly sampled 25 incidents for the three baselines, each of which contains both root cause and mitigation (300 annotations in total, with 150 annotations for root causes and 150 for mitigation steps). For the analysis, we employ a tailored classification scheme based on prior work [47]. To evaluate baselines, we compare each predicted root cause or mitigation against its corresponding reference. For every prediction-reference pair, annotators first determined whether the prediction was correct or incorrect by prioritizing semantic alignment with the reference. Each prediction was then assigned a detailed classification category reflecting the nature of its correctness or error. Particularly, correct predictions are differentiated into Precise (precisely matches the reference), Imprecise (semantically matches but misses specific details in the reference), and Hallucination (semantically matches the reference but with hallucinated factual errors). For incorrect ones, the categories include: Hallucination (containing factual errors or hallucinated causal chains), Reasoning Error (predictions with logical errors or incorrect causal chains), Insufficient Evidence (cannot predict outputs due to insufficient information), and Other (do not have a clearly identifiable prediction). Furthermore, manual observations were recorded to pinpoint where and why the model failed. This process ensured a context-aware assessment of baselines, capturing both the correctness and the specific failures relevant to RCA and mitigation.

| **Outcome** | **Description** |
| --- | --- |
| **Correct** |  |
| Precise | Precisely matches reference root cause |
| Imprecise | Matches reference but misses some details |
| Hallucination | Matches reference but contains unrelated factual errors |
| **Incorrect** |  |
| Hallucination | Contains factual errors in reasoning or prediction |
| Insufficient Evidence | Refrains from making a prediction |
| Other | Cause of error unknown |
| Reasoning Error | Reasoning contains errors |

The results of the qualitative analysis are presented in the tables below.

### Qualitative Analysis - Root Cause

**PER-MODEL BREAKDOWN (CORRECT PREDICTIONS)**

| **Baseline** | **Total** | **Precise** | **Imprecise** | **Hallucination** |
| --- | --- | --- | --- | --- |
| DeepSeek-R1-Distill-Qwen-32B | 17 | 1 (5.88%) | 15 (88.24%) | 1 (5.88%) |
| Qwen3-32B | 13 | 1 (7.69%) | 12 (92.31%) | 0 (0.00%) |
| GPT-4.1 | 17 | 3 (17.65%) | 14 (82.35%) | 0 (0.00%) |

**PER-MODEL BREAKDOWN (INCORRECT PREDICTIONS)**

| **Baseline** | **Total** | **Hallucination** | **Reasoning Error** | **Insufficient Evidence** | **Other** |
| --- | --- | --- | --- | --- | --- |
| DeepSeek-R1-Distill-Qwen-32B | 33 | 10 (30.30%) | 22 (66.67%) | 1 (3.03%) | 0 (0.00%) |
| Qwen3-32B | 37 | 20 (54.05%) | 17 (45.95%) | 0 (0.00%) | 0 (0.00%) |
| GPT-4.1 | 33 | 27 (81.82%) | 5 (15.15%) | 0 (0.00%) | 1 (3.03%) |

Across the 150‑record benchmark, LLMs achieved only 31.33 % overall accuracy, with 47 correct versus 103 incorrect predictions; DeepSeek‑R1‑Distill‑Qwen‑32B and gpt‑4.1 each achieved 34 % correct (17/50), outperforming Qwen3‑32B’s 26 % (13/50), thus representing the strongest baselines on this root‑cause evaluation.
While DeepSeek’s higher rate of Reasoning Errors (22/33, 66.67 %) and gpt‑4.1’s overwhelming Hallucination rate (27/33, 81.82 %) exemplify the distinct failure patterns of each model. Misclassifications include up to 44 Reasoning Errors (44/103, 42.7 %), where the causal chain is incorrectly inferred. These often substitute plausible but incorrect explanations for the true underlying cause. There are also 57 Hallucinations (38.00 %), where the model generates unsupported or fabricated causal links. Additionally, there is 1 Insufficient Evidence case (0.67 %) when contextual clues are too sparse, and 1 Other. Among correct predictions, 5 were Precise (3.33 %), 41 Imprecise (27.33 %) and 1 Hallucinated (0.67 %), with the high proportion of Imprecise results indicating a systematic tendency to match semantic outlines while omitting critical specifics. The distribution of failure types by model highlights a key trade‑off: DeepSeek‑R1’s misattributions stem from over‑reliance on sparse textual cues, producing a larger Reasoning‑Error share; Qwen3‑32B balances Reasoning‑Error (45.95 %) with Hallucination (54.05 %), whereas gpt‑4.1’s pattern leans heavily toward Hallucinations, suggesting a propensity for high‑level generational output that doesn't align with the actual ground truth. Common underlying causes across all models include information sparsity—particularly semantic mismatch, and over‑generalization in the absence of factual data. These weaknesses behaves consistently: failures to recognize explicit root causes, misinterpretation of error types, and omission of specific configuration‑change triggers, each underscoring the importance of accurate causal chaining.
Practical root cause prediction therefore requires integrating a retrieval‑augmented reasoning pipeline that exposes cited external references (e.g., status‑page URLs, diagnostic logs) to augment the generator, coupled with a confidence‑aware filtering layer that flags hallucinated claims by cross‑checking against explicit evidences.
Together, these measures will elevate LLM performance on root‑cause analysis by aligning model outputs more tightly with the factual evidences and reducing the tendency to fabricate artifacts, thereby advancing toward the precision required for high‑quality incident diagnosis.

### Qualitative Analysis - Mitigation

**PER-MODEL BREAKDOWN (CORRECT PREDICTIONS)**

| **Baseline** | **Total** | **Precise** | **Imprecise** | **Hallucination** |
| --- | --- | --- | --- | --- |
| DeepSeek-R1-Distill-Qwen-32B | 6 | 2 (33.33%) | 4 (66.67%) | 0 (0.00%) |
| Qwen3-32B | 10 | 5 (50.00%) | 3 (30.00%) | 2 (20.00%) |
| GPT-4.1 | 14 | 3 (21.43%) | 11 (78.57%) | 0 (0.00%) |

**PER-MODEL BREAKDOWN (INCORRECT PREDICTIONS)**

| **Baseline** | **Total** | **Hallucination** | **Reasoning Error** | **Insufficient Evidence** | **Other** |
| --- | --- | --- | --- | --- | --- |
| DeepSeek-R1-Distill-Qwen-32B | 44 | 33 (75.00%) | 7 (15.91%) | 1 (2.27%) | 2 (4.55%) |
| Qwen3-32B | 40 | 38 (95.00%) | 1 (2.50%) | 0 (0.00%) | 1 (2.50%) |
| GPT-4.1 | 36 | 30 (83.33%) | 6 (16.67%) | 0 (0.00%) | 0 (0.00%) |

Across the 150 records in the mitigation predictions, the overall accuracy of the baselines stands at a modest 20 %, with only 30 cases judged correct while 80 % of predictions are misclassified; the misclassifications are dominated by Hallucination (for both correct and incorrect predictions) followed by Reasoning Error (14/150, 9.3 %), Insufficient Evidence (0.7 % overall, <1 % of errors) and Other (2 %).
The best performing model, gpt‑4.1, produced 14 correct predictions with no Hallucination, with 3 precise and 11 imprecise predictions.
The weakest, DeepSeek‑R1‑Distill‑Qwen‑32B with only 6 correct answers, with 2 precise and 4 imprecise. Success modes are distributed as 10 Precise (6.7 % of total), 18 Imprecise (12 % of total) and 2 Hallucination‑correct (1.33 %).

The trade‑off inherent in this distribution is evident: while imprecise rates is higher than precise, they risk over‑generalization and can omit critical specifics. Failure modes cluster around contextual sparsity and untrusted external references; for instance, models sometimes hallucinate “post‑hoc API rewrites” or “device‑level routing” narratives that are absent from the reference, or incorrectly replace a rollback with an unrelated policy fix, illustrating a tendency to output causal chains that do not exist.
Insufficient Evidence and Other categories are rare but expose that the models sometimes produce generic entries in the absence of specific incident details. Comparative analysis shows Qwen3‑32B suffered the highest hallucination rate (95 % of its 40 wrong predictions) but very few reasoning errors, whereas gpt‑4.1, while reducing hallucinations (83 % of 36 wrong), but with more reasoning errors (17 % of its wrongs). On the other hand, DeepSeek fell somewhere in between (75 % hallucinations, 16 % reasoning).
The root causes for these limitations can be traced to the models’ retrieval limitations – unable to access the external sources – combined with a pattern‑matching bias that prefers to “invent” plausible post‑hoc mitigations, and a semantic mismatch where the models lean towards generic infrastructure actions rather than specific rollback or technical steps explained in the reference.
Addressing these failure mechanisms calls for a retrieval‑augmented approach that pulls the exact reference facts before generating a response.
<!-- Furthermore, fine‑tuning on a curated set of negative examples that reward “I’m uncertain” over fabricated resolution narratives, coupled with a curriculum that explicitly penalizes unnecessary detail, would reduce hallucinations. Finally, explicit guidance on balancing coverage and precision will reduce the risk of wrong but plausible mitigation predictions. -->

### Qualitative Analysis - Conclusions

In summary, our qualitative analysis demonstrates that current large language models achieve only modest accuracy on both root cause and mitigation prediction tasks, with overall correctness rates of 20–31%. The majority of errors are due to hallucinations and reasoning failures, reflecting a tendency to generate plausible but unsupported explanations and to misinterpret causal chains. These weaknesses are can be explained with contextual sparsity, limited access to external evidence, and a bias toward generic or over-detailed responses. Successes are more often imprecise than precise, indicating that models can capture the general outline of an incident but frequently omit critical specifics. To advance LLM performance for incident management, future systems need to integrate retrieval-augmented generation.
Explicit guidance to balance coverage and precision will be essential to reduce hallucinations and improve reliability. These improvements are necessary to align model outputs with real-world scenarios and support robust decision-making in production environments.
