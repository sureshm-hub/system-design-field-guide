# data storage and retrieval
* two of the most popular indexing structures used in storage engines are Log-Structured Merge Trees (LSM Trees) 
and B-Trees

## B-Trees
* The traditional indexing structure used in many relational databases. 
* They are balanced trees that maintain a logarithmic time complexity for inserts, updates, and deletes, making them 
  well-suited for use cases where data is updated frequently.
### Key Design Patterns and Characteristics:
* In-Place Modification:  Modifies data directly on disk, causing random I/O.
* Balanced Tree Structure:  Maintains a rigid, hierarchical structure to minimize disk seeks for reads.
* Page-Oriented:  Organizes data into fixed-size blocks (pages), usually 4-16 KB.
* Use Cases: SQL databases like MySQL/InnoDB and PostgreSQL.

## LSM Trees (Log-Structured Merge-tree )
* For write-intensive use cases LSM is better suited.
* LSM Trees write new data to a series of append-only data structures called SSTables.
### Key Design Patterns and Characteristics:
* Append-Only/Log-Structured: Writes are sequential, buffering data in memory (MemTable) before flushing to disk.
* Immutable Sorted Files (SSTables): Disk files are never modified; new data is added, and older data is merged  
  asynchronously.
* Compaction: Background processes merge sorted files to eliminate duplicates and fragmentation.
* How It Works:
  * Writes: New data (inserts/updates/deletes) goes to a new Memtable in memory first, then flushes as a brand-new  
  SSTable when full. Older SSTables stay untouched.
  * Changes: Updates create fresh entries in newer SSTables (tombstones for deletes); the latest value is picked during 
  reads by checking files from newest to oldest.
  * Merging: Old data is cleaned up asynchronously via compaction, which merges multiple SSTables into a new one 
  (discarding obsolete entries) without altering originals. 
  * This design prioritizes fast sequential writes over mutable files, trading read complexity for write performance 
    in high-throughput systems


https://bytebytego.com/guides/b-tree-vs/
https://tikv.org/deep-dive/key-value-engine/b-tree-vs-lsm/