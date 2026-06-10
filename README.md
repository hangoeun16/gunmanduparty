# gunmanduparty
Simulates reader comments for Korean web novel authors drafting in isolation. AI reads, never writes. 2nd place poster, UChicago [CMSC 33231: Writing with AI '26](https://minalee-research.github.io/teaching/writing-with-ai.html).

## Demo

[▶ Watch the demo video](https://www.youtube.com/watch?v=TWx_ACFiBuI&feature=youtu.be)

## Poster

![Poster](source/gunmandu_poster_only1_300.png)

## Recognition

![2nd Place](source/recognition.jpeg)  
🏆 2nd place — UChicago CMSC 33231: Writing with AI, Spring 2026 Poster Session

---
작가님, 군만두 좋아하세요? 🥟

## What This Is

Korean web novel writers draft 20–50 episodes before a single reader shows up. The platform structure forces it. Existing AI writing tools write prose or give critique. Writers stockpiling episodes need neither. They need a sign that someone is reading.

GunmanduParty generates comments, the text based reactions that real readers leave on web novel platform. The personas come from 804 real comments on my own work, clustered into reader archetypes and tested against a separate held-out work. The system never writes prose, never autocompletes, never touches the writer's text.

## Personas

Three from data, one author-defined:

- 💗 **Emotional Reactor** — short, intense feelings
- 🔍 **Devoted Analyst** — long, theory-building, tracks foreshadowing
- ✨ **Polite Supporter** — brief per-episode check-ins
- ⚙ **Author-Defined** — the writer describes their ideal reader; the system grounds it in real comments via BGE-M3 retrieval


## Key Findings

- **3 of 4 archetypes transfer across works** at cosine similarity above 0.85. The Hiatus archetype scored lowest (0.685), confirming the decision to drop it.
- **Personas are tendencies, not identities.** Median dominance: 56.9%. Even loyal readers drift across comment styles about 40% of the time.
- **One prompt line moved cosine +0.09. Vocabulary did not follow.** The system catches tone cheaply but misses word choice. This is the strongest case for the author-defined persona: a work's reader vocabulary cannot be pulled from a different work's corpus through prompt engineering alone.

## Code Structure

```
pipeline/                            # Offline: comment → persona (10 scripts)
├── 01_parse_comments.py             # Raw text → anonymized JSONL (SHA256 nicknames)
├── 02_eda.py                        # Exploratory analysis + heuristic classification
├── 03_clustering_bge.py             # BGE-M3 embeddings → KMeans clustering
├── 04_reader_validation.py          # Dominance scores (persona consistency)
├── 05_reader_archetypes.py          # Reader-level meta-clustering
├── 06_extract_examples.py           # Hand-picked examples → personas.json
├── 07_cluster_comparison.py         # Cross-work centroid cosine similarity
├── 08_generate_for_work_B.py        # Generate comments for held-out work
├── 09_compare_generated_vs_real.py  # Embedding / vocab / length evaluation
├── 10_extract_base_vocab.py         # Corpus → base vocabulary JSON
└── outputs/

tool/
├── backend/
│   ├── main.py                      # FastAPI, 5 endpoints
│   ├── personas.py                  # Combine / novel mode logic
│   ├── prompts.py                   # Prompt builders (reader, classify, novel, chat)
│   ├── retrieval.py                 # BGE-M3 in-memory KNN (804 comments)
│   └── requirements.txt
└── frontend/
    └── index.html                   # Single-file React app
```

## Why the Code Is Not Public

The personas rest on 804 real comments from real readers on my Joara works, collected over 8 years. Nicknames are hashed, but the words belong to the people who wrote them. I do not share them.

The pipeline, retrieval layer, and prompts are all built around this corpus. Releasing the code without the data would be incomplete. The methodology and results are in the [final report](report.pdf).

## Tech Stack

FastAPI, Claude API, BGE-M3 (1024d), KMeans. Ten offline pipeline scripts. Static HTML + React frontend.

## Ethics

Nicknames hashed (SHA256 + salt) at parse time. Raw comments never leave my machine. Both source works set to private on Joara. Work B stayed out of persona construction entirely.

One honest gap: the tool needs existing reader comments to build personas, which means writers with zero readers — the people it is for — cannot use it yet.

## About Me

I serialized fiction on Joara, hit Today's Best multiple times, and got a publication offer. I built this because I am a writer and I know how a single comment can make writers continue to write their stories. 
