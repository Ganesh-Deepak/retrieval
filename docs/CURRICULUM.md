# Retrieval & Context Engineering: curriculum

*Course 5 of the library · Applied track · 18 chapters · about 22 hours · status: preview. Generated from `curricula/retrieval.txt` in the hub repository; edit it there.*

Getting the right information into the context window, and proving you did. You will build sparse, dense and late-interaction retrievers, build ANN indexes from brute force up, measure retrieval separately from generation, and decide with a cost model between retrieval, long context and an agent that searches. The demo that worked and the product that did not differ in exactly these chapters.

**For:** Engineers who have built a RAG demo that worked and a RAG product that did not.

**By the end you can:**

- Build BM25, dense and multi-vector retrievers and know when each wins
- Build IVF-PQ and HNSW indexes and trade recall against latency and memory
- Measure retrieval, grounding and answers separately, with retrieval golden sets
- Choose between retrieval, long context and agentic search with a cost model
- Engineer a context window: ordering, compression and cache-aware layout

**Before you start:** Transformers From Scratch Ch 04 (Embeddings and the Residual Stream); Transformers From Scratch Ch 09 (Whole Architectures + End-to-End Shape Trace); Transformers From Scratch Ch 21 (Scaling Laws and Long Context); Evaluation & Experimentation for LLM Systems Ch 12 (Evaluating Retrieval-Augmented Systems)

**Current to November 2026:** Late interaction and multi-vector retrieval (ColBERT, PLAID, ColPali for page images) and the 2025–26 theory of single-vector limits; contextual retrieval and late chunking; LLM rerankers; agentic search, where a model with grep and a search tool beats a fixed pipeline on some corpora; context rot in long-context models; prompt-cache-aware context layout.

**Not covered:** Building a search engine for the open web, and recommender systems.

**Reference package:** `retkit/` · **Semantic hues:** dim-1 = the query; dim-2 = the document; dim-3 = the index; dim-4 = the context window; dim-5 = the answer


## Part I · Representations

*What retrieval matches on.*

### 01 · The Retrieval Problem (50 min)

*Why retrieve, how it fails, and how to measure it.*

Builds on: Evaluation & Experimentation for LLM Systems Ch 12 (Evaluating Retrieval-Augmented Systems)

1. **Why retrieve at all** — knowledge the model lacks, freshness, permissions and citations
2. **Precision and recall for retrieval** — and which one your product needs
3. **The failure taxonomy** — not retrieved, retrieved but buried, retrieved but ignored
4. **Evaluate first** — a small retrieval golden set before any modelling

**Build:** a 200-query retrieval golden set with graded relevance for the course corpus.

### 02 · Sparse Retrieval (60 min)

*TF-IDF, BM25, and the inverted index.*

Builds on: this course Ch 01 (The Retrieval Problem)

1. **The inverted index** — built from scratch
2. **TF-IDF and BM25** — derived, with what k1 and b do
3. **Tokenisation and stemming choices, measured**
4. **Learned sparse retrieval** — SPLADE and term expansion
5. **Where BM25 still wins** — identifiers, rare terms and exact phrases

**Build:** an inverted index with BM25, evaluated on the golden set.

### 03 · Dense Embeddings (65 min)

*What an embedding encodes, and what it does not.*

Builds on: Transformers From Scratch Ch 09 (Whole Architectures + End-to-End Shape Trace); this course Ch 02 (Sparse Retrieval)

1. **Bi-encoders** — mean pooling, CLS and last-token pooling
2. **Contrastive training** — InfoNCE with in-batch and hard negatives
3. **Similarity geometry** — cosine, anisotropy and normalisation
4. **Matryoshka embeddings** — truncating dimensions at a measured cost
5. **Instruction-tuned embeddings and asymmetric queries**

**Build:** dense retrieval with an open embedding model, compared with BM25 by query type.

### 04 · Training a Retriever (70 min)

*Data, hard negatives, and distillation.*

Builds on: this course Ch 03 (Dense Embeddings)

Related: Post-Training in Practice Ch 06 (Synthetic Data)

1. **Training data** — real queries, synthetic queries, and their biases
2. **Hard-negative mining** — and the false negatives it introduces
3. **Distilling a cross-encoder into a bi-encoder**
4. **Domain adaptation** — when fine-tuning a retriever pays

**Build:** fine-tune an embedding model on synthetic queries and measure the gain.

### 05 · Late Interaction and Multi-Vector Retrieval (70 min)

*ColBERT, PLAID, ColPali, and the limits of one vector.*

Builds on: this course Ch 03 (Dense Embeddings)

1. **Why one vector is not enough** — the theoretical limits of single-vector retrieval
2. **ColBERT** — token-level matching with MaxSim
3. **Compression** — residual quantisation and PLAID
4. **Visual document retrieval** — ColPali on page images
5. **Cost** — storage and latency against the quality gain

**Build:** a small ColBERT-style index, compared with single-vector retrieval.


## Part II · Indexes

*Finding neighbours fast.*

### 06 · Exact Search and Vector Compression (55 min)

*Brute force, quantisation, and the cost of memory.*

Builds on: this course Ch 03 (Dense Embeddings)

1. **Brute-force search** — the baseline that is often enough
2. **Scalar and binary quantisation** — memory against recall
3. **Product quantisation** — derived, with asymmetric distance computation
4. **Rescoring** — search compressed, rerank exact

**Build:** brute-force, int8, binary and PQ search compared on recall, memory and speed.

### 07 · ANN Indexes from Scratch (80 min)

*IVF, IVF-PQ, HNSW, and the trilemma.*

Builds on: this course Ch 06 (Exact Search and Vector Compression)

1. **IVF** — clustering the corpus and probing lists
2. **IVF-PQ** — compressed lists
3. **HNSW** — navigable small-world graphs, built and searched
4. **The recall, latency and memory trilemma** — measured curves
5. **Filtered search and disk-based indexes**

**Build:** IVF-PQ and HNSW implemented from scratch and checked against a library.

### 08 · Vector Search in Production (55 min)

*Updates, deletes, sharding, and freshness.*

Builds on: this course Ch 07 (ANN Indexes from Scratch)

1. **Updates and deletes** — what they do to graph and cluster indexes
2. **Incremental indexing and freshness guarantees**
3. **Sharding and replication**
4. **Metadata filters and access control**

**Build:** an index service with incremental updates and permission filters.


## Part III · Pipelines

*From a query to the right passages.*

### 09 · Chunking as a Design Decision (55 min)

*Measured, not guessed.*

Builds on: this course Ch 01 (The Retrieval Problem)

1. **Fixed, recursive and structural chunking**
2. **Chunk size against recall and context cost** — measured
3. **Contextual retrieval** — prefixing chunks with document context
4. **Late chunking and parent–child retrieval**

**Build:** a chunking benchmark over four strategies on the golden set.

### 10 · Hybrid Fusion and Reranking (65 min)

*RRF, cross-encoders, and LLM rerankers.*

Builds on: this course Ch 02 (Sparse Retrieval); this course Ch 03 (Dense Embeddings)

1. **Fusing sparse and dense** — reciprocal rank fusion and score normalisation
2. **Cross-encoder rerankers** — the accuracy and the latency
3. **LLM rerankers** — listwise and pointwise
4. **Cascades** — cheap recall, expensive precision, and the budget for each stage

**Build:** a hybrid retriever with a reranking cascade, with gains and latency per stage.

### 11 · Query Understanding (50 min)

*Rewriting, expansion, HyDE, and routing.*

Builds on: this course Ch 10 (Hybrid Fusion and Reranking)

1. **Query rewriting from conversation context**
2. **Expansion and HyDE** — generating a hypothetical answer to search with
3. **Decomposition for multi-part questions**
4. **Routing between indexes and tools**

**Build:** query rewriting and HyDE evaluated on conversational queries.

### 12 · Structured and Graph Retrieval (60 min)

*SQL, tables, and knowledge graphs.*

Builds on: this course Ch 11 (Query Understanding)

1. **Text-to-SQL** — schema linking, validation and its failure modes
2. **Tables and semi-structured data**
3. **Knowledge graphs and GraphRAG** — when relations matter
4. **Hybrid queries over text and structure**

**Build:** a text-to-SQL tool with execution-based evaluation.

### 13 · Multimodal Retrieval (50 min)

*PDFs, layouts, images, and charts.*

Builds on: this course Ch 05 (Late Interaction and Multi-Vector Retrieval)

Related: Multimodal & Generative Models Beyond Text Ch 03 (Contrastive Learning and CLIP)

1. **PDFs** — parsing against page images
2. **Layout-aware chunking**
3. **Image and chart retrieval with vision-language embeddings**

**Build:** page-image retrieval over a PDF corpus compared with text extraction.


## Part IV · Context engineering

*Assembling the window.*

### 14 · Long Context Against Retrieval (60 min)

*Cost, lost-in-the-middle, and context rot.*

Builds on: Transformers From Scratch Ch 21 (Scaling Laws and Long Context); this course Ch 01 (The Retrieval Problem)

1. **The cost model** — tokens, latency and price for long context against retrieval
2. **Lost in the middle** — position effects, measured
3. **Context rot** — degradation as the window fills, on realistic tasks
4. **Why needle tests mislead** — and harder long-context evaluations

**Build:** a long-context against retrieval comparison on the course corpus.

### 15 · Context Engineering (60 min)

*Ordering, compression, citations, and caching.*

Builds on: this course Ch 14 (Long Context Against Retrieval)

Related: Inference Engineering at Scale Ch 06 (Prefix Caching)

1. **What goes into the window** — instructions, retrieved passages, memory and tools
2. **Ordering and formatting** — measured effects
3. **Compression** — extractive, abstractive and token-level
4. **Citations** — making the model cite, and checking it
5. **Cache-aware layout** — stable prefixes first

**Build:** a context assembler with compression and citations, evaluated end to end.

### 16 · Agentic Search (65 min)

*Iterative retrieval and research loops.*

Builds on: this course Ch 15 (Context Engineering)

Related: Agents & Tool Use Ch 01 (The Agent Loop from First Principles)

1. **Retrieval as a tool** — letting the model decide when and what to search
2. **Iterative retrieval and deep-research loops**
3. **grep, file navigation and search engines as tools**
4. **When an agent beats a pipeline** — and what it costs

**Build:** an agentic search loop compared with the fixed pipeline on hard queries.

### 17 · Evaluating Retrieval End to End (55 min)

*Golden sets, synthetic queries, and attribution.*

Builds on: Evaluation & Experimentation for LLM Systems Ch 12 (Evaluating Retrieval-Augmented Systems); this course Ch 01 (The Retrieval Problem)

1. **Retrieval golden sets** — pooling, graded relevance and maintenance
2. **Synthetic queries** — generating them without making the test easy
3. **Answer-level metrics** — groundedness, attribution and completeness
4. **Regression testing a retrieval system**

**Build:** the end-to-end evaluation suite used by the capstone.

### 18 · Capstone: Beat Long Context, for Less (300 min)

*A retrieval system with the evidence to back it.*

Builds on: this course Ch 17 (Evaluating Retrieval End to End); this course Ch 15 (Context Engineering)

1. **The corpus and the questions** — with a retrieval golden set
2. **Hybrid retrieval with reranking and a tuned chunking strategy**
3. **The context assembler with citations**
4. **The comparison** — quality and cost against a long-context baseline, with intervals

**Build:** the system, its evaluation, and the cost report.

## Capstone

A retrieval system over a real corpus that beats a long-context baseline at lower cost: a retrieval golden set, hybrid retrieval with reranking, measured groundedness, and a cost comparison.
