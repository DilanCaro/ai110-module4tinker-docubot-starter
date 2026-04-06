# DocuBot Model Card

This model card is a short reflection on your DocuBot system. Fill it out after you have implemented retrieval and experimented with all three modes:

1. Naive LLM over full docs  
2. Retrieval only  
3. RAG (retrieval plus LLM)

Use clear, honest descriptions. It is fine if your system is imperfect.

---

## 1. System Overview

**What is DocuBot trying to do?**  
Describe the overall goal in 2 to 3 sentences.

DocuBot is trying to answer questions using a set of documentation files instead of just guessing. The idea is to make answers more grounded in actual info instead of what the model “thinks” is correct.

**What inputs does DocuBot take?**  
For example: user question, docs in folder, environment variables.

It takes a user question, the docs folder with all the files, and optionally an API key for the LLM.

**What outputs does DocuBot produce?**

Either raw snippets from the docs or a generated answer (if using RAG), or it says it doesn’t know.

---

## 2. Retrieval Design

**How does your retrieval system work?**  
Describe your choices for indexing and scoring.

- How do you turn documents into an index?
- How do you score relevance for a query?
- How do you choose top snippets?

I built a simple inverted index where words map to documents. For scoring, I just count how many words from the query appear in the document. Then I sort by score and return the top ones.

**What tradeoffs did you make?**  
For example: speed vs precision, simplicity vs accuracy.

I kept it very simple so it’s easy to understand, but that means it’s not super accurate. It’s fast and basic but misses context and meaning.

---

## 3. Use of the LLM (Gemini)

**When does DocuBot call the LLM and when does it not?**  
Briefly describe how each mode behaves.

- Naive LLM mode: uses the whole docs and just generates an answer
- Retrieval only mode: no LLM, just returns text
- RAG mode: first retrieves snippets, then sends them to the LLM



**What instructions do you give the LLM to keep it grounded?**  
Summarize the rules from your prompt. For example: only use snippets, say "I do not know" when needed, cite files.

Basically to only use the retrieved snippets and not make stuff up, and to say “I don’t know” if there isn’t enough info.

---

## 4. Experiments and Comparisons

Run the **same set of queries** in all three modes. Fill in the table with short notes.

You can reuse or adapt the queries from `dataset.py`.

| Query | Naive LLM: helpful or harmful? | Retrieval only: helpful or harmful? | RAG: helpful or harmful? | Notes |
|------|---------------------------------|--------------------------------------|---------------------------|-------|
| Example: Where is the auth token generated? | kinda wrong |correct but messy |good |naive guess |
| Example: How do I connect to the database? |vague |ok |best |rag clearer |
| Example: Which endpoint lists all users? |wrong |correct |good |retrieval helped |
| Example: How does a client refresh an access token? |hallucinated |not found |said idk |good guardrail |


**What patterns did you notice?**  

- When does naive LLM look impressive but untrustworthy?  
- When is retrieval only clearly better?  
- When is RAG clearly better than both?

Naive looks confident but is often wrong. Retrieval is more accurate but hard to read. RAG is the best mix most of the time.

---

## 5. Failure Cases and Guardrails

**Describe at least two concrete failure cases you observed.**  
For each one, say:

- What was the question?  
- What did the system do?  
- What should have happened instead?

Question: when is theodore roosvelt birthday
System: naive still answered, and gave the correct birthday . Retrieval returned something totally no relevant.
Should: say I don’t know

Question: do I have a database
System: retrieval returns database.md but also api reference and auth md files. 
Should: either be more precise or refuse

**When should DocuBot say “I do not know based on the docs I have”?**  
Give at least two specific situations.

When no documents match the query or when the score is basically zero. Also when the question is not related to the docs at all.

**What guardrails did you implement?**  
Examples: refusal rules, thresholds, limits on snippets, safe defaults.

if no relevant snippets are found, it returns “I do not know based on these docs.” Also RAG is told to only use the snippets and not guess.

---

## 6. Limitations and Future Improvements

**Current limitations**  
List at least three limitations of your DocuBot system.

1. Only keyword matching, no real understanding
2. Returns full docs sometimes, too much text
3. Doesn’t handle synonyms or similar meaning

**Future improvements**  
List two or three changes that would most improve reliability or usefulness.

1. Better scoring (like TF-IDF or embeddings)
2. Split docs into smaller chunks
3. Add threshold for better filtering

---

## 7. Responsible Use

**Where could this system cause real world harm if used carelessly?**  
Think about wrong answers, missing information, or over trusting the LLM.

If someone trusts wrong answers from naive mode or even RAG when retrieval is bad. Also if important info is missing and it still answers.

**What instructions would you give real developers who want to use DocuBot safely?**  
Write 2 to 4 short bullet points.

- Always double check answers
- Don’t trust naive mode
- Use retrieval or RAG only
- Be okay with “I don’t know”

---
