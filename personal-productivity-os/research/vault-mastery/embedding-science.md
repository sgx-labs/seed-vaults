---
title: "How Vector Embeddings Work in SAME"
tags: [vault-mastery, embeddings, vector-search, machine-learning, search]
content_type: research
domain: productivity
workstream: vault-mastery
---

# How Vector Embeddings Work in SAME

Understanding how embeddings work is not required to use SAME, but it explains why certain note-writing practices produce better search results. This is the simplified version, aimed at people who want the intuition without the math.

## What Is an Embedding

An embedding is a list of 768 numbers that represents the "meaning" of a piece of text. SAME uses the nomic-embed-text model, which runs locally via Ollama, to convert each chunk of your notes into one of these 768-dimensional vectors.

Think of it as a meaning fingerprint. Just as a fingerprint uniquely identifies a person, an embedding uniquely identifies the semantic content of a text passage. Two passages about the same topic will have similar fingerprints. Two passages about unrelated topics will have different fingerprints.

## Why Similar Concepts Have Similar Embeddings

The embedding model was trained on billions of text pairs that humans labeled as "similar" or "different." Through this training, the model learned to place semantically related text close together in 768-dimensional space and unrelated text far apart.

This means:
- "Context switching hurts productivity" and "Multitasking reduces output" produce nearby vectors — the model understands they mean similar things
- "Context switching hurts productivity" and "Italian pasta recipes" produce distant vectors — the model understands they are unrelated
- The model captures meaning, not just keywords. It knows that "burnout" is closer to "exhaustion" than to "fire"

When you search for "how to avoid burnout," SAME converts your query into an embedding and finds the notes whose embeddings are closest. This is why semantic search feels like magic — it finds notes by meaning, not just matching words.

## Why One-Topic Notes Search Better

Here is the key insight. An embedding is a single point in 768-dimensional space. It represents the average meaning of the entire text chunk. When a chunk is about one topic, that average is precise — it sits squarely in the region of space that represents that topic.

When a chunk is about three topics — say, time management, cooking, and travel — the embedding is the average of all three meanings. It sits somewhere in the middle, not close to any of them. A search for "time management" finds it, but weakly, because the vector is being pulled toward cooking and travel.

Analogy: if someone asks "where do you live?" and you live in one city, your answer is precise. If you split your time between three cities on three continents, your "average location" is somewhere in the ocean — close to nothing.

One concept per note = one precise point in meaning space = strong search matches.

## Why Titles and First Paragraphs Matter More

SAME prepends the note's title to the text before generating the embedding. The resulting embedding is computed from `title + "\n" + chunk_text`. Since embedding models process text sequentially and weight earlier tokens more heavily (a property inherited from the transformer architecture), the title has outsized influence on the final vector.

The first paragraph of the body text comes next in the sequence, so it carries the second-most influence. By the time the model reaches the fifth paragraph, each word's contribution to the overall embedding is diluted by everything that came before.

Practical implication: front-load your meaning. Put the core concept in the title and reinforce it in the first paragraph. Do not bury the lead.

## How Chunking Works

Long notes are split into chunks so each section gets its own embedding. SAME chunks at H2 heading boundaries:

```markdown
# Note Title           ← not a chunk boundary

## Section One         ← chunk 1 starts here
Content about topic A

## Section Two         ← chunk 2 starts here
Content about topic B
```

Each chunk is embedded separately with the title prepended. This means Section One and Section Two get distinct vectors, even though they are in the same file. A search for topic A can find Section One without being diluted by Section Two.

If a chunk exceeds 7,500 characters (the model's context window), it is split further at paragraph boundaries. These "overflow" splits are less clean because they break at arbitrary points rather than semantic boundaries. This is why keeping notes concise — under 200 lines — avoids forced splits.

Content before the first H2 heading becomes an "(intro)" chunk. Notes without any H2 headings are embedded as a single chunk.

## The Distance Threshold

SAME uses cosine distance to measure how far apart two embeddings are. A distance of 0 means identical meaning. SAME's maximum distance cutoff is 16.3 — any result farther than this is considered irrelevant and discarded.

Results also need to pass a composite score threshold (0.70) that combines vector similarity, recency, and confidence. This means a note needs to be both semantically relevant and reasonably current to appear in search results. Decisions and hub notes have an advantage here because their confidence never decays.

## The Quality Chain

```
Good notes
  → precise embeddings
    → accurate search results
      → useful agent responses
        → better conversations
          → more knowledge captured
            → even better notes
```

This is the compound loop. Every well-written note improves future search quality. Every useful search result leads to better conversations. Every good conversation produces more notes. The system gets smarter as you use it.

The weakest link determines the chain's strength. Vague notes with generic titles produce blurry embeddings that return mediocre results. Focused notes with descriptive titles produce sharp embeddings that return exactly what you need.

See: `research/vault-mastery/optimizing-notes-for-search.md`, `research/vault-mastery/frontmatter-guide.md`
