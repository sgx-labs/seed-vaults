---
title: "Real-Time Patterns"
tags: [realtime, websocket, sse, polling, supabase, pusher, server-sent-events, live-updates]
content_type: hub
domain: typescript-fullstack
---

# Real-Time Patterns

## Overview

Real-time features (live notifications, collaborative editing, chat, dashboards) require data to flow from server to client without the client asking. The four approaches: polling, Server-Sent Events (SSE), WebSockets, and managed services. Each has different complexity, infrastructure requirements, and use cases.

## Comparison

| Factor | Polling | SSE | WebSocket | Managed (Supabase/Pusher) |
|--------|---------|-----|-----------|--------------------------|
| Direction | Client -> Server | Server -> Client | Bidirectional | Bidirectional |
| Complexity | Trivial | Low | High | Low |
| Infrastructure | None | None | WebSocket server | Third-party |
| Serverless friendly | Yes | Partial | No | Yes |
| Best for | Dashboards | Notifications, feeds | Chat, collaboration | Everything |

## Polling — The Simplest Option

Good enough for dashboards and data that updates every few seconds.

```typescript
"use client";

import { useQuery } from "@tanstack/react-query";

export function DashboardStats() {
  const { data } = useQuery({
    queryKey: ["stats"],
    queryFn: () => fetch("/api/stats").then((r) => r.json()),
    refetchInterval: 5000, // Poll every 5 seconds
    refetchIntervalInBackground: false, // Stop when tab is hidden
  });

  return <StatsDisplay data={data} />;
}
```

**When to use**: Data doesn't need sub-second updates, simple infrastructure, serverless deployment.

## Server-Sent Events (SSE) — One-Way Streaming

Server pushes events to the client over a single HTTP connection. Works with serverless (with caveats).

```typescript
// app/api/events/route.ts
export async function GET() {
  const stream = new ReadableStream({
    start(controller) {
      const encoder = new TextEncoder();

      // Send event every 2 seconds
      const interval = setInterval(() => {
        const data = JSON.stringify({ timestamp: Date.now(), count: getCount() });
        controller.enqueue(encoder.encode(`data: ${data}\n\n`));
      }, 2000);

      // Cleanup on close
      return () => clearInterval(interval);
    },
  });

  return new Response(stream, {
    headers: {
      "Content-Type": "text/event-stream",
      "Cache-Control": "no-cache",
      Connection: "keep-alive",
    },
  });
}

// Client
"use client";

import { useEffect, useState } from "react";

export function LiveFeed() {
  const [events, setEvents] = useState<Event[]>([]);

  useEffect(() => {
    const source = new EventSource("/api/events");

    source.onmessage = (event) => {
      const data = JSON.parse(event.data);
      setEvents((prev) => [...prev.slice(-49), data]); // Keep last 50
    };

    source.onerror = () => {
      source.close();
      // Reconnect after delay
      setTimeout(() => { /* re-create EventSource */ }, 3000);
    };

    return () => source.close();
  }, []);

  return <EventList events={events} />;
}
```

## WebSockets — Bidirectional

For chat, collaborative editing, multiplayer. Requires a persistent server (not serverless).

```typescript
// Using Socket.IO with Next.js (separate server or custom server)
// server.ts
import { createServer } from "http";
import { Server } from "socket.io";
import next from "next";

const app = next({ dev: process.env.NODE_ENV !== "production" });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  const server = createServer(handle as any);
  const io = new Server(server, { cors: { origin: "*" } });

  io.on("connection", (socket) => {
    socket.on("join-room", (roomId: string) => {
      socket.join(roomId);
    });

    socket.on("message", (data: { roomId: string; text: string }) => {
      io.to(data.roomId).emit("message", {
        text: data.text,
        sender: socket.id,
        timestamp: Date.now(),
      });
    });
  });

  server.listen(3000);
});
```

## Supabase Realtime — Managed Solution

Subscribe to database changes. Works with serverless. No infrastructure to manage.

```typescript
"use client";

import { createClient } from "@supabase/supabase-js";
import { useEffect, useState } from "react";

const supabase = createClient(
  process.env.NEXT_PUBLIC_SUPABASE_URL!,
  process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
);

export function LiveMessages({ channelId }: { channelId: string }) {
  const [messages, setMessages] = useState<Message[]>([]);

  useEffect(() => {
    // Subscribe to new inserts on the messages table
    const channel = supabase
      .channel(`messages:${channelId}`)
      .on(
        "postgres_changes",
        {
          event: "INSERT",
          schema: "public",
          table: "messages",
          filter: `channel_id=eq.${channelId}`,
        },
        (payload) => {
          setMessages((prev) => [...prev, payload.new as Message]);
        }
      )
      .subscribe();

    return () => { supabase.removeChannel(channel); };
  }, [channelId]);

  return <MessageList messages={messages} />;
}
```

## Decision Framework

1. **Dashboard with 5-30s updates?** -> Polling with React Query
2. **Live notifications, activity feeds?** -> SSE
3. **Chat, collaboration, gaming?** -> WebSocket or managed service
4. **Using Supabase already?** -> Supabase Realtime
5. **Deploying to serverless?** -> Polling, SSE, or managed service (not WebSocket)

## See Also

- `hub/state-management.md` — Client-side state for real-time data
- `hub/deployment-patterns.md` — Infrastructure requirements per approach
- `hub/performance-patterns.md` — Optimizing real-time renders
