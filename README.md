# 🍳 RecipesNLP  
A Retrieval-Augmented Generation (RAG) system for recipe creation and title generation, powered by a fine-tuned **Mistral 7B** chatbot and a two-stage **retrieval & re-ranking pipeline**.

---

## 🚀 Project Overview  
This project addresses two key NLP challenges in the culinary domain:

- 🔍 **Recipe Retrieval**: Find the most relevant recipes from a large corpus using hybrid search strategies  
- ✍️ **Recipe Generation**: Generate structured, creative recipes under specific ingredient constraints (e.g., include/avoid)

A full **RAG pipeline** was implemented, combining retrieval, re-ranking, and fine-tuned language models.

---

### 📚 Dataset Summary

The project uses a dataset of **2,231,142 recipes**, each including:  
- `id`, `title`, `ingredients`, `directions`, `link`, and `NER` entities.  
- `source`: either `"Recipes1M"` or `"Gathered"` (scraped from cooking websites).

#### 🔍 Data Profiling Highlights
- **Missing Values**: NaN counts, empty list detection, and overall completeness were analyzed.  
- **Statistics**: Computed title lengths, ingredient counts, instruction steps, and NER entity counts (mean/min/max).  
- **Distributions**: Histograms and boxplots show ingredient and instruction step distributions (cutoff at 30).  
- **Units Normalization**: Mapped measurement unit variants (e.g., `"tsp"` → `"teaspoon"`) and visualized top units.  
- **Vocabulary**: Extracted frequent words in titles, instructions, and NER entities; removed stop words for clarity.  
- **NER Analysis**: Examined correlations with other fields and overlap with ingredients.  
- **Cooking Verbs**: Ranked and visualized the most common cooking actions in instructions.  
- **NER vs Ingredients**: Analyzed discrepancies between ingredient lists and NER entities.

This analysis ensured **data quality, structure clarity, and modeling readiness** for downstream NLP tasks.
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

###  Fine-Tuning – Title Generation  
| Model         | Dataset Size | Quality     |
|---------------|--------------|-------------|
| FLAN-T5-base  | 6k           | Good|
| Mistral-7B    | 6k           | Overkill|

###  Fine-Tuning – Recipe Generation  
| Model + Setup                           | Constraint Adherence | Creativity | Structure |
|----------------------------------------|----------------------|------------|-----------|
| Mistral-7B (Base)                       | ❌ Often violated     | ✅         | ⚠️ Loose   |
| Mistral-7B (Fine-tuned + Prompt + RAG) | 🟨 Sometimes violated   | ✅    | ✅ Structured |

### 🧑‍⚖️ LLM Evaluation (Gemini 2.5 Pro)  
| Query                                   | Best Model                        | Avg Score (10) |
|----------------------------------------|-----------------------------------|----------------|
| `pasta and banana`                     | Mistral-7B (FT + RAG)             | 9.0            |
| `pasta and banana, no nuts/oranges`   | Mistral-7B (FT + RAG)             | 7.5            |
| `rice, coconut milk, chicken, no peppers/wine` | Mistral-7B (FT + RAG)     | 7.5          |

## 💡 Takeaways   
- 📚 Prompt design is crucial for structural generation tasks  
- 🧠 RAG empowers creativity while not always respecting constraints  

## 📁 Struttura del Progetto

<details>
<summary>Clicca per espandere</summary>

- retriever/ - Moduli per il recupero di informazioni (TF-IDF, embeddings)
- reranker/ - Modelli per riordinare i risultati (es. Cross-Encoder)
- generation/ - Modelli per la generazione di testo (titoli, ricette)
- fine_tuning/ - Script per l'addestramento e il fine-tuning dei modelli
- eval/ - Script per la valutazione delle performance (automatica e manuale)
- prompts/ - Raccolta di template e strategie di prompt
- README.md - Questo file

bash
Copia
Modifica

</details>
