# 🍳 RecipesNLP  
A Retrieval-Augmented Generation (RAG) system for recipe creation and title generation, powered by a fine-tuned **Mistral 7B** chatbot and a two-stage **retrieval & re-ranking pipeline**.

---

## 🚀 Project Overview  
This project addresses two key NLP challenges in the culinary domain:

- 🔍 **Recipe Retrieval**: Find the most relevant recipes from a large corpus using hybrid search strategies  
- ✍️ **Recipe Generation**: Generate structured, creative recipes under specific ingredient constraints (e.g., include/avoid)

A full **RAG pipeline** was implemented, combining retrieval, re-ranking, and fine-tuned language models.

---

## ⚙️ System Architecture  

### 🔹 1. Two-Stage Retrieval  
Efficiently retrieves high-relevance recipe candidates from a large database.

**Stage 1 – Candidate Retrieval**  
- 🧠 Lexical: **TF-IDF**  
- 🔎 Semantic: **KNN** over sentence embeddings

**Stage 2 – Re-ranking**  
- 📐 **Cosine Similarity** (multi-qa-MiniLM-L6-cos-v1)  
- 🤝 **Cross-Encoder** (cross-encoder/ms-marco-MiniLM-L-6-v2)

**Evaluation**  
- 🧪 Test set built with LLM + human feedback  
- 📊 Metric: **Spearman's ρ** for ranking quality  
- ✅ **TF-IDF + Cross-Encoder** delivered the best balance of recall and precision

---

### 🔹 2. Fine-Tuned Language Models  

#### 🏷️ Title Generation  
Input: recipe procedure → Output: title  
Best result: `FLAN-T5-base` trained on 6k samples (concise + creative)

#### 🧾 Recipe Generation  
Input: ingredients to include/exclude → Output: full recipe (Title, Ingredients, Instructions)

**Models evaluated**:  
- `FLAN-T5-small/base`  
- `Mistral-7B-Instruct-v0.3`

**Prompting strategies (for Mistral-7B):**  
1. Standard instruction following  
2. Simple persona-based prompt  
3. Detailed system prompt (structured output enforced)

---

### 🔹 3. RAG-Powered Recipe Generation  
The final system integrates retrieval + generation:

1. 🔍 Retrieve top 3 most relevant recipes  
2. 🧠 Feed them as context to **fine-tuned Mistral-7B**  
3. ✨ Generate new, constraint-aware recipes with improved coherence and creativity

---

## 📈 Results & Key Findings  

### 🧪 Retrieval  
- ✅ **TF-IDF + Cross-Encoder** = most effective pipeline  
- ⚠️ KNN alone = weaker performance on keyword-driven queries  

### 🧠 Fine-Tuning – Title Generation  
| Model         | Dataset Size | Quality     |
|---------------|--------------|-------------|
| FLAN-T5-base  | 6k           | ⭐️⭐️⭐️⭐️⭐️ (Best) |
| Mistral-7B    | 6k           | Overkill, too verbose |

### 🧠 Fine-Tuning – Recipe Generation  
| Model + Setup                           | Constraint Adherence | Creativity | Structure |
|----------------------------------------|----------------------|------------|-----------|
| Mistral-7B (Base)                       | ❌ Often violated     | ✅         | ⚠️ Loose   |
| Mistral-7B (Fine-tuned + Prompt + RAG) | ✅ Always respected   | ✅    | ✅ Structured |

### 🧑‍⚖️ LLM Evaluation (Gemini 2.5 Pro)  
| Query                                   | Best Model                        | Avg Score (10) |
|----------------------------------------|-----------------------------------|----------------|
| `pasta and banana`                     | Mistral-7B (FT + RAG)             | 9.5            |
| `pasta and banana, no nuts/oranges`   | Mistral-7B (FT + RAG)             | 9.0            |
| `rice, coconut milk, chicken, no peppers/wine` | Mistral-7B (FT + RAG)     | 9.8            |


