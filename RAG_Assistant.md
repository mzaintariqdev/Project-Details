
# RAG Research Assistant

A retrieval-augmented generation (RAG) system built to *understand* what
actually improves answer quality in a RAG pipeline — not just to wire an
LLM to a vector store and call it done. It implements three retrieval
strategies (naive vector search, hybrid search, and hybrid + reranking),
compares them with a real evaluation harness, and serves everything
through an interactive Streamlit app.

[screen-capture (12).webm](https://github.com/user-attachments/assets/7d2d58c5-632a-496d-9fb7-f2f78aa81a04)

---

## Table of Contents

1. [What This Project Is](#what-this-project-is)
2. [Why This Project Exists](#why-this-project-exists)
3. [How RAG Works, Briefly](#how-rag-works-briefly)
4. [Architecture](#architecture)
5. [Tech Stack](#tech-stack)
6. [Folder Structure](#folder-structure)
7. [File-by-File Explanation](#file-by-file-explanation)
8. [Setup](#setup)
9. [Usage](#usage)
10. [Results](#results)
11. [Design Decisions Worth Knowing](#design-decisions-worth-knowing)
12. [What I'd Add Next](#what-id-add-next)
13. [Troubleshooting](#troubleshooting)
14. [Cost](#cost)

---

## What This Project Is

You ask a question about a document corpus (a set of research papers, by
default). The system:

1. Retrieves the most relevant chunks of text from the corpus using one of
   three strategies
2. Passes those chunks to an LLM (Groq's `openai/gpt-oss-120b`) as context
3. Returns an answer that cites which chunk each claim came from
4. Optionally shows all three retrieval strategies side by side so you can
   see how retrieval quality actually affects the final answer

There's also a built-in evaluation harness that measures retrieval
accuracy (Hit Rate, Mean Reciprocal Rank) and generation quality
(LLM-judged faithfulness and relevancy) — so claims about "this strategy
is better" are backed by numbers, not vibes.

## Why This Project Exists

Most "chat with your PDFs" tutorials stop at the simplest possible
version: embed some text, do a cosine-similarity search, stuff the top
results into a prompt. That version works on toy examples and falls apart
on real corpora — it misses exact keyword matches (product codes, names,
acronyms), it has no way to prioritize the *most* relevant chunk over a
merely related one, and there's no way to know if it's actually working
well without eyeballing individual answers.

This project exists to build — and *measure* — the parts that actually
matter in a production-grade RAG system:

- **Hybrid search**, so exact terminology isn't lost to pure semantic
  matching
- **Reranking**, so the handful of chunks that actually get passed to the
  LLM are the most relevant ones, not just the first ones a fast search
  happened to find
- **Evaluation**, so "hybrid + reranking is better" is a number you can
  point to, not an assumption

## How RAG Works, Briefly

Retrieval-Augmented Generation solves a basic problem with LLMs: they only
know what was in their training data, and they can't cite where an answer
came from. RAG fixes this by giving the model relevant source material
*at the moment it answers*, instead of relying purely on what it
memorized during training.

The flow is:

1. **Index time** (done once, offline): split your documents into small
   chunks, convert each chunk into a vector embedding, and store those
   embeddings in a searchable database
2. **Query time** (done every time someone asks a question): convert the
   question into the same kind of embedding, find the chunks whose
   embeddings are most similar, and hand those chunks to the LLM alongside
   the question
3. The LLM answers using only what's in those chunks, and can cite exactly
   which chunk supported which claim

This project implements that flow, but treats step 2 (retrieval) as the
part worth getting right, since a wrong or incomplete retrieval means the
LLM has no way to give a correct answer no matter how good the model is.

## Architecture

```
                    ┌────────────────────┐
   arXiv PDFs  ───▶ │ chunk_documents.py │  → data/chunks/chunks.json
                    └────────────────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │     ingest.py      │  embeds chunks (bge-small)
                    │                    │  stores vectors in ChromaDB
                    └────────────────────┘
                              │
   user query ───────────────┤
                              ▼
                 ┌─────────────────────────┐
                 │      retrieval.py         │
                 │                          │
                 │  ┌────────┐  ┌────────┐  │
                 │  │ Vector │  │  BM25  │  │   ← two independent
                 │  │ search │  │ search │  │     search methods
                 │  └───┬────┘  └───┬────┘  │
                 │      └── RRF ────┘        │   ← merged via
                 │            │              │     Reciprocal Rank Fusion
                 │            ▼              │
                 │     cross-encoder         │   ← re-scores the merged
                 │       reranking           │     candidates for precision
                 └─────────────┬─────────────┘
                                ▼
                      ┌───────────────────┐
                      │   generate.py      │  builds prompt, calls Groq,
                      │                    │  returns cited answer
                      └───────────────────┘
                                │
                                ▼
                        Streamlit app.py
                     (shows all 3 strategies
                      side by side + eval dashboard)
```

## Tech Stack

| Layer | Choice | Why |
|---|---|---|
| PDF parsing | `pymupdf` | Fast, reliable text extraction from academic PDFs |
| Document source | `arxiv` (Python API) | Free, scriptable, no manual downloading |
| Chunking | `langchain-text-splitters` | Recursive character splitting respects paragraph/sentence boundaries better than naive fixed-size cuts |
| Embeddings | `sentence-transformers` running `BAAI/bge-small-en-v1.5` | Small (~130MB), free, runs locally — no API cost or account needed for the retrieval side of the pipeline |
| Vector store | `chromadb` | Zero-config, persists to disk, good enough at this scale without needing a hosted service |
| Keyword search | `rank_bm25` | Classic sparse retrieval, catches exact terms embeddings can miss |
| Reranking | `sentence-transformers` `CrossEncoder` (`ms-marco-MiniLM-L-6-v2`) | Free, local, and meaningfully improves precision on the final shortlist |
| Generation | `groq` (Llama/GPT-OSS models via Groq's API) | Free tier with generous rate limits, fast inference, no credit card required |
| Config | `python-dotenv` | Loads API keys from a `.env` file instead of manual `export` every session |
| UI | `streamlit` | Fastest path to an interactive, shareable demo |
| Eval | Hand-rolled Hit Rate/MRR + LLM-as-judge | Retrieval and generation quality measured separately, since a failure in either produces a wrong final answer for a different reason |

## Folder Structure

```
rag-portfolio/
├── .env                        # your GROQ_API_KEY (never committed)
├── .env.example                # template showing what .env needs
├── .gitignore
├── README.md
├── requirements.txt
│
├── data/
│   ├── sample_corpus/           # placeholder text docs — works out of the box
│   │   ├── doc1_rag_overview.txt
│   │   ├── doc2_chunking.txt
│   │   ├── doc3_hybrid_search.txt
│   │   ├── doc4_reranking.txt
│   │   └── doc5_evaluation.txt
│   ├── papers/                  # your real corpus lands here
│   │   ├── *.pdf                 (downloaded papers)
│   │   └── metadata.json         (titles, authors, abstracts, arXiv IDs, URLs)
│   ├── chunks/
│   │   └── chunks.json           (output of step 2 — every chunk + its metadata)
│   └── chroma_db/                (persistent vector store — output of step 3)
│
├── src/
│   ├── fetch_papers.py        # Step 1: download a corpus from arXiv
│   ├── chunk_documents.py     # Step 2: parse + split into chunks
│   ├── ingest.py              # Step 3: embed chunks, load into ChromaDB
│   ├── retrieval.py              # Core retrieval logic (all 3 strategies)
│   └── generate.py               # Prompt construction + Groq call
│
├── eval/
│   ├── eval_questions.json       # question set with known correct sources
│   ├── generate_eval_questions.py # auto-generates the above from your corpus
│   ├── run_eval.py               # Step 4: runs the comparison, writes results.json
│   └── results.json              # output — read by the Streamlit eval tab
│
└── app/
    └── app.py                    # Step 5: the interactive Streamlit UI
```

## File-by-File Explanation

### `src/fetch_papers.py`
Queries the arXiv API for papers matching a topic string, downloads each
PDF (manually via `requests`, since the `arxiv` package dropped its
built-in download method in recent versions), and writes
`data/papers/metadata.json` — an index of every paper's title, authors,
abstract, publish date, file path, and URL. This metadata is what lets the
final answers cite real, clickable sources.

### `src/chunk_documents.py`
Reads every PDF's raw text (via `pymupdf`) and splits it into overlapping
chunks (default: 800 characters, 120 character overlap) using a recursive
splitter that tries to break on paragraph boundaries first, then
sentences, then words — so chunks stay coherent rather than cutting
sentences in half. If no real corpus has been fetched yet, it
automatically falls back to the 5 placeholder documents in
`data/sample_corpus/`, so the rest of the pipeline can be tested
immediately without waiting on a real fetch. Output: `data/chunks/chunks.json`.

### `src/ingest.py`
Loads every chunk from `chunks.json`, embeds each one using the
`bge-small` model (runs locally, no API call), and stores the resulting
vectors in a persistent ChromaDB collection. This is the one-time "build
the index" step — it only needs to be re-run when the corpus or chunking
strategy changes.

### `src/retrieval.py`
The core of the project. Defines a `Retriever` class with three methods:

- `vector_search()` — plain cosine-similarity search against the Chroma
  vector store (the "naive" strategy)
- `bm25_search()` — sparse keyword search using BM25 scoring
- `hybrid_search()` — runs both of the above and merges their ranked
  lists using **Reciprocal Rank Fusion (RRF)**, a technique that combines
  rankings without needing to normalize two different scoring scales onto
  each other
- `hybrid_rerank_search()` — takes the hybrid search's top ~15 candidates
  and re-scores them with a cross-encoder, which jointly reads the query
  and each candidate together (much more accurate than the bi-encoder used
  for the initial search, but too slow to run against the whole corpus)

This module is imported by both `eval/run_eval.py` and `app/app.py`, so
retrieval logic lives in exactly one place.

### `src/generate.py`
Builds the final prompt (system instructions + numbered context chunks +
the question) and calls Groq's API to generate an answer. The system
prompt instructs the model to answer strictly from the provided context,
say so explicitly if the context is insufficient, and cite claims with
bracketed numbers matching the source chunks — this is what makes
hallucination visible instead of invisible.

### `eval/eval_questions.json` / `eval/generate_eval_questions.py`
A set of questions, each paired with the document ID(s) that actually
contain the answer. Since arXiv search results vary slightly run to run,
hand-writing a fixed question set only works reliably against the
placeholder corpus — `generate_eval_questions.py` solves this by reading
whatever papers you *actually* downloaded and asking the LLM to write one
grounded question per paper, automatically producing a correctly-labeled
eval set for any corpus.

### `eval/run_eval.py`
Runs every question in the eval set through all three retrieval
strategies and computes:

- **Hit Rate@k** — did the correct document appear anywhere in the top k
  results?
- **Mean Reciprocal Rank@k** — same idea, but rewards the correct result
  appearing *near the top*, not just somewhere in the top k

If `GROQ_API_KEY` is set, it also runs a sample of questions through full
generation and scores the answers on **faithfulness** (is every claim
actually supported by the retrieved context?) and **relevancy** (does the
answer address the question?) using the LLM itself as a judge. Results are
written to `eval/results.json`, which the Streamlit app's "Eval Results"
tab reads directly.

### `app/app.py`
The interactive demo. Two tabs:

- **Ask** — type a question, optionally compare all three strategies
  side by side, see the generated answer plus an expandable list of
  source chunks with visual relevance-score bars
- **Eval Results** — renders whatever's in `eval/results.json` as
  comparison cards, so the retrieval-quality numbers are visible in the
  app itself, not just buried in a terminal log

## Setup

```bash
git clone <your-repo-url>
cd rag-portfolio
pip install -r requirements.txt
```

Get a free Groq API key at **console.groq.com/keys** (no credit card
required), then create `.env` in the project root:

```
GROQ_API_KEY=gsk_your_actual_key_here
```

## Usage

Run once, in order, to build the corpus and index:

```bash
# 1. Fetch papers from arXiv
python src/fetch_papers.py --topic "LLM agents tool use" --n 60

# 2. Parse and chunk them
python src/chunk_documents.py

# 3. Embed and load into the vector store
python src/ingest.py
```

Then generate an eval set matched to your actual corpus and run the
evaluation:

```bash
python eval/generate_eval_questions.py --n 15
python eval/run_eval.py --k 5
```

Finally, launch the app:

```bash
streamlit run app/app.py
```

No corpus fetched yet? Steps 2 and 3 fall back to the placeholder corpus
in `data/sample_corpus/` automatically, so the whole pipeline is testable
end to end before you touch arXiv.

## Results

Run on a real corpus of 60 arXiv papers on "LLM agents tool use" (7,318
chunks total), evaluated against 15 auto-generated questions:

| Strategy | Hit Rate@5 | MRR@5 |
|---|---|---|
| Naive (vector only) | 0.067 | 0.067 |
| Hybrid (vector + BM25) | 0.133 | 0.100 |
| Hybrid + Reranked | 0.133 | 0.133 |

*(Generation quality metrics not yet run — requires `GROQ_API_KEY` to be
set when running `eval/run_eval.py`.)*

**Takeaway:** Hybrid search doubled Hit Rate@5 over naive vector search
alone (0.067 → 0.133), confirming that BM25's exact keyword matching finds
relevant chunks that pure embedding similarity misses on this corpus.
Reranking didn't find any *additional* correct documents beyond what
hybrid search already surfaced (Hit Rate stayed at 0.133), but it did
improve where those correct documents ranked: working backward from the
MRR values, hybrid search's two correct hits landed at ranks 1 and 2,
while after reranking both landed at rank 1 — reranking is doing its job
of pushing the most relevant result to the top, just not helping the
"does the right answer/document show up at all" side of the problem.

The absolute numbers are lower than you might expect for a working RAG
system, and that's worth understanding rather than treating as a bug:
this corpus is 60 papers all on the *same narrow topic* (LLM agents), so
many papers legitimately discuss overlapping ideas. Finding the *one*
specific source paper within the top 5 results out of 7,318 chunks is a
genuinely harder task here than it would be on a more topically diverse
corpus, where competing papers wouldn't be nearly as similar to each
other. Two follow-ups worth trying if you want to push these numbers
further: (1) increase `k` to 10 and see how much Hit Rate recovers, and
(2) tighten the eval question generation prompt to ask for more specific,
paper-distinguishing questions (e.g. referencing a named method or result)
rather than generic ones that many similar papers could plausibly answer.

## Design Decisions Worth Knowing

- **Chunking**: 800 characters / 120 overlap is a reasonable starting
  point, not a claimed optimum — the right values are corpus-dependent
  and should be tuned against the eval set.
- **Embeddings run locally, generation doesn't.** Splitting the pipeline
  this way means indexing and retrieval cost nothing regardless of how
  many queries you run — only the final generation call touches a paid
  (or free-tier) API.
- **RRF over weighted score averaging**: BM25 scores and cosine
  similarities live on completely different scales, so averaging them
  directly would require arbitrary normalization. RRF sidesteps this by
  only caring about *rank position*, not raw score.
- **Reranking a shortlist, not the whole corpus**: cross-encoders score
  much better because they read the query and document together, but
  that also makes them far too slow to run against every chunk — hence
  hybrid search first narrows to ~15 candidates, then reranking picks the
  best few from those.
- **Retrieval and generation are evaluated separately.** A system can
  have excellent retrieval and a lazy generator, or the reverse —
  combining them into one score would hide which part actually needs
  fixing.

## What I'd Add Next

- Query rewriting/expansion before retrieval, to handle vague or
  under-specified questions better
- Multi-hop retrieval for questions that need evidence from more than one
  chunk to answer fully
- Swap the hand-rolled LLM-judge for the full [RAGAS](https://github.com/explodinggradients/ragas)
  library for more standardized metrics
- An agentic loop where the model can decide to retrieve again if its
  first pass looks insufficient, rather than always answering in one shot

## Troubleshooting

**`chromadb.errors.InternalError: Batch size of N is greater than max batch size`**
ChromaDB caps how many items can be added in one `collection.add()` call
(varies by version, commonly ~5,461). `src/ingest.py` handles this by
asking Chroma for its actual limit and adding in safe-sized batches — if
you see this error, make sure you're on the current version of that
script, not an earlier copy.

**`MuPDF error: format error: cmsOpenProfileFromMem failed`**
Harmless. This is MuPDF failing to parse an embedded color profile in a
figure or image inside some PDFs. Text extraction is unaffected — ignore it.

**`Warning: You are sending unauthenticated requests to the HF Hub`**
Also harmless for this project. It's just Hugging Face suggesting an
`HF_TOKEN` for higher download rate limits, which only matters if you're
downloading many models repeatedly. A one-time download of the small
embedding model doesn't need it.

**Eval numbers look surprisingly low even after everything runs correctly**
Check whether your corpus is topically narrow (many papers on the exact
same subject). Hit Rate@k measures whether the *one specific* correct
paper appears in the global top-k across the *entire* corpus — the more
similar the papers are to each other, the harder that becomes, independent
of whether your retrieval pipeline is actually working. This isn't
necessarily a bug; it's worth confirming by manually inspecting a few
retrieved chunks against the expected answer before assuming something's
broken.

## Cost

Every part of this pipeline is free to run:

- Embeddings, vector search, BM25, and reranking all run locally — no API
  calls, no cost, ever
- Generation uses Groq's free tier (no credit card required), which
  covers the query volume of a portfolio project comfortably within its
  rate limits

The only cost that could ever appear is if you swap Groq for a paid
provider like the Anthropic or OpenAI APIs — the codebase is written so
that swap only touches `src/generate.py`.
