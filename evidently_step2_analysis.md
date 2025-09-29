# Evidently - Step 2 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S2P1 | 2 | Supports tabular and text data, basic preprocessing, limited modalities |
| S2P2 | 1 | Basic retrieval infrastructure with FAISS, limited to text embeddings |
| S2P3 | 1 | Minimal vector search capabilities, basic embedding handling |
| S2P4 | 0 | No dedicated model artifact validation features |
| S2P5 | 2 | Good data generation and LLM evaluation scenario creation |
| S2P6 | 1 | Basic data splitting using external libraries, no built-in features |

## Detailed Analysis

### S2P1: Load and validate datasets with modality-specific preprocessing
**Rating:** 2 (Partial Support)
**Evidence:**
- **Data Loading**: Evidently supports loading data from pandas DataFrames via `Dataset.from_pandas()` (core/datasets.py)
- **Modality Support**: Supports tabular data (numerical, categorical) and text data through `DataDefinition` class, which can specify `text_columns`, `numerical_columns`, `categorical_columns`, `datetime_columns` (core/datasets.py:200-286)
- **Text Processing**: Has built-in text descriptors and evaluation capabilities through `TextEvals` preset and descriptors like `TextLength`, `Sentiment`, `Contains` (descriptors/ directory)
- **Validation**: Basic data quality checks through `DataSummaryPreset` and metrics like missing values, duplicates, data drift detection (presets/dataset_stats.py)
- **Configuration**: Data types and column roles can be specified through `DataDefinition` class with proper type detection (core/datasets.py:269-286)

**Limitations:**
- Limited to tabular and text data only - no native support for image, audio, or video modalities
- No built-in preprocessing pipelines for different modalities
- Basic validation focuses on data quality rather than format/schema validation
- Preprocessing must be done externally before loading into Evidently

### S2P2: Build retrieval infrastructure
**Rating:** 1 (Basic Support)
**Evidence:**
- **Vector Index Support**: Basic FAISS integration for building vector indices (llm/rag/index.py:133-146)
- **Embedding Generation**: Uses sentence-transformers (SentenceTransformer "all-MiniLM-L6-v2") for generating embeddings (llm/rag/index.py:108-120)
- **Text Chunking**: `LlamaIndexSplitter` and `SimpleSplitter` for corpus preprocessing and chunking (llm/rag/splitter.py)
- **Data Collection**: `DataCollection` class for managing chunks and indices (llm/rag/index.py:122-166)
- **Retrieval Methods**: Basic similarity search through `find_relevant_chunks()` method (llm/rag/index.py:147-165)

**Limitations:**
- Only supports FAISS IndexFlatL2 - no support for ColBERT, BM25, or other retrieval methods
- Limited to single embedding model (all-MiniLM-L6-v2)
- No built-in support for different distance metrics or advanced retrieval strategies
- Requires external libraries for full functionality (`pip install evidently[llm]`)

### S2P3: Prepare vector search benchmarks
**Rating:** 1 (Basic Support)
**Evidence:**
- **Vector Loading**: Can work with pre-computed embeddings through numpy arrays (llm/rag/index.py:119, 145)
- **Distance Computation**: Support for multiple distance metrics including euclidean, cosine, cityblock, chebyshev (legacy/metrics/data_drift/embedding_drift_methods.py:24-29)
- **Embedding Handling**: Built-in functions for embedding generation and FAISS index operations (llm/rag/index.py)
- **Similarity Search**: Basic k-NN search functionality through FAISS integration (llm/rag/index.py:147-165)

**Limitations:**
- No dedicated tools for ground truth nearest neighbor computation
- Limited vector normalization capabilities
- No specialized benchmark preparation utilities
- Basic distance computation mainly used for drift detection, not benchmarking

### S2P4: Validate model artifacts
**Rating:** 0 (No Support)
**Evidence:**
- **No Checksum Verification**: No built-in functionality for model checksum validation
- **No Version Management**: No dedicated model version checking or compatibility validation
- **No Configuration Validation**: No model configuration validation beyond basic data type checking
- **No Dependency Checks**: No model dependency verification features

**Limitations:**
- Framework focuses on data and prediction evaluation, not model artifact validation
- Would require external tools or custom implementation for model validation tasks
- No infrastructure for model integrity checks

### S2P5: Generate evaluation scenarios
**Rating:** 2 (Partial Support)
**Evidence:**
- **Data Generation**: `FewShotDatasetGenerator` and `RagDatasetGenerator` for creating evaluation datasets (llm/datagen/)
- **LLM Evaluation Templates**: Rich set of evaluation templates and LLM judges through descriptors like `LLMEval`, `ToxicityLLMEval`, `BiasLLMEval`, `ContextQualityLLMEval` (descriptors/)
- **Scenario Templates**: Support for different evaluation templates through `BinaryClassificationPromptTemplate`, `MulticlassClassificationPromptTemplate` (llm/templates.py)
- **Test Case Generation**: Can generate synthetic data for testing through data generators (llm/datagen/base.py)
- **Adversarial Testing**: Support for various evaluation scenarios including toxicity, bias, PII detection through LLM judges

**Limitations:**
- No specialized adversarial input generation beyond LLM-based evaluation
- Limited multi-turn dialogue scenario generation
- Generators require external LLM providers (OpenAI, etc.)
- No built-in templates for complex evaluation scenarios

### S2P6: Create deterministic data splits
**Rating:** 1 (Basic Support)
**Evidence:**
- **External Integration**: Can use scikit-learn's `train_test_split` for basic splitting (as demonstrated in embedding_drift_methods.py:17)
- **DataFrame Handling**: Works with pandas DataFrames which support indexing and splitting
- **Random State**: Can set random seeds through external libraries for reproducibility

**Limitations:**
- No built-in data splitting functionality in Evidently framework
- No native support for stratified splitting strategies
- No stable identifier assignment system for data samples
- Relies entirely on external libraries for splitting operations
- No dedicated tools for managing train/val/test splits within the framework

## Overall Assessment

Evidently is primarily designed as an **evaluation and monitoring framework** for ML and LLM systems rather than a comprehensive data preparation and system initialization toolkit. Its strengths lie in:

1. **Evaluation and Monitoring**: Strong capabilities for data drift detection, model performance evaluation, and LLM assessment
2. **Text Analysis**: Good support for text evaluation with numerous descriptors and LLM judges
3. **Data Quality**: Solid data quality assessment and validation capabilities
4. **LLM Integration**: Decent integration with LLM providers for evaluation scenarios

However, it has significant limitations for Step 2 (PREPARE) processes:

1. **Limited Modality Support**: Only supports tabular and text data, no multimedia capabilities
2. **Basic Infrastructure**: Minimal retrieval infrastructure compared to specialized frameworks
3. **No Model Validation**: Lacks model artifact validation features entirely
4. **External Dependencies**: Many preparation tasks require external tools and libraries

**Total Score**: 6/18 points (33% coverage)

Evidently is best suited for organizations that already have robust data preparation pipelines and need strong evaluation and monitoring capabilities, rather than those seeking a complete data preparation and system initialization framework.