SystemDesign.DistributedSystems

## CAP Theorem

**id:** sd-cap-theorem

**question:** What does CAP actually say, and why is “pick two” misleading?

**answer:** Under a network partition, a replicated read/write system cannot guarantee both linearizability (C) and successful responses to every operation on a non-failing node (A). Partitions are a potential fault, not a property to opt out of.

**mechanism:**
- C = linearizable operations, respecting real-time order.
- A = each operation on a non-failing node eventually responds successfully (no fixed latency bound).
- P = communication between nodes can fail or be arbitrarily delayed.

**caveat:** CAP is a genuine impossibility result but its assumptions are narrow. “CP/AP database” labels obscure latency, partial availability, operation-specific guarantees, and failure models.

**example:** Disconnected datacenters cannot each accept conflicting writes to one register while both promising a single up-to-date value.

**related:** sd-linearizability, sd-cap-acid-consistency, db-acid-transactions

**source:** https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html

---

SystemDesign.Consistency

## Linearizability

**id:** sd-linearizability

**question:** How does linearizability differ from serializability?

**answer:** Each operation appears instantaneous between invocation and response and respects real-time ordering. Serializability concerns multi-operation transactions and does not necessarily respect real-time order.

**mechanism:**
- A read starting after an acknowledged write must see that write or a newer value, assuming no intervening overwrite.
- Concurrent operations may be ordered either way.
- A stale replica read can violate linearizability.

**tradeoffs:** Real-time coordination may cost latency and availability during communication failures.

**caveat:** Strict serializability combines both real-time ordering and transaction serializability.

**related:** db-serializability, sd-cap-theorem

**source:** https://jepsen.io/consistency/models/linearizable

---

SystemDesign.Consistency

## CAP vs ACID: Consistency

**id:** sd-cap-acid-consistency

**question:** Do the C and A in CAP mean the same things as in ACID?

**answer:** No. CAP C means linearizability (real-time visibility); ACID C means application/data invariants. CAP A means availability; ACID A means atomicity (all-or-nothing).

**caveat:** ACID isolation, particularly serializability, is more closely related to transaction concurrency than ACID consistency is.

**example:** A correct bank transfer may preserve total money, even while a replica temporarily returns an outdated balance.

**related:** sd-cap-theorem, db-acid-transactions, db-serializability

**source:** https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html

---

SystemDesign.Messaging

## Kafka vs RabbitMQ

**id:** sd-kafka-vs-rabbitmq

**question:** When should you choose Kafka rather than RabbitMQ?

**answer:** Kafka is primarily a partitioned, durable event log for independently replayable event streams, large fan-out and data pipelines. RabbitMQ's traditional queues emphasize broker-side routing, distributing jobs to workers, message acknowledgments, retries and dead-letter handling.

**mechanism:**
- Kafka: producers append to topic partitions; ordinary consumer groups track offsets; reading does not delete records, which remain until retention/compaction removes them.
- RabbitMQ queues: producers publish to exchanges that route into queues; workers consume and ack or reject messages; acknowledged messages can be removed from queues.
- Kafka guarantees log order within a partition, not a topic-wide total order. RabbitMQ queue delivery order can diverge from processing/completion order with concurrent workers or redelivery.
- Kafka partitions scale conventional consumer groups; RabbitMQ queues scale competing workers and can route copies to multiple queues.

**tradeoffs:** Prefer Kafka for CDC, audit/event history, replay, stream processing and many independent downstream consumers. Prefer RabbitMQ queues for task dispatch, routing rules, delayed retries, priorities and job-oriented backpressure.

**caveat:** Not an absolute split: RabbitMQ Streams/Superstreams offer durable replayable logs; modern Kafka share groups provide queue-like per-record acknowledgment and shared consumption. Exactly-once business side effects aren't automatic on either system.

**example:** `OrderPlaced` consumed by analytics, warehouse and email independently -> Kafka. Dispatch a specific image-conversion task to one worker with retry/DLQ -> RabbitMQ queue.

**related:** sd-message-delivery, sd-consumer-groups

**source:**
- https://www.rabbitmq.com/docs/compare/kafka
- https://kafka.apache.org/44/design/design/

---

SystemDesign.Messaging

## Consumer Groups vs Competing Consumers

**id:** sd-consumer-groups

**question:** How does one message reach multiple services in Kafka vs RabbitMQ?

**answer:** In Kafka's standard model, each consumer group has its own progress for each topic-partition; different groups each process the same event. In RabbitMQ's queue model, workers on one queue compete for messages; to fan out to services, route one publication into multiple queues via an exchange.

**mechanism:**
- Kafka: partition offsets are retained independently per group; within one standard group, a partition is assigned to at most one consumer at a time.
- RabbitMQ: a queue delivers work among competing consumers; an exchange's bindings can route copies to multiple distinct queues.
- Kafka share groups can share records more flexibly, including per-record acknowledgment, while RabbitMQ Streams support multiple independent readers and replay.

**caveat:** Replication, partitioning and logical subscription are separate concerns. Parallel handling may reorder completions even if source ordering is preserved.

**related:** sd-kafka-vs-rabbitmq, sd-message-delivery

**source:**
- https://kafka.apache.org/44/design/design/
- https://www.rabbitmq.com/docs/streams

---

SystemDesign.Messaging

## Delivery Guarantees, Acks, and Retries

**id:** sd-message-delivery

**question:** Do Kafka or RabbitMQ guarantee that a business operation executes exactly once?

**answer:** No general end-to-end exactly-once guarantee for arbitrary external side effects. Network failures can happen between processing and acknowledging progress, causing redelivery; build idempotent handlers and deliberate retry behavior.

**mechanism:**
- RabbitMQ queue: manual ack after processing; if connection closes before ack, unacked messages can be requeued. Use publisher confirms for broker acceptance, and DLQs/retry policies for poison messages.
- Kafka standard groups: consumer commits an offset/checkpoint; crash after processing but before committing may replay the event.
- Kafka transactional/exactly-once semantics can cover supported Kafka read-process-write workflows, not arbitrary external databases/emails without additional coordination.
- Modern Kafka share groups also support individual record ack/release/reject handling.

**example:** Before sending a payment, record a unique operation/idempotency key to prevent a duplicate charge on retry.

**related:** sd-kafka-vs-rabbitmq, sd-consumer-groups

**source:**
- https://www.rabbitmq.com/docs/confirms
- https://kafka.apache.org/44/design/design/

---
