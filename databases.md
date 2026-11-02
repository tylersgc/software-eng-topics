Databases.Transactions

## ACID Transactions

**id:** db-acid-transactions

**question:** What does ACID mean, and why is “ACID compliant” underspecified?

**answer:** Atomicity = all-or-nothing writes; Consistency = application invariants; Isolation = concurrent transaction guarantees; Durability = commits survive covered failures. The acronym alone does not identify the isolation level or failure model.

**mechanism:**
- Atomicity: an aborted transaction does not partially commit.
- Consistency: business invariants must be specified and protected by app logic/constraints.
- Isolation: concurrency anomalies depend on the isolation level.
- Durability: persistence is relative to the database's promised failures.

**caveat:** ACID C is not CAP C. Not all “ACID” transactions are serializable.

**example:** Moving money between accounts needs atomic debit/credit and safe handling of concurrent transfers.

**related:** db-isolation-levels, sd-cap-acid-consistency

**source:** https://dataintensive.net/

---

Databases.Isolation

## Isolation Levels

**id:** db-isolation-levels

**question:** How do the common SQL isolation levels differ?

**answer:** Read Uncommitted permits the weakest behavior; Read Committed prevents dirty reads; Repeatable Read typically stabilizes previously observed data; Serializable ensures the result is equivalent to some sequential execution of transactions.

**mechanism:**
- Read Uncommitted: may expose uncommitted data (not in every implementation).
- Read Committed: each statement sees only committed values; repeated reads may differ.
- Repeatable Read: definition and implementation vary; PostgreSQL uses snapshot isolation.
- Serializable: prevents serialization anomalies, sometimes by aborting conflicting transactions.

**caveat:** Isolation level names alone do not fully specify allowed anomalies across vendors. In PostgreSQL, Repeatable Read avoids phantom reads yet permits write skew.

**related:** db-read-committed, db-snapshot-isolation, db-serializability

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Read Committed

**id:** db-read-committed

**question:** What does Read Committed guarantee and what anomaly remains?

**answer:** No dirty reads: a query cannot see another transaction's uncommitted writes. But two SELECTs in the same transaction may observe different committed versions (non-repeatable read/read skew).

**example:** Read account A, another transfer commits, then read account B; the observed pair may not correspond to one point in time.

**related:** db-isolation-levels, db-snapshot-isolation

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Snapshot Isolation and MVCC

**id:** db-snapshot-isolation

**question:** Why can snapshot isolation still violate an invariant?

**answer:** Each transaction reads a consistent snapshot, usually backed by MVCC (multiple row versions), but concurrent transactions reading the same snapshot can update different rows and both commit, causing write skew.

**mechanism:**
- MVCC maintains versions so readers can see a snapshot without blocking every writer.
- Snapshot isolation ordinarily prevents dirty reads and read skew.
- Write-write conflict handling does not necessarily detect cross-row dependency cycles.

**caveat:** Snapshot isolation is NOT generally serializable; “Repeatable Read” is vendor-dependent.

**related:** db-write-skew, db-serializability, db-isolation-levels

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Write Skew

**id:** db-write-skew

**question:** How can two valid transactions jointly violate an invariant under snapshot isolation?

**answer:** Both inspect a shared condition and write different rows. They do not conflict at the row-writing level, yet their combined result violates the condition.

**example:** Alice and Bob both see two doctors on call, each independently removes themselves, and both commit—leaving no doctor on call.

**tradeoffs:** Use Serializable, a suitable constraint/locking strategy, or redesign the data to make the invariant enforceable. Explicit locks must cover all relevant competing operations.

**related:** db-snapshot-isolation, db-serializability

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Serializability

**id:** db-serializability

**question:** What does serializability guarantee, and how can a database implement it?

**answer:** The committed outcome of concurrently executed transactions must be equivalent to some one-at-a-time ordering. It does not by itself require that ordering to match wall-clock order.

**mechanism:**
- Actual serial execution: run transactions one at a time.
- Two-phase locking: hold appropriate locks to prevent unsafe overlap.
- Serializable Snapshot Isolation (SSI): detect dangerous read/write dependencies and abort some transactions.

**tradeoffs:** May increase blocking, aborts, retries or coordination overhead; applications need retry logic for serialization failures.

**caveat:** Serializability differs from linearizability; strict serializability gives serial execution semantics plus real-time ordering.

**related:** db-write-skew, sd-linearizability, db-snapshot-isolation

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Dirty Read

**id:** db-dirty-read

**question:** What is a dirty read?

**answer:** Transaction A reads a value written by transaction B before B commits. If B rolls back, A relied on a value that never became committed state.

**example:** B temporarily changes balance 100 to 20; A reads 20; B rolls back to 100.

**caveat:** SQL Read Uncommitted may allow this, but PostgreSQL maps Read Uncommitted to Read Committed and never performs dirty reads.

**related:** db-read-committed, db-isolation-levels

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Nonrepeatable Read

**id:** db-nonrepeatable-read

**question:** What is a nonrepeatable read, and does it automatically trigger retry?

**answer:** Within one transaction, reading the same row twice returns different values because another transaction committed an update between the reads. At Read Committed, this is allowed and ordinarily triggers neither an error nor an automatic retry.

**example:** A reads balance=100. B updates balance=200 and commits. A reads again and sees 200, then may commit normally.

**caveat:** Repeatable Read/Snapshot Isolation usually prevents the changed observation by retaining a snapshot. Serializable implementations can abort transactions for unsafe dependency patterns, not simply because a read was repeated.

**related:** db-dirty-read, db-phantom-read, db-read-committed, db-serializability

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Phantom Read

**id:** db-phantom-read

**question:** How is a phantom read different from a nonrepeatable read?

**answer:** The same predicate query inside one transaction returns a different set of matching rows because another transaction inserted, deleted or changed qualifying rows and committed. A nonrepeatable read instead concerns changed values of a row read again.

**example:** A runs SELECT COUNT(*) FROM orders WHERE status='pending' and sees 5; B inserts a pending order and commits; A reruns the query and sees 6.

**caveat:** Under the SQL standard, Repeatable Read need not prevent phantoms. PostgreSQL's Repeatable Read uses snapshot isolation and *does* prevent them, but still permits serialization anomalies.

**related:** db-nonrepeatable-read, db-snapshot-isolation, db-isolation-levels

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.Isolation

## Serialization Anomaly

**id:** db-serialization-anomaly

**question:** What is a serialization anomaly, and what happens under Serializable isolation?

**answer:** Concurrent transactions commit a result that cannot be explained by *any* ordering where those transactions ran one at a time. A Serializable database must avoid this, often by rolling back a transaction and returning a retryable serialization failure.

**example:** Two on-call doctors each see the other present and independently go off call. Under snapshot isolation both may commit, violating the rule that at least one remain; either possible serial ordering would stop the second change.

**mechanism:** PostgreSQL's Serializable Snapshot Isolation (SSI) tracks hazardous read/write dependencies and can abort a transaction instead of forcing all transactions to execute one by one.

**caveat:** A serialization anomaly is not synonymous with a nonrepeatable read. Read Committed may allow a nonrepeatable read without raising any error. PostgreSQL's serialization failure SQLSTATE is 40001; retry the entire transaction, not only the last query.

**related:** db-write-skew, db-serializability, db-snapshot-isolation

**source:** https://www.postgresql.org/docs/current/transaction-iso.html

---

Databases.StorageInternals

## Storage-Engine Organization vs Physical Layout

**id:** db-storage-axes

**question:** Are B-Trees/LSM-Trees at a different layer from row/column stores?

**answer:** Both concern physical storage design, but on different axes. B-Trees/LSM-Trees organize reads, writes and key access; row/column formats determine which field values are physically grouped together. They can coexist within a storage engine rather than forming a strict two-layer stack.

**example:** MySQL InnoDB: B+Tree clustered index whose leaves store row records. ClickHouse MergeTree: sorted immutable parts with columnar files and background merging. PostgreSQL: heap-organized row table plus separate B-Tree indexes.

**caveat:** B-Tree can be an index over a heap rather than the primary data store. An LSM tree typically organizes key/value records; a database may also have several indexing and encoding structures.

**related:** db-btree-vs-lsm, db-row-vs-column

**source:** https://dev.mysql.com/doc/refman/8.4/en/innodb-index-types.html
**source_2:** https://clickhouse.com/docs/en/merges

---

Databases.StorageInternals

## B-Tree vs LSM-Tree

**id:** db-btree-vs-lsm

**question:** How do B-Tree and LSM-Tree storage organizations differ, especially on writes?

**answer:** A B(+)-Tree updates relevant pages of a balanced disk-page tree; an LSM-Tree accumulates sorted in-memory updates, flushes immutable sorted SSTables, then compacts files in the background.

**mechanism:**
- B(+)-Tree: traverse internal pages to leaf pages; update data/index pages; page splits possible.
- LSM: append WAL for crash recovery, update memtable, flush sorted SSTables, perform compaction.
- Both may use a WAL; LSM's write advantage comes from batching and avoiding immediate random updates to the main on-disk structure, not from skipping durability.
- B-Tree tends toward predictable point/range reads; LSM can improve write throughput while incurring compaction and read/write amplification.

**tradeoffs:** Actual performance depends on write/read mix, storage hardware, memory, compaction configuration and indexing.

**related:** db-storage-axes, db-row-vs-column

**source:** https://github.com/facebook/rocksdb/wiki/RocksDB-Overview
**source_2:** https://www.postgresql.org/docs/current/btree.html

---

Databases.StorageInternals

## Row-Oriented vs Column-Oriented Storage

**id:** db-row-vs-column

**question:** What does row vs column storage change about physical data layout?

**answer:** A row store places fields from a record together; a column store groups values from the same field together, usually in row groups and column chunks. Row layout favors fetching or changing full records; column layout favors scanning selected fields, compression and aggregations.

**example:** SELECT * WHERE id=2 naturally suits row-oriented OLTP; SELECT AVG(age) over millions of rows often suits columnar OLAP. Parquet uses row groups containing separate column chunks.

**caveat:** These are workload tendencies, not exclusivity. Both can use indexes; many engines use hybrids. Columnar storage is a file/data layout, not synonymous with LSM or a B-Tree.

**related:** db-storage-axes, db-btree-vs-lsm, db-wide-column-vs-columnar

**source:** https://parquet.apache.org/docs/concepts/
**source_2:** https://clickhouse.com/resources/engineering/what-is-columnar-storage

---

Databases.StorageInternals

## Wide-Column vs Columnar Analytics

**id:** db-wide-column-vs-columnar

**question:** Is Apache Cassandra's “wide-column store” the same as an analytical column store?

**answer:** No. Wide-column describes Cassandra's distributed, partition-key-oriented data model. It uses an LSM-based storage engine with memtables and immutable SSTables. Analytical column stores physically group field values by column to accelerate scans and aggregates.

**example:** Cassandra: write-heavy, partition-key/cluster-key lookup workloads. ClickHouse: columnar compressed data parts, sparse sorting-key index and background part merges for analytics.

**caveat:** ClickHouse's MergeTree is a merge-based columnar engine, not simply the same layout or code as a classic RocksDB LSM tree.

**related:** db-btree-vs-lsm, db-row-vs-column

**source:** https://cassandra.apache.org/doc/stable/cassandra/architecture/storage-engine.html
**source_2:** https://clickhouse.com/docs/en/merges

---
