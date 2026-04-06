# DocuBot

DocuBot is a small documentation assistant that helps answer developer questions about a codebase.  
It can operate in three different modes:

1. **Naive LLM mode**  
   Sends the entire documentation corpus to a Gemini model and asks it to answer the question.

2. **Retrieval only mode**  
   Uses a simple indexing and scoring system to retrieve relevant snippets without calling an LLM.

3. **RAG mode (Retrieval Augmented Generation)**  
   Retrieves relevant snippets, then asks Gemini to answer using only those snippets.

The docs folder contains realistic developer documents (API reference, authentication notes, database notes), but these files are **just text**. They support retrieval experiments and do not require students to set up any backend systems.

---

## Setup

### 1. Install Python dependencies

    pip install -r requirements.txt

### 2. Configure environment variables

Copy the example file:

    cp .env.example .env

Then edit `.env` to include your Gemini API key:

    GEMINI_API_KEY=your_api_key_here

If you do not set a Gemini key, you can still run retrieval only mode.

---

## Running DocuBot

Start the program:

    python main.py

Choose a mode:

- **1**: Naive LLM (Gemini reads the full docs)  
- **2**: Retrieval only (no LLM)  
- **3**: RAG (retrieval + Gemini)

You can use built in sample queries or type your own.

---

## Running Retrieval Evaluation (optional)

    python evaluation.py

This prints simple retrieval hit rates for sample queries.

---

## Modifying the Project

You will primarily work in:

- `docubot.py`  
  Implement or improve the retrieval index, scoring, and snippet selection.

- `llm_client.py`  
  Adjust the prompts and behavior of LLM responses.

- `dataset.py`  
  Add or change sample queries for testing.

---

## Requirements

- Python 3.9+
- A Gemini API key for LLM features (only needed for modes 1 and 3)
- No database, no server setup, no external services besides LLM calls

## TF Guide

The main thing students needed to understand is that LLMs are not reliable just because they sound confident. Like they can say something that sounds really good but is actually not based on anything in the docs. Retrieval is what helps ground the answers in actual evidence. A big part of this was also understanding the difference between naive generation, retrieval only, and RAG, and how combining retrieval + generation makes things better. 

I think students will struggle the most with the retrieval part, especially how to score relevance, how much text to return, and when the system should just say “I don’t know” instead of guessing. AI was helpful when using it to think through the design or understand parts of the code, but it was also misleading sometimes because it gives answers that look correct even if you don’t fully understand them. 

One important thing I learned is to treat AI output more like a guess or starting point, not something to blindly trust. If I had to guide a student without giving the answer, I would tell them to follow exactly what happens to a query step by step (index → scoring → retrieval) and then compare that with the output they’re getting.