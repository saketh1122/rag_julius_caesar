✅ Project Overview

This project implements a Retrieval-Augmented Generation (RAG) expert system for Shakespeare’s Julius Caesar*.
The goal is to enable users to ask natural-language questions about the play and receive:

Scholarly, citation-rich answers

Act/Scene–grounded evidence

Transparent retrieved sources

Confidence-weighted retrieval explanation

The system is designed end-to-end with:

PDF processing & structured chunking of Julius Caesar

Multi-level semantic chunking to capture both fine-grained and high-level meaning

Dense embeddings stored in ChromaDB (persistent)

Weighted retrieval across four semantic granularities

Gemini 2.0 Flash as the generator

FastAPI backend + Streamlit frontend for a clean user interface

Users interact through a Streamlit app which queries a FastAPI backend—this backend runs the complete RAG pipeline and returns both the final answer and supporting evidence.

✅ Design Justification
1. Chunking Strategy

Shakespearean text is structurally dense, and single-level chunking (like only speaker or only windows) fails for many interpretative questions.
Therefore, this project uses four complementary granularities, each solving a different retrieval weakness:

a. Speaker-Level Chunks

Purpose:

Capture exact speeches and who says what

Useful for questions about motives, character personality, conflicts

Why needed:
Shakespeare’s meaning is often in individual lines, not entire scenes.
Speaker-level chunks allow pinpoint retrieval for questions like:
“What does Cassius say about fate?”

b. Context-Window Chunks

Purpose:

Provide local coherence — speeches often depend on prior lines

Sliding windows of multiple chunks keep dialogue continuity

Why needed:
Pure speaker-based chunks break the flow.
This strategy recovers short sequences of 3–5 speeches, fixing loss of context.

c. Scene-Level Chunks

Purpose:

Provide global meaning

Capture thematic, narrative, and plot-level information

Why needed:
Many questions require full-scene context:

Why characters behave a certain way

Emotional build-ups

Causal relationships across several speeches

This dramatically improves RAG accuracy for interpretative questions.

d. Explanation-Level Chunks (Manual Summaries)

Purpose:

Provide concise human-level summaries per scene or act

Help answer broader thematic or conceptual questions more accurately

Why needed:
LLMs perform best when given a “middle abstraction layer.”
Explanation chunks ensure the system can answer questions like:
“Compare Caesar’s ambition with Brutus’s political values.”

Why Four Levels?

Each level solves a different failure mode:

Chunk Type	Strength	Weakness
Speaker	Precision	Lacks context
Window	Dialogue flow	Still too narrow for themes
Scene	Rich meaning	Too long for some queries
Explanation	Conceptual clarity	Loses original text fidelity

Combining all four gives the best hybrid retrieval system.

2. Embedding Model Choice: BGE Base (bge-base-en-v1.5)
✔ Why BGE?

State-of-the-art dense retrieval performance

Strong on literary, abstract, and contextual embeddings

Outperforms MiniLM, all-MPNet, and GloVe for semantic retrieval

Fully open-source & fast enough for real-time inference

Works extremely well on long Shakespearean lines due to superior tokenization

✔ Why not OpenAI or Gemini embeddings?

Costly

Not available in offline mode

Harder to reproduce results

BGE is free, high-quality, local, and consistent.

3. LLM Choice: Gemini 2.0 Flash
✔ Why Gemini 2.0 Flash?

Extremely fast (ideal for interactive RAG)

Strong reasoning for literature content

Great contextual grounding

Low-latency generation (much faster than GPT-4 or Claude Opus on free tier)

Supports multi-paragraph reasoning with citations

✔ System Prompt Reinforcement

The LLM is constrained to:

Use only retrieved context

Infer but not hallucinate

Cite Act/Scene

Stay scholarly

This ensures academic-style responses.
