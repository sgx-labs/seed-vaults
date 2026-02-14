---
title: "Message Queue Backup Runbook (SQS/RabbitMQ/Kafka)"
tags: [queue, SQS, RabbitMQ, Kafka, backup, consumer-lag, runbook, P1]
content_type: research
domain: operations
---

# Message Queue Backup Runbook

## The Question

What do I do when the message queue is backing up?

## Symptoms

- Queue depth growing (messages accumulating)
- Consumer lag increasing
- Downstream processing delayed
- Application logs: "message processing timeout" or "queue full"
- Dead letter queue receiving messages

## Severity: P1 (P2 if only affecting background jobs)

## Step 1: Assess the backup (3 minutes)

### AWS SQS

```bash
# Check queue depth
aws sqs get-queue-attributes --queue-url QUEUE_URL \
  --attribute-names ApproximateNumberOfMessages,ApproximateNumberOfMessagesNotVisible

# Check dead letter queue
aws sqs get-queue-attributes --queue-url DLQ_URL \
  --attribute-names ApproximateNumberOfMessages

# Check oldest message age
aws sqs get-queue-attributes --queue-url QUEUE_URL \
  --attribute-names ApproximateNumberOfMessagesDelayed
```

### RabbitMQ

```bash
# Check queue depth
rabbitmqctl list_queues name messages consumers

# Check for queues with no consumers
rabbitmqctl list_queues name messages consumers | awk '$3 == 0 && $2 > 0'

# Check node health
rabbitmqctl status | grep -A5 "memory\|disk"
```

### Kafka

```bash
# Check consumer lag
kafka-consumer-groups.sh --bootstrap-server kafka:9092 \
  --describe --group my-consumer-group

# Check topic partition offsets
kafka-run-class.sh kafka.tools.GetOffsetShell \
  --broker-list kafka:9092 --topic my-topic
```

## Step 2: Identify the cause

```
Queue growing because:
1. Consumers are down or crashed      → Restart consumers
2. Consumers are too slow             → Scale consumers
3. Message volume spiked              → Scale consumers + investigate source
4. Consumer is in error loop          → Fix consumer bug, check DLQ
5. Downstream dependency is slow/down → Fix downstream first
```

## Step 3: Fix

### Consumers down — restart them

```bash
# Kubernetes
kubectl get pods -n production -l app=queue-worker
kubectl rollout restart deployment/queue-worker -n production

# Verify consumers reconnected
kubectl logs -n production deploy/queue-worker --since=2m | grep "connected\|consuming"
```

### Consumers too slow — scale up

```bash
# Kubernetes: increase replicas
kubectl scale deployment/queue-worker -n production --replicas=10

# AWS Lambda (SQS trigger): increase concurrency
aws lambda put-function-concurrency \
  --function-name queue-processor \
  --reserved-concurrent-executions 50
```

### Consumer in error loop — check and fix

```bash
# Check consumer logs for errors
kubectl logs -n production deploy/queue-worker --since=30m | grep -i "error\|fail\|exception" | head -20

# Check dead letter queue for failed messages
aws sqs receive-message --queue-url DLQ_URL --max-number-of-messages 5 \
  --attribute-names All --message-attribute-names All

# If a bad message is poisoning the queue:
# Move it to DLQ manually or skip it
```

## Step 4: Drain the backlog

Once consumers are healthy and scaled, monitor the queue draining:

```bash
# Watch queue depth decrease
watch -n 10 'aws sqs get-queue-attributes --queue-url QUEUE_URL \
  --attribute-names ApproximateNumberOfMessages --output text'

# Estimate drain time
# drain_time = queue_depth / (consumer_throughput_per_second * num_consumers)
```

## Step 5: Scale back down

After the backlog is drained:

```bash
# Scale consumers back to normal
kubectl scale deployment/queue-worker -n production --replicas=3

# Verify queue stays stable
# Monitor queue depth for 30 minutes
```

## Prevention

1. Alert on queue depth (warning at 1000, critical at 10000)
2. Alert on consumer lag growth rate
3. Auto-scale consumers based on queue depth
4. Set up dead letter queues for all queues
5. Monitor DLQ — messages there mean something is failing silently

## Related

- `research/runbooks/slow-api-debugging.md` — If queue backup causes slow APIs
- `research/runbooks/high-cpu-memory-disk.md` — Consumer resource issues
- `research/infrastructure/scaling-procedures.md` — Scaling consumers
