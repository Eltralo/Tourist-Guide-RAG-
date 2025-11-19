# Tourist-Guide-RAG

## **RAG-Based Landmark Retrieval and Evaluation System**

**This project implements a Retrieval-Augmented Generation (RAG) system for answering questions about landmarks using multimodal descriptions (textual and BLIP-generated). It includes data preprocessing, retrieval with ColBERT-style reranking, and evaluation of retrieval quality using the answer_relevancy metric. Also we used Qween for judjing.**

## 📁 Overview

The system:

* Cleans and structures multimodal data (Russian descriptions + English BLIP captions).
* Builds retrieval chunks grouped by landmark (WikiData ID).
* Implements a RAG retriever using bert-base-multilingual-cased with:
* FAISS index for fast coarse retrieval (via [CLS] embeddings).
Token-level ColBERT-style MaxSim reranking for precision.
* Evaluates retrieval quality using the answer_relevancy metric on 100 sampled landmarks.

  
##🛠️ Requirements

bash
pip install -q faiss-cpu transformers torch pandas numpy
Note: No external evaluation libraries (e.g., deepeval, ragas) are used — all metrics are computed with the project’s own model. 

## 📦 Data Structure
Input data must contain the following columns:
* WikiData: Unique identifier for each landmark.
* Name: Landmark name (e.g., "Nizhny Novgorod Kremlin").
City: City where the landmark is located.
* description: Russian textual description (may contain NaN).
* en_txt: English BLIP-generated caption (may contain NaN).
  
## 🔍 Retrieval Pipeline

1. Data Cleaning
2. Chunk Creation
3. RAG Retrieval
   
* Stage 1: FAISS index on [CLS] embeddings for fast candidate retrieval (k*5 candidates).
* Stage 2: Rerank candidates using ColBERT-style MaxSim:
Token-level embeddings.
Max-similarity between query and document tokens.

4. Final score = sum of max-sims weighted by query attention mask.

   
## 📊 Evaluation: answer_relevancy

Method:

* Sample: 100 unique landmarks (or all if <100).
* Query: "Расскажи про {Name} в городе {City}".
* Answer: Top-2 retrieved chunks (joined).
* Metric: Cosine similarity between [CLS] embeddings of question and answer, normalized to [0, 1].
* Final Score: Mean similarity across all samples.
* Output
A single scalar value:

query = "Расскажи про Нижегородский Кремль в Новгороде"
results, _ = rag.retrieve(query, k=7)

answer_relevancy = 0.9629


▶️ Usage

* Prepare your data in a DataFrame data with required columns.
* Run the full script — it will:
* Clean data and build chunks.
* Initialize the FAISS + ColBERT RAG system.
* Compute and print answer_relevancy.
* Example test query is included:

📝 Notes

* The model is multilingual (bert-base-multilingual-cased), supporting mixed Russian/English text.
* BLIP captions are in English; descriptions are in Russian — the model handles this mix.
* Evaluation uses the same model for retrieval and scoring — ensuring consistency.
* Designed to run on CPU (via faiss-cpu) but will auto-use GPU if available.

  
📎 Optional Extensions (Bonus)
Add faithfulness metric (check if answer is supported by context).
Add context_recall (check if ground truth is covered by retrieved contexts).
Export evaluation dataset for external tools (ragas, trulens)
