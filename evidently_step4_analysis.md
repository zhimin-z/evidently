# Evidently - Step 4 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S4P1 | 2 | JSON validation, schema matching, format checks available but limited safety filters |
| S4P2 | 2 | Good coverage for classification/retrieval/RAG metrics, limited text generation metrics |
| S4P3 | 3 | Comprehensive LLM-as-judge support, evaluator model integration, custom APIs |
| S4P4 | 1 | Statistical tests for drift detection only, no confidence intervals for metrics |

## Detailed Analysis

### S4P1: Validate and normalize model outputs
**Rating:** 2
**Evidence:**
- **Output format validation:** `IsValidJSON`, `IsValidPython`, `IsValidSQL` descriptors for format checking ([src/evidently/descriptors/generated_descriptors.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/descriptors/generated_descriptors.py))
- **Schema validation:** `JSONSchemaMatch` descriptor with exact/minimal matching and type validation ([src/evidently/legacy/features/json_schema_match_feature.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/legacy/features/json_schema_match_feature.py))
- **Content filters:** `HuggingFaceToxicity` descriptor for toxicity detection, regex matching via `RegExp` descriptor
- **Safety evaluators:** `ToxicityLLMEval`, `BiasLLMEval`, `PIILLMEval` for LLM-based safety checks
**Limitations:**
- No built-in normalization utilities for different output types
- Limited safety filter options beyond toxicity and bias detection
- Schema validation is basic compared to full JSON Schema specification support

### S4P2: Compute task-specific metrics
**Rating:** 2
**Evidence:**
- **Classification metrics:** Full support with accuracy, F1, precision, recall, ROC-AUC via `ClassificationQuality` metrics ([src/evidently/metrics/classification.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/metrics/classification.py))
- **Retrieval/RAG metrics:** Comprehensive coverage with `NDCG`, `MRR`, `MAP`, `HitRate`, `Precision@k`, `Recall@k` ([src/evidently/metrics/recsys.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/metrics/recsys.py))
- **Text metrics:** `BERTScore` for semantic similarity, `SemanticSimilarity` descriptor, exact matching via `ExactMatch`
- **Safety metrics:** `ToxicityLLMEval`, `BiasLLMEval` for measuring bias and toxicity
- **Optimization scorers:** Available for classification (accuracy, precision, recall, F1, ROC-AUC, MCC) and regression (R²) ([src/evidently/llm/optimization/scorers.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/llm/optimization/scorers.py))
**Limitations:**
- **Missing text generation metrics:** No BLEU, ROUGE, or factuality check implementations found
- **Limited calibration:** Only Brier score available, no comprehensive calibration metrics
- **No adversarial robustness:** No specific adversarial evaluation metrics

### S4P3: Apply evaluator models
**Rating:** 3
**Evidence:**
- **LLM-as-judge:** Comprehensive support via `LLMJudge` framework with provider abstraction (OpenAI, Gemini, Ollama) ([src/evidently/legacy/features/llm_judge.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/legacy/features/llm_judge.py))
- **Built-in evaluators:** Multiple pre-built LLM evaluators: `CorrectnessLLMEval`, `FaithfulnessLLMEval`, `CompletenessLLMEval`, `ContextQualityLLMEval` ([src/evidently/descriptors/generated_descriptors.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/descriptors/generated_descriptors.py))
- **Specialized models:** `BERTScore` integration for semantic evaluation, `HuggingFace` descriptor for transformer models
- **Custom evaluators:** `GenericLLMDescriptor` and `LLMEval` classes for custom prompt templates and evaluator integration
- **Template system:** Binary/multiclass classification templates, uncertainty handling, custom prompt support
**Limitations:**
- No direct COMET or RAGAS integration mentioned (though extensible via custom descriptors)

### S4P4: Calculate confidence intervals and statistical significance
**Rating:** 1
**Evidence:**
- **Statistical tests:** Extensive statistical test library for data drift detection with 20+ methods (KS test, Mann-Whitney, Chi-square, etc.) ([src/evidently/legacy/calculations/stattests/](https://github.com/zhimin-z/evidently/blob/main/src/evidently/legacy/calculations/stattests/))
- **Standard errors:** Limited implementation in regression top error analysis ([src/evidently/legacy/metrics/regression_performance/top_error.py](https://github.com/zhimin-z/evidently/blob/main/src/evidently/legacy/metrics/regression_performance/top_error.py))
**Limitations:**
- **No metric confidence intervals:** Statistical tests are only for drift detection, not for computing confidence intervals on evaluation metrics
- **No bootstrap methods:** No bootstrap sampling or other resampling methods for uncertainty quantification
- **No significance testing:** No statistical significance tests for comparing model performance or metric differences
- **Missing uncertainty quantification:** No systematic approach to quantify uncertainty in evaluation results
