---
title: "How Do I Scale Agent Systems?"
tags: [scaling, concurrency, queues, async, performance]
content_type: research
domain: engineering
---

# How Do I Scale Agent Systems?

## The Question

How do you go from one user to thousands running agents concurrently?

## Scaling Dimensions

| Dimension | Bottleneck | Solution |
|-----------|-----------|----------|
| More users | Agent instances | Horizontal scaling (more workers) |
| Harder tasks | Model capability | Larger models, multi-agent |
| Longer tasks | Context window, time | Async execution, multi-window |
| Higher throughput | API rate limits | Multi-provider, request queuing |

## Pattern 1: Worker Pool

Run agent tasks as background jobs:

```python
from celery import Celery

app = Celery('agents', broker='redis://localhost')

@app.task(bind=True, max_retries=3, time_limit=300)
def run_agent_task(self, task_id: str, task_description: str):
    try:
        agent = create_agent(tools=get_tools())
        result = agent.run(task_description)
        save_result(task_id, result)
    except Exception as e:
        self.retry(countdown=60)
```

Each worker runs one agent task at a time. Scale by adding workers.

## Pattern 2: Async with Webhooks

For tasks that take minutes or hours:

```python
# Submit task
@app.post("/tasks")
async def submit_task(task: TaskRequest):
    task_id = str(uuid4())
    await task_queue.put({"id": task_id, "task": task.description})
    return {"task_id": task_id, "status": "queued"}

# Check status
@app.get("/tasks/{task_id}")
async def get_task_status(task_id: str):
    return await get_status(task_id)

# Webhook callback when done
async def on_task_complete(task_id: str, result: dict):
    await notify_webhook(task_id, result)
```

## Pattern 3: Session Pooling

Reuse agent sessions for similar tasks:

```python
class AgentPool:
    def __init__(self, size: int = 10):
        self.pool = [create_agent() for _ in range(size)]
        self.available = asyncio.Queue()
        for agent in self.pool:
            self.available.put_nowait(agent)

    async def execute(self, task: str) -> str:
        agent = await self.available.get()
        try:
            result = await agent.run(task)
            return result
        finally:
            agent.reset()  # Clear conversation state
            await self.available.put(agent)
```

## Pattern 4: Tiered Processing

Route tasks to different processing tiers based on complexity:

```python
def route_task(task: dict) -> str:
    complexity = estimate_complexity(task)
    if complexity == "simple":
        return "fast_queue"    # Haiku, 30s timeout, cheap
    elif complexity == "medium":
        return "standard_queue"  # Sonnet, 120s timeout
    else:
        return "complex_queue"   # Opus, 600s timeout, expensive
```

## Rate Limit Management at Scale

When running many agents concurrently, aggregate rate limits matter:

```python
# Global rate limiter shared across all agent workers
rate_limiter = TokenBucket(
    rate=500,      # 500 RPM across all workers
    capacity=50,   # Burst of 50
)

async def call_llm_with_global_limit(request):
    await rate_limiter.acquire()
    return await llm.generate(request)
```

## Infrastructure Considerations

| Component | Tool | Purpose |
|-----------|------|---------|
| Task queue | Redis, RabbitMQ, SQS | Distribute tasks to workers |
| Worker pool | Celery, Dramatiq, custom | Execute agent tasks |
| Result store | PostgreSQL, Redis | Track task status and results |
| Rate limiter | Redis (distributed) | Global rate limit across workers |
| Monitoring | Prometheus + Grafana | Track queue depth, latency, costs |

## See Also

- `hub/deployment.md` — Deployment overview
- `research/deployment/cost-optimization.md` — Cost at scale
- `research/reliability/rate-limits-api-failures.md` — Rate limit handling
