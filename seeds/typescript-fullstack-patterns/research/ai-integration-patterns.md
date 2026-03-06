---
title: "AI Integration Patterns"
tags: [ai, vercel-ai-sdk, streaming, rag, openai, llm, embeddings, chat]
content_type: research
domain: typescript-fullstack
---

# AI Integration Patterns

## The Core Idea

AI features in fullstack apps follow three patterns: streaming chat (LLM responses), retrieval-augmented generation (RAG), and structured output (LLM returns typed data). The Vercel AI SDK handles streaming, model switching, and React hooks.

## Streaming Chat — Vercel AI SDK

```typescript
// app/api/chat/route.ts
import { openai } from "@ai-sdk/openai";
import { streamText } from "ai";

export async function POST(request: Request) {
  const { messages } = await request.json();

  const result = streamText({
    model: openai("gpt-4o"),
    system: "You are a helpful assistant for a project management app.",
    messages,
    maxTokens: 1000,
  });

  return result.toDataStreamResponse();
}
```

```typescript
// Client component
"use client";

import { useChat } from "ai/react";

export function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat",
  });

  return (
    <div>
      <div className="space-y-4">
        {messages.map((m) => (
          <div key={m.id} className={m.role === "user" ? "text-right" : "text-left"}>
            <p className="inline-block rounded-lg px-4 py-2 bg-gray-100">
              {m.content}
            </p>
          </div>
        ))}
        {isLoading && <p className="animate-pulse">Thinking...</p>}
      </div>

      <form onSubmit={handleSubmit} className="mt-4 flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="Ask something..."
          className="flex-1 border rounded px-3 py-2"
        />
        <button type="submit" disabled={isLoading}>Send</button>
      </form>
    </div>
  );
}
```

## Structured Output — Typed LLM Responses

Force the LLM to return data matching a Zod schema.

```typescript
import { openai } from "@ai-sdk/openai";
import { generateObject } from "ai";
import { z } from "zod";

const extractionSchema = z.object({
  sentiment: z.enum(["positive", "negative", "neutral"]),
  topics: z.array(z.string()).max(5),
  summary: z.string().max(200),
  actionItems: z.array(z.object({ task: z.string(), priority: z.enum(["high", "medium", "low"]) })),
});

export async function analyzeEmail(content: string) {
  const { object } = await generateObject({
    model: openai("gpt-4o"),
    schema: extractionSchema,
    prompt: `Analyze this email and extract structured data:\n\n${content}`,
  });

  return object; // Fully typed: { sentiment, topics, summary, actionItems }
}
```

## RAG — Retrieval-Augmented Generation

Search your data, inject relevant context, then generate.

```typescript
// 1. Create embeddings and store
import { openai } from "@ai-sdk/openai";
import { embed } from "ai";

async function indexDocument(doc: { id: string; content: string }) {
  const { embedding } = await embed({
    model: openai.embedding("text-embedding-3-small"),
    value: doc.content,
  });

  // Store in PostgreSQL with pgvector
  await db.$executeRaw`
    INSERT INTO documents (id, content, embedding)
    VALUES (${doc.id}, ${doc.content}, ${embedding}::vector)
    ON CONFLICT (id) DO UPDATE SET content = ${doc.content}, embedding = ${embedding}::vector
  `;
}

// 2. Search similar documents
async function searchDocs(query: string, limit = 5) {
  const { embedding } = await embed({
    model: openai.embedding("text-embedding-3-small"),
    value: query,
  });

  return db.$queryRaw`
    SELECT id, content, 1 - (embedding <=> ${embedding}::vector) AS similarity
    FROM documents
    ORDER BY embedding <=> ${embedding}::vector
    LIMIT ${limit}
  `;
}

// 3. Generate with context
export async function answerQuestion(question: string) {
  const relevantDocs = await searchDocs(question);
  const context = relevantDocs.map((d: any) => d.content).join("\n\n---\n\n");

  const result = streamText({
    model: openai("gpt-4o"),
    system: `Answer based on the following context. If the context doesn't contain the answer, say so.\n\nContext:\n${context}`,
    prompt: question,
  });

  return result.toDataStreamResponse();
}
```

## Model Switching

```typescript
import { openai } from "@ai-sdk/openai";
import { anthropic } from "@ai-sdk/anthropic";

// Switch models without changing application code
const model = process.env.AI_PROVIDER === "anthropic"
  ? anthropic("claude-sonnet-4-20250514")
  : openai("gpt-4o");

const result = await generateText({ model, prompt: "..." });
```

## Rate Limiting AI Endpoints

AI API calls are expensive. Use Upstash Ratelimit with a sliding window (e.g., 10 requests per minute per user). See `hub/api-route-patterns.md` for the rate limiting pattern.

```typescript
export async function POST(request: Request) {
  const session = await auth();
  if (!session) return Response.json({ error: "Unauthorized" }, { status: 401 });

  const { success } = await ratelimit.limit(session.user.id);
  if (!success) {
    return Response.json({ error: "Rate limit exceeded" }, { status: 429 });
  }

  // ... process AI request
}
```

## Common Mistakes

1. **Not streaming** — users stare at a spinner for 5-10 seconds. Stream the response.
2. **No rate limiting** — AI API calls cost money. One user can rack up hundreds of dollars.
3. **Sending too much context** — trim context to relevant chunks. More context != better answers.
4. **Hardcoding one provider** — use the AI SDK's provider abstraction for easy switching.
5. **Not validating structured output** — even with schema, validate the response before using it.

## See Also

- `hub/api-route-patterns.md` — API route patterns for AI endpoints
- `hub/realtime-patterns.md` — Streaming patterns
- `research/background-jobs.md` — Long-running AI tasks
