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
