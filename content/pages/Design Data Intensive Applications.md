---
tags:
- Software Architecture
- Microservice
title: Design Data Intensive Applications
categories:
date: 2022-08-15
lastMod: 2022-10-18
---
This is my reading notes of [Design Data Intensive Applications](https://www.oreilly.com/library/view/designing-data-intensive-applications/9781491903063/).

Preface


  + if data (quantity, complexity, change speed, etc) is an application's primary challenge, then we call the application _**data-intensive**_. As opposed to _**compute-intensive**_, where CPU cycles are the bottleneck.

  + When we think about data systems, not just think how they work, but also why they work that way, and what questions we need to ask.

  + Reference materials: [ddia-references](https://github.com/ept/ddia-references).

## Chapter 1: Reliable, Scalable, and Maintainable Applications


  + For data-intensive applications, the bigger problem are usually the amount of data, the complexity of data, and the speed at which it's changing.

  + data system building blocks:


    + databases: store data so that they, or another application, can find it again later

    + caches: remember the result of an expensive operation, to speed up reads

    + search indexes: allow users to search data by keyword or filter it in various ways

    + stream processing: send a message to another process, to be handled asynchronously

    + batch processing: periodically crunch a large amount of accumulated data

  + ### Reliability


    + Reliability means the system will continue to work correctly (how do we define correctness?), even when things go wrong.

    + The things that can go wrong are called _faults_, and systems that anticipate faults and can cope with them are called _fault-tolerant_ and _resilient_.

      + Fault

        + A fault is not the same as a failure. A fault is usually defined as one component of the system deviating from its spec, whereas a failure is when the system as a whole stops providing the required service to the user.

        + As long as you can restore a backup onto a new machine fairly quickly, the downtime in case of failure is not catastrophic in most applications.

        + There are several types of faults: hardware fault, software faults, human errors...

        + Humans are known to be unreliable, even when they have the best intentions.


          + How to make our system reliable, in spite of unreliable humans?

            + Design systems in a way that minimizes opportunities for error.

            + Decouple the places where people make the most mistakes from the places where they can cause failures.

            + Test thoroughly at all levels.

            + Allow quick and easy recovery from human errors, to minimize the impact in the case of a failure.

            + Set up detailed and clear monitoring.

            + Implement good management practices and training.

  + ### Scalability


    + Scalability is the term we use to describe a system's ability to cope with increased load. A common question is what are our options to handle the additional load?

    + Load

      + Load can be described with a few numbers which we call _load parameters_, such as requests per second for a web server the ratio of reads or writes to a database, etc. The best choice of parameters depends on the architecture of your system.

    + Once we have described the load on our system, we can investigate what happens when the load increases. We can look at it in two ways:

      + When we increase a load parameter and keep the system resources unchanged, how is the performance of the system affected?

      + When we increase a load parameter, how much do we need to increase the resources if we want to keep the performance unchanged?

    + Performance

      + In a batch processing system, we usually care about _throughput_.

      + In online systems, _response time_ is more important.

        + Even if we only make the same request over and over again, we'll get a slightly different response time on every try. Sometimes, the response time can vary a lot. Therefore, we'd better think of response time not as a single number, but as a **_distribution_** of values that we can measure.

          + It's common to see the _average_ response time of a service reported. However, the average is not a very good metric is we want to know our "typical" response time because it doesn't tell us how many users actually experienced that delay. Usually it is better to use **_percentiles_**.


            + Median is a good metric if we want to know how long users typically have to wait,. The median is also known as the _50th percentile_, or _p50_.

            + In order to figure out how bad the outliers are, we can look at higher percentiles: the _95th_, _99th_, and the _99.9th_ percentiles are common (abbreviated _p95_, _p99_, and _p999_).

            + High percentiles of response times, also known as **_tail latencies_**, are important because they directly affect users' experience of the service.

    + An architecture that scales well for a particular application is built around assumptions of which operations will be common and which will be rare.

  + ### Maintainability


    + The majority of the cost of software is not in its initial development, but in its ongoing maintenance.

    + Maintainability is in essence about making life better for the engineering and operations teams who need to work with the system.

    + Three design principles for a better maintainable software system:

      + Operability

        + Make it easy for operations teams to keep the system running smoothly.

        + Good operations can often work around limitations of bad (or incomplete) software, but good software cannot run reliably with bad operations.

        + Good operability means making routine tasks easy, allowing the operations team to focus their efforts on high-value activities.

        + Good operability also means having good visibility into the system's health, and having effective ways of managing it.

      + Simplicity

        + Make it easy for new engineers to understand the system.

        + A software project mired in complexity is sometimes described as a _big ball of mud_.

        + Making a system simpler doesn't necessarily mean reducing its functionality. Moseley and Marks define complexity as accidental if it is not inherent in the problem that the software solves but arises only from the implementation.

        + One of the best tools we have for removing accidental complexity is **_abstraction_**.

      + Evolvability

        + Make it easy for engineers to make changes to the system in the future, also known as extensibility, modifiability, or plasticity.

      + 

## Chapter 2: Data Models and Query Languages


  + Data models are perhaps the most important part of developing software, because they not only affect how the software is written, but also how we think about the problem that we're solving.

  + ### Relational Model Versus Document Model


    + SQL

      + SQL may be the best-known data model, it's based on the relational model: data is organized into _relations_ (called tables in SQL), where each relation is an unordered collection of _tuples_ (rows in SQL).

    + NoSQL

      + NoSQL, or _Not Only SQL_ appeared in the 2010s.

    + {{< logseq/orgTIP >}}Different applications have different requirements, and the best choice of technology for one use case may well be different from the best choice for another use case.
{{< / logseq/orgTIP >}}

    + The JSON representation of data has better _locality_ than the multi-table schema. However, in document databases, joins are not needed for one-to-many tree structures, and support for joins is often weak.

      + {{< logseq/orgTIP >}}A document is usually stored as a single continuous string. If our application needs to access the entire document, there is a performance advantage to this _storage locality_. However, the locality advantage only applies if we need large parts of the document at the same time, The database typically needs to load the entire document, even if we access only a small portion of it, which can be wasteful on large documents.
On update to a document, the entire document usually needs to be written. So, keep documents fairly small and avoid writes that increase the size of a document is a good practice.
{{< / logseq/orgTIP >}}

    + The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by the application. The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships.

    + Document databases are sometimes called _schemaless_, but that's misleading, as the code that reads the data usually assumes some kind of structure.

    + schema-on-read vs schema-on-write

      + _schema-on-read_: the structure of the data is implicit, and only interpreted when the data is read.

      + _schema-on-write_: the traditional approach of relational databases, where the schema is explicit and the database ensure all written data conforms to it.

      + From a programming language perspective, schema-on-read is similar to dynamic (runtime) type checking, whereas schema-on-write is similar to static (compile-time) type checking.

    + A hybrid of the relational and document models is a good route for databases to take in the future.

  + ### Query Language for Data


    + SQL is a _declarative_ query language. In declarative query language, we just specify the pattern of the data we want.

    + An imperative language tells the computer to perform certain operations in a certain order, or how to achieve a goal.

    + _MapReduce_ is a programming model for processing large amounts of data in bulk across many machines. MapReduce is neither a declarative query language nor a fully imperative query API, but somewhere in between.

  + ### Graph-Like Data Models


    + A graph consists of two kinds of objects: _vertices_ (also known as _nodes_ or _entities_) and _edges_ (also known as _relationships_ or _arcs_).

    + Graphs are not limited to homogeneous data: an equally powerful use of graphs is to provide a consistent way of storing completely different types of objects in a single datastore.

    + 

## Chapter 3: Storage and Retrieval
  + One the most fundamental level, a database needs to do two things: when you give it some data, it should store the data, when when you ask it again later, it should give the data back to you.

  + ### Data Structures That Power Your Database


    + In order to efficiently find the value for a particular key in the database, we need a data structure called _index_. The general idea behind index is to keep some additional metadata on the side, which acts as a signpost and helps you to locate the data you want. An index is an additional structure that derived from the primary data.

    + An important trade-off in storage systems is about the index: well-chosen indexes speed up read queries, but every index slows down writes. For this reason, database don't usually index everything by default, but require its user to choose indexes manually.

    + #### Hash Indexes


      + Hash index is for key-value data, and key-value stores are usually implemented as a hash map (hash table).

        + A simple hash indexing strategy may look like this: keep an in-memory hash map where every key is mapped to a byte offset in the data file---the location at which the value can be found.

![image.png](/assets/ddia/simple_hash_index.png)

      + Hash index is usually used with _append-only_ file (log) write strategy, which means the key-value pairs appear in the order that they were written, and values later in log take precedence over values for the same key earlier in that log.

      + The hash table index is simple, but has some limitations

        + The hash table must fit in memory, so it is not very friendly to large data.

        + Range queries are not efficient.

    + #### SSTables


      + In simple hash index, keys are not sorted. _Sorted String Table_ (or _SSTable_) require that the sequence of key-value pairs is **sorted by key**. Append new key-value pairs to the log will be unsuitable.

      + In order to find a particular key in the file, we no longer need to keep an index of all the keys in memory. In stead, we only need an in-memory index to tell us the offset for some of the keys and the index can be sparse.

      + The biggest problem is how to get our data sorted by key in the first place.


        + Some data structures, such as red-black trees and AVL trees, can help us insert keys in any order and read them back in sorted order.

        + A simple storage engine may work as follow:

          + When a write comes in, add it to an in-memory balanced tree data structure (called _memtable_).

          + Write the memtable out to disk as an SSTable file when the memtable gets bigger than some treshhold.

          + In order to serve a read request, first try to find the key in the memtable, then in the most recent on-disk SSTable file, then in the next-order one, etc.

          + From time to time, run a merging and compaction process in the background to combine files and to discard overwritten or deleted values.

        + There is a big problem in above storage engine: if the database crashes, the data in memtable is lost. To avoid this problem, we can keep a separate log on disk to which every write is immediately append, it doesn't need to be sorted, since it's only purpose is to restore memtable after a crash.

      + LSM-Tree (or Log-Structured Merge-Tree) is built on log-structured filesystems. The basic idea of LSM-trees is to keep a cascade of SSTables that are merged in the background.

    + #### B-Trees


      + B-Tree is the most widely used indexing structure. Like SSTables, B-trees keep key-value pairs sorted by key, which provides efficient key-value lookups and range queries.

      + The biggest difference between log-structured indexes and B-trees is how the database is broken down.

        + log-structured indexes break the database down into variable-size _segments_, and always write a segment sequentially.

        + B-trees break the database down into fixed-size _blocks_ or _pages_, and read or write one page at a time. This design corresponds more closely to underlying hardware, as disks are also arranged in fixed-size blocks.

      + Each page can be identified by an address or location, which allows one page to refer to another---similar to a pointer, but on disk instead of memory. These page references can be used to construct a tree of pages.


![](/assets/ddia/looking_up_a_key_using_a_btree_index.png)

      + One page is designated as the root of the B-tree, and every lookup will start from the root page. Each page contains several keys and references to child pages, each child page is responsible for a continuous range of keys, and the keys between the references indicate where the boundaries between those ranges lie.

      + The number of references to child pages in one page of the B-tree is called the _branching factor_, which depends on the amount of space required to store the page references and the range boundaries.

      + A B-tree with _n_ keys always has a depth of O(logn).

      + The basic underlying write operation of a B-tree is to overwrite a page on disk with new data. Moreover, some operations require several different pages to be overwritten, such as a insert operation result a page split.

      + Since a database may crash at any time, write multiple pages one time is extremely dangerous. To avoid this, B-tree implementations typically will include an additional data structure on disk: a _write-ahead log_ (WAL, also known as _redo log_). This is an append-only file to which every B-tree modification must be written before it can be applied to the pages of the tree itself.

      + 

      + 

    + #### Other Indexing Structures


      + Hash indexes, SSTables, LSM-Trees and B-Trees are key-value indexes, which are like a **_primary key_** index in the realational model. A primary key **uniquely** identifies one row in a relational table, or one document in a document database, or one vertex in a graph database.

      + _**Secondary index**_ is another type of index. It can easily be constructed from a key-value index. But the main difference is that in a secondary index, the indexed value are not necessarily unique.

      + The key in an index is the thing that queries search for, but the value can be one of two things: the actual row (document, vertex) in question, or a reference to the row stored elsewhere. In the latter case , the place where rows are stored is known as a _heap file_, and it stores data in no particular order. But heap file make update a value without changing the key more efficient.

      + Sometimes, an extra lookup into the heap has a performance penalty for reads, so it can be desirable to stored the indexed row directly within an index. This is known as a **_clustered index_**.

        + {{< logseq/orgNOTE >}}In MySQL's InnoDB storage engine, the primary key of a table is always a clustered index, and secondary indexes refer to the primary key.
{{< / logseq/orgNOTE >}}

      + A compromise between a clustered index (storing all row data within the index) and a nonclustered index (storing only references to the data within the index) is known as a _covering index_ or _index with included columns_, which stores some of a table's columns within the index alone,

        + {{< logseq/orgNOTE >}}As with any kind of duplication of data, clustered and covering indexes can speed up reads, but they require additional storage and can add overhead on writes.
{{< / logseq/orgNOTE >}}

      + Multi-column indexes

        + If the index only map a single key to a value, then it's not sufficient when we need to query multiple columns of a table simultaneously.

        + _Concatenated index_ is the most common type of multi-column index, it simply combines several fields into one key by appending one column to another. Such as (firstname, lastname) -> firstname-lastname, in this way, prefix query is also benefited.

        + Multi-dimensional indexes are a more general way of querying several columns at one, which is particularly important for geospatial data.

      + Full-text search and fuzzy indexes

        + Both single key indexes and multi-column indexes assume that users have exact data and allow them to query for exact values of a key, or a range of values of a key with a sort order. What they don't allow us to do is search for _similar keys_, such fuzzy querying requires different techniques.

## Chapter 4: Encoding and Evolution


  + Applications inevitably change over time. Thus we should aim to build systems that make it easy to adapt to change.


    + Data format or schema changes often result a corresponding change to application code. However, in a large application, code changes often cannot happen instantaneously:

      + With server-side applications, we may want to perform a _rolling upgrade_ (also known as _staged rollout_), deploying the new version to a few nodes at a time, checking whether the new version is running smoothly, and gradually working our way through all the nodes. This allows new version to be deployed without service downtime, and thus encourages more frequent releases and better evolvability.

      + With client-side applications, we're at the mercy of the user, who may not install the update for some time.

    + Old and new versions of the code, and old and new data formats may potentially coexist in the system at the same time. In order for the system to continue running smoothly, we need to maintain compatibility in both directions:

      + _Backward compatibility_: Newer code can read data that was written by older code.

      + _Forward compatibility_: Older code can read data that was written by newer code.

      + Backward compatibility is normally not hard to achieve: as author of the newer code, we know the format of data written by old code, and so we can explicitly handle it. However, forward compatibility is not very easy, because it requires older code to ignore additions made by a newer version of the code.

      + During rolling updates, or for various other reasons, we must assume that different nodes are running the different version of our application's code. Thus it is important that all data flowing around the system is encoded in a way that provides backward compatibility and forward compatibility.

  + ###  Formats for Encoding data


    + Programs usually work with data in (at least) two different representations:

      + 1. In memory, data is kept in objects, lists, etc. These data structures are optimized for efficient access and manipulation by the CPU.
2. When we want to write data to a file or send it over the network, the data needs to be encoded into some kind of self-contained sequence of bytes.

    + The translation from the in-memory representation to a byte sequence is called _encoding_ (also known as _serialization_ or _marshalling_), and the reverse is called _decoding_ (_parsing_, _deserialization_, _unmarshalling_).

    + Since data encoding and decoding is a common problem, there are many different libraries to handle this. Among these libraries, some are language-specific (such as `java.io.Serializable`), while others are cross-programming-languages (such as those in textual format like JSON, XML, CSV, and those in binary like Thrift, Protocol Buffers, Avro).

    + Protocol Buffers, Thrift, and Avro all use a schema to describe a binary encoding format. Schema evolution is mainly reflected on how these data formats implement backward/forward compatibility.

  + ### Modes of Dataflow
    + Compatibility is a relationship between one process that encodes the data, and another process that decodes it. There're many ways data can flow from one process to another. The central point is who encodes the data, and who decodes it?

    + Some of the common ways of data flows between processes are: via databases, via service calls, and via asynchronous message passing.

    + Dataflow through Databases: In the database, the process that writes to the database encodes the data, and the process that reads from the database decodes it.

    + RPC model tries make a request to a remote network service look the same as calling a funciton or method in your programming language, within the same process (this abstraction is called _location transparency_)

    + Asynchronous message-passing systems are somewhere between RPC and databases. They are similar to RPC in that a client's request (usually called a message) is delivered to another process with low latency. They are similar to databases in that the message is not sent via a direct network connection, but goes via an intermediary called a _message broker_ (also called a _message queue_ or _message-oriented middleware_), which stores the message temporarily.

      + message-passing communicate is usually one-way: a sender normally doesn't expect to receive a reply to its messages. The send doesn't need wait for the message to be delivered, but simple sends and then forgets about it (fire-and-forget).

  + 

## Chapter 5: Replication


  + There are two common ways data is distributed across multiple nodes:


    + Replication: keeping a copy of the same data on several different nodes, potentially in different locations.

    + Partitioning: splitting a big database into smaller subsets called _partitions_ so that different partitions can be assigned to different nodes (also known as _sharding_).

  + There are several reasons why we might want to replicate data:


    + To keep data geographically close to our users (and thus reduce access latency)

    + To allow the system to continue working even if some of its parts have failed (and thus increase availability)

    + To scale out the number of machines that can serve read queries (and thus increase read throughput)

  + All of the difficulty in replication lies in handling changes to replicated data. There are mainly three popular algorithms for replicating changes between nodes: single-leader, multi-leader, and leaderless replication.

  + ### Leaders and Followers


    + Each node that stores a copy of the database is called a _replica_. A question arises when multiple replica exists: how do we ensure that all the data ends up on all the replicas?

    + To avoid data unconsistency, each write to the database needs to be processed by every replica. The common solution for this is called _leader-based replication_ (also known as _active/passive_ or _master-slave replication_).

![](/assets/ddia/leader_based_replication.png)

      + The leader-based replication works as follows:

        + 1. One of the replicas is designated the _leader_ (also known as _master_ or _primary_). When clients want to write to the database, they must send their requests to the leader, which first writes the new data to its local storage.
2. The other replicas are known as _followers_ (also know as _read replicas_, _slaves_, _secondaries_, or _hot standbys_). Whenever the leader writes new data to its local storage, it also sends the data change to all of its followers as part of a _replication log_ or _change stream_. Each follower take the log from the leader and updates its local copy of the database accordingly.
3. When a client wants to read from the database, it can query either the leader of any of the followers. However, writes are only accepted on the leader.

    + ### Synchronous Versus Asynchronous Replication

      + An important detail of a replica system is whether the replication happens _synchronously_ or _asynchronously_.

      + A follower might be synchronous (the leader wait until the follower has confirmed that it received the write before reporting success to the user) or asynchronous (the leader send the message, but doesn't wait for a response from the follower).

      + The advantage of synchronous replication is that the follower is guaranteed to have an up-to-date copy of the that that is consistent with the leader. The disadvantage is that if the synchronous follower doesn't respond, the write cannot be processed, the leader must block all writes and wait until the synchronous replica is available again.

      + In practice, if we enable synchronous replication on a database, it usually means that one of the followers is synchronous, and the others are asynchronous.

      + If the synchronous follower becomes unavailable or slow, one of the asynchronous followers is made synchronous. This guarantees that we have an up-to-date copy of the data on at least two nodes: the leader and one synchronous follower. This configuration is sometimes also called _semi-synchronous_.

      + Often, leader-based replication is configured to be completely asynchronous. In this case, if the leader fails and is not recoverable, any writes that have not yet been replicated to followers  are lost. This means that a write is not guaranteed to be durable, even if it has been confirmed to be the client.

    + ### Setting Up New Followers

      + When we need to set up new followers, one thing needs to be considered is how do we ensure that the new follower has an accurate copy of the leader's data?

      + Simply copying data files from one node to another is typically not sufficient: clients are constantly writing to the database, and the data is always in flux, so a standard file copy would see different parts of the database at different points in time. Locking the database (making it not writable) is also unacceptable, because it will decrease availability. Fortunately, setting up a follower can usually be done without downtime:

        + 1. Take a consistent snapshot of the leader's database at some point in time.
2. Copy the snapshot to the new follower node.
3. The follower connects to the leader and requests all the data changes that have happened since the snapshot was taken. This requires that the snapshot is associated with an exact position in the leader's replication log. For PostgreSQL, this position is called _log sequence number_; for MySQL, it is called _binlog coordinates_.
4. When the followers has processed the backlog of data changes since the snapshot, we say it has _caught up_.

    + ### Handling Node Outages

      + How do we achieve high availability with leader-based replication?

      + #### Follower failure: Catch-up recovery

        + On its local disk, each follower keeps a log of the data changes it has received from the leader. If the follower crashes and is restarted, the follower can recover quite easily: from its log, it knows the last transaction that was processed before the fault occurred. Thus, the follower can catch up to the leader.

      + #### Leader failure: Failover

        + Handling a failure of the leader is trickier: one of the followers needs to be promoted to be the new leader, clients need to be reconfigured to send their writes to the new leader, and the other followers need to start consuming data changes from the new leader. This process is called _failover_.

        + When choosing a new leader, the best candidate for leadership is usually the replica with the most up-to-date data changes from the old leader (to minimize any data loss).

        + In certain fault scenarios, it could happen that two node both believe that they are the leader. This situation is called _split brain_, and it is dangerous: if both leaders accept writes, and there is no process for resolving conflicts, data is likely to be lost or corrupted.

    + ### Implementation of Replication Logs

      + How does leader-based replication work under the hood?

      + #### Statement-based replication

        + In the simplest case, the leader logs every write request (_statement_) that it executes and sends that statement log to its followers.

        + Although statement-based replication is simple, there are various ways in which this approach to replication can break down:

          + Any statement that calls a nondeterministic function, such as NOW() or RAND(), is likely to generate a different value on each replica.

          + If statements use an autoincrementing column, or if they depend on the existing data in the database, they must be executed in exactly the same order on each replica, or else they may have a different effect.

          + Statements that have side effects (e.g., triggers, stored procedures, user-defined functions) may result in different side effects occurring on each replica.

      + #### Write-ahead (WAL) shipping

        + Both log-structured storage engine (such as SSTables and LSM-Trees) and B-tree use logs, the log is an append-only sequence of bytes containing all writes to the database. We can use the exact same log to build a replica on another node.

        + The main disadvantage of log mentioned above is that the log describes the data on a very low level: a WAL contains details of which bytes were changed in which disk blocks. This makes replication closely coupled to the storage engine.

      + #### Logical (row-based) log replication

        + An alternative of WAL log is to use different log formats for replication and for the storage engine, which allows the replication log to be decoupled from the storage engine internals. This kind of replication log is called a _logical log_, to distinguish it from the storage engine's (_physical_) data representation.

        + A logical log a relational database is usually a sequence of records describing writes to database tables at the granularity of a row.

        + A logical log format is also easier for external applications to parse. This aspect is useful if we want to send the contents of a database to an external system, this technique is called _change data capture_.

      + #### Trigger-based replication

        + A trigger lets us register custom application code that is automatically executed when a data change (write transaction) occurs in a database system. The trigger has the opportunity to log this change into a separate table.

        + Trigger-based replication typically has greater overheads than other replication methods, and is more prone to bugs and limitations than the database's built-in replication.

  + ### Problems with Replication Lag


    + Being able to tolerate node failures is just one reason for wanting replication, other reasons are scalability (processing more requests than a single machine can handle) and latency (placing replicas geographically closer to users).

    + In normal operation, the delay between a write happening on the leader and being reflected on a follower---the _replication lag_---may be only a fraction of a second, and not noticeable in practice. However, if the system is operating near capacity or if there is a problem in the network, the lag can easily increase to several seconds or even minutes.

    + ### Reading Your Own Writes


      + Many applications let the user submit some data and then view that they have submitted.

      + With asynchronous replication, there is a problem: if the user views the data shortly after making a write, the new data may not yet have reached the replica. To the user, it looks as though the data they submitted was lost. A user makes a write, followed by a read from a stale replica. To prevent this anomaly, we need read-after-write consistency.

![](/assets/ddia/a_user_makes_a_write_followed_by_a_read_from_a_stale_replica.png)

      + To prevent this anomaly, we need _read-after-write consistency_ (also known as _read-your-writes consistency_). This guarantee that if the user reloads the page, they will always see any updates they submitted themselves.

    + ### Monotonic Reads


      + When reading from asynchronous followers, it is possible for a user to see things _moving backward in time_. This can happen if a user makes several reads from different replicas. A user first reads from a fresh replica, then from a stale replica. Time appears to go backward. To prevent this anomaly, we need monotonic reads.

![](/assets/ddia/a_user_first_read_from_a_fresh_replica_then_from_a_stale_replica.png)

      + Monotonic reads (单调读) is a guarantee moving backward in time does not happen, it means if one user makes several reads in sequence, they won't see time go backward---i.e., they won't read older data after having previously read newer data.

    + ### Consistent Prefix Reads

      + If some partitions are replicated slower than others, an observer may see the answer before they see the question. This anomaly is about causality. Preventing this kind of anomaly requires _consistent prefix reads_.

![](/assets/ddia/some_partitions_are_replicated_slower_than_others.png)

      + Consistent prefix reads says that if a sequence of writes happens in a certain order, then anyone reading those writes will see them appear in the same order.

      + 

  + ### Multi-Leader Replication


    + Leader-based replication has one major downside: there is only one leader, and all writes must go through it. If we can't connect to the leader for any reason, we can't write to the database.

    + An extension of the leader-based replication model is to allow more than one node to accept writes. Each node that processes a write must forward that data change to all the other nodes. This model is called _multi-leader_ (also known as _master-master_ or _active/active replication_). In this setup, each leader simultaneously acts as a follower to the other leaders.

    + There are many use cases for multi-leader replication mode, such as:

      + multi-datacenter operation: each datacenter can have a leader. Within each datacenter, regular leader-follower replication is used; between datacenters, each datacenter's leader replicates its change to the leaders in other datacenters.

        + Although multi-leader replication has advantages, it also has a big downside: the same data may be concurrently modified in two different datacenters, and those write conflicts must be resolved.

      + Clients with offline operation: a typical case is calendar. A person could have a calendar account, but the calendar application can be installed on several devices. Different devices need a synchronization, but the synchronization may take place after a long time because network is unavailable.

        + From a architectural point of view, each device is a "datacenter", and the network is unreliable.

      + Collaborative editing: Real-time collaborative editing applications allow several people to edit a document simultaneously. Like offline operation, each people play a role in datacenter.

    + ### Handling Write Conflicts


      + The biggest problem with multi-leader replication is that write conflicts can occur, which means that conflict resolution is required.

![](/assets/ddia/a_write_conflict_caused_by_two_leaders_concurrently_updating_the_same_record.png)

      + #### Synchronous versus asynchronous conflict detection

        + In a single-leader database, the second writer will either block and wait for the first write to complete, or abort the second write transaction, forcing the user to retry the write. However, in multi-leader replication, both two writes are successful, and the conflict is only detected asynchronously at some later point in time. At that time, it may be too late to ask the user to resolve the conflict.

        + In principle, we could make the conflict detection synchronous---wait for the write to be replicated to all replicas before telling the user that the write was successful. However, the main advantage (allowing each replica to accept writes independently) of multi-leader replication is lost.

      + #### Conflict avoidance

        + The simplest strategy for dealing with conflicts is to avoid them: if the application can ensure that all writes for a particular record go through the same leader, then conflicts cannot occur.

        + However, sometimes we might want to change the designated leader for a record, perhaps because one datacenter has failed, or any other reasons. In this situation, conflict avoidance won't work, we have to deal with the possibility of concurrent writes on different leaders.

      + #### Converging toward a consistent state

        + A single-leader database applies writes in a sequential order: if there are several updates to the same field, the last write determines the final value of the field. But in multi-leader configuration, there is no defined ordering of writes.

        + If each replica simply applied writes in the order that it saw the writes, the database would end up in an inconsistent state. This is not acceptable---every replication system must ensure that the data is eventually the same in all replicas. Thus, the database must resolve the conflict in a **_convergent_** way, which means that all replicas must arrive at the same final value when all changes have been replicated.

        + There are various ways of achieving convergent conflict resolution:

          + Give each write a unique ID, pick the write with the highest ID as the _winner_, and throw away the other writes. For example, if the unique ID is timestamp, the technique is known as _last write wins (LWW)_.

          + Give each replica a unique ID, and let writes that originated at a higher-numbered replica always take precedence over writes that originated at a lower-numbered replica.

          + Somehow merge the values together.

          + Record the conflict in an explicit data structure that preserves all information, and write application code that resolves the conflict at some later time.

      + #### Custom conflict resolution logic

        + As the most appropriate way of resolving a conflict may depend on the application, most multi-leader replication tools let us write conflict resolution logic using application code. That code may be executed on write or on read.

        + {{< logseq/orgNOTE >}}There has been some interesting research into automatically resolving conflicts caused by concurrent data modifications, such as Conflict-free Replicated Datatypes (CRDTs), mergeable persistent data structures, operational transformation.
{{< / logseq/orgNOTE >}}

    + ### Multi-Leader Replication Topologies


      + A replication topology describes the communication paths along which writes are propagated from one node to another.

![](/assets/ddia/three_example_topologies_in_which_multi_leader_replication_can_be_setup.png)

        + In circular topology, each node receives writes from one node and forwards those writes (plus any writes of its own) to other node.

        + In star topology, one designated root node forwards writes to all of the other nodes.

        + In all-to-all topology, every leader sends its writes to every other leader.

      + To prevent infinite replication loops, each node is given a unique identifier, and in the replication log, each write is tagged with the identifiers of all the node it has passed through. When a node receives a data change that is tagged with its own identifier, that data change will be ignored.

      + A problem with circular and start topologies is that if just one node fails, it can interrupt the flow of replication messages between other nodes, causing them to be unable to communicate until the node is fixed.

      + On the other hand, all-to-all topologies can have issues too. In particular, some network links may be faster than others, with the result that some replication message may "overtake" others.

![](/assets/ddia/writes_may_arrive_in_the_wrong_order_at_some_replicas.png)

        + To address this issue, imply attaching a timestamp to every write is not sufficient, because clocks may not be synchronous in all nodes. To order these events correctly, a technique called _version vector_ can be used.

  + ### Leaderless Replication


    + Both single-leader and multi-leader replications are based on the idea that a client sends a write request to one node (the leader), and the database system takes care of copying that write to the other replicas.

    + Some of the earliest replicated systems were leaderless, but the idea was mostly forgotten during the era of relational database. Util Amazon used it for its in-house _Dynamo_ system, the architecture became popular again. Since Riak, Casssndra, and Voldmort are open source datastores with leaderless replication models inspired by Dynamo, so this kind of database is known as _Dynamo-style_.

    + ### Writing to the Database When a Node Is Down


      + In a leaderless configuration, failover doesn't exist. Generally, for a write request, the client will send it to all replicas in parallel, and if the client received enough (for example, two in three) _ok_ responses, it consider the write to be successful.


![](/assets/ddia/a_quorum_write_quorum_read_and_read_repair_after_a_node_outage.png)

      + Any writes that happened while the node was down are missing from that node. Thus, if a client read from that node, it may get _stale_ (outdated) values as responses.

      + To solve this problem, when a client reads from the database, it doesn't just send its request to one replica: read requests are also sent to several nodes in parallel. The client get may get different responses from different nodes. Versioned numbers are used to determine which value is newer.

      + #### Read repair and anti-entropy


        + The replication system should ensure that eventually all the data is copied to every replica. After an unavailable node comes back online, how does it catch up on the writes that it missed?

        + Two mechanisms are often used in Dynamo-sty;e datastores:

          + Read repair. When a client makes a read from several nodes in parallel, it can detect any stale responses. Once found a node has a stale value, the client will writes the newer value back to that replica. This approach works well for values that are frequently read.

          + Anti-entropy process. Some datastores have a background process that constantly looks for differences in the data between replicas and copies any missing data from one replica to another. Unlike the replication log in leader-based replication, this _anti-entropy process_ does not copy writes in any particular order, and there may be a significant delay before data is copied.

        + Without an anti-entropy process, values that are rarely read may be missing from some replicas and thus have reduced durability, because read repair only performed when a value is read by the application.

      + #### Quorum for reading and writing

        + If there are _n_ replicas, every write must be confirmed by _w_ nodes to be considered successful, and we must query at least _r_ nodes for each read. As long as _w + r > n_, we expect to get an up-to-date value when reading, because at least one of the _r_ nodes we're reading from must be up to date. Reads and writes that obey there _r_ and _w_ values are called _quorum_ reads and writes. We can think of _r_ and _w_ as the minimum number of votes required for the read or write to be valid.

          + {{< logseq/orgNOTE >}}There may be more than _n_ nodes in the cluster, but any given value is stored only on _n_ nodes. This allows the database to be partitioned, supporting datasets that are larger than we can fit on one node.
{{< / logseq/orgNOTE >}}

    + ### Sloppy Quorums and Hinted Handoff

      + Quorums are not as fault-tolerant as they could be. A network interruption can easily cut off a client from a large number of database nodes.

      + Sloppy quorum: writes and reads still require _w_ and _r_ successful responses, but those may include nodes that are not among the designated _n_ "home" nodes for a value.

      + Once the network interruption is fixed, any writes that one node temporarily accepted on behalf of another node are sent to the appropriate "home" nodes. This is called _hinted handoff_.

      + Sloppy quorums are particularly useful for increasing write availability: as long as any _w_ nodes are available, the database can accepts writes. However, this means even when _w + r > n_, you cannot be sure to read the latest value for a key, because the latest value may have been temporarily written to some nodes outside of _n_.

    + #### Detecting Concurrent Writes

      + Dynamo-style databases allow several clients to concurrently write to the same key, which means that conflicts will occur even if strict quorums are used. The problem is that events may arrive in a different order at different nodes, due to network delays and partial failures.

      + #### Last writes wins (discarding concurrent writes)

        + One approach for achieving eventual convergence is to declare that each replica need only store the most "recent" value and allow "older" values to be overwritten and discarded.

        + LWW is a good choice in which lost writes are perhaps acceptable, such as caching. However, if losing data is not acceptable, LWW is a poor choice for conflict resolution.

      + #### The "happens-before" relationship and concurrency

        + An operation A _happens before_ another operation B if B knows about A, or depends on A, or builds upon A in some way.

        + Whether one operation happens before another operation is the key to defining what concurrency means. In fact, we can simply say that two operations are concurrent if neither happens before the other.

  + 

## Chapter 6: Partitioning


  + Replication is having multiple copies of the same data on different nodes. For very large databases, or very high query throughput, that is not sufficient: we need to break the data up into _partitions_, also known as _sharding_.

  + {{< logseq/orgNOTE >}}_partition_ has many different names, but they are all the same thing. Such as: _shard_ in [MongoDB]({{< ref "/pages/MongoDB" >}}), Elasticsearch, and SolrCloud; _region_ in HBase, _tablet_ in Bigtable, _vnode_ in Cassandta and Riak; _vBucket_ in CouchBase.
{{< / logseq/orgNOTE >}}

  + Normally partitions are defined in such a way that each piece of data (each record, row, or document) belongs to exactly one partition.

  + The main reason for wanting to partition data is scalability: different partitions can be placed on different nodes in a shared-nothing cluster. Thus, a large dataset can be distributed across many nodes, so as the query load.

  + ### Partitioning and Replication


    + Partitioning is usually combined with replication so that copies of each partition are stored on multiple nodes. This means that, even though each record belongs to exactly one partition, it may still be stored on several different nodes for fault tolerance.

    + A node may store more than one partition. Each partition's leader is assigned to one node, and its followers are assigned to other nodes. Each node may be the leader for some partitions and a followers for other partitions.

![](/assets/ddia/combining_replication_and_partitioning.png)

  + ### Partitioning of Key-Value Data


    + The goal of partitioning is to spread the data and the query load evenly across nodes. If the partitioning is unfair, so that some partitions have more data or queries than others, we call it _skewed_. The presence of skew makes partitioning much less effective. In the worst case, all the load end up on one partition. leaves all other partitions idle. A partition with disproportionately high load is called a _hot spot_.

    + The simplest approach to avoid hot spots would be to assign records to node randomly, but it has a big disadvantage: we have to query all nodes in parallel because we have no way of knowing which node the target data is on.

    + ### Partitioning by Key Range

      + Suppose we have a simple key-value data model, one way to partitioning is to make the records sorted by their key and assign a continuous range of keys (from some minimum to some maximum) to each partition.

      + If we know the boundaries between the ranges, we can easily determine which partition contains a given key.

      + The range of keys are necessarily evenly spaced, because our data may not be evenly distributed. In order to distribute the data evenly, the partition boundaries need to adapt to the data.

      + Unfortunately, the downside of key range partitioning is that certain access patterns can lead to hot spots.

    + ### Partitioning by Hash of Key

      + Because of the risk of skew and hot spots, many distributed datastores use a hash function to determine the partition for a given key. Because a good hash function takes skewed data and makes it uniformly distributed.

      + For partitioning purpose, the hash function need not be cryptographically strong, but be cautious the same key may have a different hash value in different processes, making it unsuitable for partitioning.

      + Once we have a suitable hash function for keys, we can assign each partition a range of hashes (rather a range of keys), and every key whose hash falls within a partition's range will be stored in that partition.

      + Hash function can make the partition boundaries evenly spaced or be chosen pseudorandomly (in which case the technique is sometimes known as _consistent hashing_).

        + Consistent hashing uses randomly chosen partition boundaries to avoid the need for central control or distributed consensus. The word consistent here describes a particular approach to rebalancing.

      + By using the hash of the key for partitioning, we get evenly distributed data. However, we lost the efficiency of range queries when partitioned by key.

      + Partition by key is useful for range scans, but partition by hash of key will distribute load more evenly.

    + ### Skewed Workloads and Relieving Hot Spots

    + Hashing a key to determine its partition can help reduce hot spots. However, it can't avoid them entirely: in the extreme case where all reads and writes are for the same key, we still end up with all requests being route to the same partition.

    + Today, most data systems are not able to automatically compensate for such a highly skewed workload, so it's the responsibility of the application to reduce the skew.

  + ### Partitioning and Secondary Indexes


    + Key-value data model is just a simple case, if the secondary indexes are involved, the situation will become more complicated. Secondary indexes are widely used in search engines such as Solr and Elasticsearch. The problem with secondary indexes is that they don't map neatly to partitions. There are two main approaches to partitioning a database with secondary indexes: document-based partitioning and term-based partitioning.

    + ### Partitioning Secondary Indexes by Document

      + In this indexing approach, each partition is completely separate: each partition maintains its own secondary indexes, covering only the documents in that partition.

![](/assets/ddia/partitioning_secondary_indexes_by_document.png)

      + A document-partitioned index is also known as a _local index_ (as opposed to a _global index_), because it doesn't care what data is stored in other partitions.

      + If we want search by secondary indexes, we need to send the query to all partitions, and combine all the results we get back. This approach to query a partitioned database is sometimes known as _scatter/gather_, and it can make read queries on secondary quite expensive.

    + ### Partitioning Secondary Indexes by Term

      + Rather than each partition having it's own secondary index (a local index), we can construct a global index that covers data in all partitions.

![](/assets/ddia/partitioning_secondary_indexes_by_term.png)

      + If we store all secondary index in one node, the node would likely become a bottleneck. Thus, a global index must also be partitioned, but it can be partitioned differently from the primary key index.

      + This kind of secondary index partition is called _term-partitioned_, because the term we're looking for determines the partition of the index. The name _term_ comes from full-text indexes (a particular kind of secondary index), where the terms are all the words that occur in a document.

      + The advantage of global (term-partitioned) index over a document-partitioned index is that it can make reads more efficient, but the disadvantage is that it makes writes slower and more complicated.

  + ### Rebalancing Partitions


    + The processing of moving load from one node in the cluster to another is called _rebalancing_.

    + No matter which partitioning scheme is used, rebalancing is usually expected to meet some minimum requirements:

      + After rebalancing, the load should be shared fairly between the nodes in the cluster.

      + While rebalancing is happening, the database should continue accepting reads and writes.

      + No more data than necessary should be moved between nodes.

    + Hash mod N is not very good for rebalancing. Because if the number of nodes N changes, most of the keys will need to be moved from one node to another.

    + A fairly simple solution to overcome the drawback of mod N approach is use fixed number of partitions: create many more partitions than there are nodes, and assign several partitions to a node. If a node is added to the cluster, the new node can steal a few partitions from every existing node until partitions are fairly distributed once again. If a node is removed from the cluster, the same happens in reverse.

    + Some key range-partitioned database can create partitions dynamically: when a partition grows to exceed a threshold, it is split into two smaller partitions. Conversely, if lots of data is deletes, a partition can be merged into an adjacent partition. In a nutshell, dynamic partitioning is about the number of partitions adapting to the total data volume.

    + With dynamic partitioning, the number of partitions is proportional to the size of the dataset. On the other hand, with a fixed number of partitions, the size of each partition is proportional to the size of the dataset.

  + ### Request Routing


    + As partitions are rebalanced, the assignment of partitions to nodes changes. When a client wants to make a request, how does it know which node to connect to?  This is an instance of a more general problem: _service discovery_.

    + On a high level, there are a few different approaches to this problem:

      + Allow clients contact any node. If that node coincidentally owns the partition to which the request applies, handle the request directly; otherwise, forward the request to the appropriate node, receive the reply and pass the reply along to the client.

      + Sends all request from clients to a routing tier first, which determines the node that should handle each request and forwards it accordingly.

        + Many distributed data system rely on a separate coordination service such as ZooKeeper to keep track of cluster metadata. Whenever a partition changes ownership, or a node is added or removed, ZooKeeper notifies the routing tier so that it can keep its routing information up to date.

      + Require the clients be aware of the partitioning and the assignment of the partitions to nodes.

    + 

## Chapter 7: Transactions


  + A transaction is a way for an application to group several reads and writes together into a logical unit. Conceptually, all the reads and writes in a transaction are executed as one operation: either the entire transaction succeeds (_commit_) or it fails (_abort_, _rollback_).

  + Transactions are not a law of nature; they were created with a purpose, namely to _simplify the programming model_ for applications accessing a database.

  + ### The Slippery Concept of a Transaction


    + Like every other technical design choice, transactions have advantages and limitations.

    + ### The Meaning of ACID

      + The safety guarantees provides by transactions are often described by the well-known acronym ACID, which stands for Atomicity, Consistency, Isolation, and Durability.

        + {{< logseq/orgWARNING >}}In practice, one database's implementation of ACID doesn't equal another's implementation.
{{< / logseq/orgWARNING >}}

      + Systems that don't meet the ACID criteria are sometimes called BASE, which stands for Basically Available, Soft state, and Eventual consistency.

      + #### Atomicity

        + In general, atomic refers to something that cannot be broken down into smaller parts. ACID atomicity describes what happens if a client wants to make several writes, but a fault occurs after some of the writes have been processed. If the writes are grouped together into a atomic transaction, and the transaction cannot be completed (committed) due to a fault, then the transaction is aborted and the database must discard or undo any writes it has made so far in that transaction.

        + The ability to abort a transaction on error and have all writes from that transaction discarded is the defining feature of ACID atomicity. Perhaps _abortability_ is a better term than _atomicity_.

      + #### Consistency

        + The idea of ACID consistency is that you have certain statements about your data (_invariants_) that must always be true. But the statements are depend on the application's notion of invariants, and it's the application's responsibility to define its transactions correctly so that they preserve consistency.

        + In general, the application defines what data is valid or invalid, the database only stores it.

        + Atomicity, isolation, and durability are properties of the database, whereas consistency (in the ACID sense) is a property of the application.

      + #### Isolation

        + Isolation in the sense of ACID means concurrently executing transactions are isolated from each other: they cannot step on each other's toes.

        + This is achieved by ensuring that any interim state changes made during one transaction are invisible to other transactions.

      + #### Durability

        + The purpose of a database is to provide a safe place where data can be stored without fearing of losing it. Durability is the promise that once a transaction has committed successfully, any data it has written will not be forgotten, even if there is a hardware fault or the database crashes.

        + In order to provide a durability guarantee, a database must wait until these writes or replications are complete before reporting a transaction as successfully committed.

  + ### Weak Isolation Levels


    + Databases try to hide concurrency issues from application developers by providing transaction isolation.

    + Serializable isolation means that the database guarantees that transactions have the same effect as if they ran serially. However, serializable isolation has a performance cost, and many databases don't want to pay that price. Instead, most database systems use weaker level of isolation, which protect against some concurrency issues, but not all.

    + ### Read Committed


      + The most basic level of transaction isolation is _read committed_. It makes two guarantees:

        + 1. No dirty reads. When reading from the database, you only see data that has been committed.
2. No dirty writes. When writing to the database, you will only overwrite data that has been committed.

      + Most commonly, databases prevent dirty writes by using row-level locks: when a transaction wants to modify a particular object (row or document), it must first acquire a lock on that object. It must be hold that lock until the transaction is committed or aborted.

      + Lock can also be used to prevent dirty reads, but its bad for performance. Thus, most database prevent dirty reads by a different approach: for every object that is written, then database remembers both the old committed value and the new value set by transaction that is ongoing, any other transactions that read the object are simply given the old value. Only when the new value is committed do transactions switch over to reading the new value.

    + ### Snapshot Isolation and Repeatable Read


      + The idea of _snapshot isolation_ is that each transaction reads from a consistent snapshot of the database. In other words, the transaction sees all the data that was committed in the database at the start of the transaction. Even if the data is subsequently changed by another transaction, each transaction sees only the old data from that particular point in time.

      + #### Implementing snapshot isolation


        + Implementations of snapshot isolation typically use write locks to prevent dirty writes, however, reads do not require any locks. From a performance view, a key principle of snapshot isolation is that _readers never block writers, and writers never block readers_.

        + To implement snapshot isolation, database must potentially keep several different committed versions of an object, because various in-progress transactions may need to see the state of the database at different points in time. Because it remains several versions of an object side by side, this technique is known as _multi-version concurrency control (MVCC)_.

        + When a transaction is started, it is given a unique, always-increasing transaction ID (txid). Whenever a transaction writes anything to the database, the data it writes is tagged with the transaction ID of the writer.

      + #### Visibility rules for observing a consistent snapshot


        + When a transaction reads from the database, transaction IDs are used to decide which objects it can see and which are invisible. By carefully defining visibility rules, the database can present a consistent snapshot of the database to the application. This works as follows:

          + 1. At the start of the transaction, the database makes a list of all the other transactions that are in progress (not yet committed or aborted) at that time. Any writes that those transactions have made are ignored, even if the transactions subsequently commit.
2. Any writes made by aborted transactions are ignored.
3. Any writes made by transactions with a later transaction ID are ignored, regardless of whether those transactions have committed.
4. All other writes are visible to the application's queries.

        + Put another, way, an object is visible if both of the following conditions are true:

          + At the time when the reader's transaction started, the transaction that created the object had already committed.

          + The object is not marked for deletion, or if it is, the transaction that requested deletion had not yet committed at the time when the reader's transaction started.

      + #### Repeatable read and naming confution

        + Snapshot isolation is a useful isolation level, especially for read-only transactions. However, many databases that implement it call it by different names. In Oracle, it is called _serializable_, and in PostgreSQL and MySQL, it is called _repeatable read_.

    + ### Preventing Lost Updates


      + The lost updates problem can occur if an application reads some value from the database, modifies it, and writes back the modified value (a _read-modify-write cycle_). If two transactions do this concurrently, one of the modifications will be lost, because the second write does not include the first modification.

      + A variety of solutions have been developed to deal with lost updates problem:

        + Atomic write operations.

        + Explicit locking. (`SELECT ... FOR UPDATE`).

        + Automatically detecting lost updates and forcing the read-modify-write cycles to happen sequentially.

        + Compare-and-set.

        + Resolve conflicts when replications exist.

    + ### Write Skew and phantoms


      + Write skew can occur if two transactions read the same objects, and then update some of those objects (different transactions may update different objects).

      + Write skew is neither a dirty write or a lost update, because the two transactions are updating two different objects. But we can think of write skew as a generalization of the lost update problem (when different transactions update the same object).

      + Most of the writes that are skewed happen in a similar pattern:

        + 1. A SELECT query checks whether some requirement is satisfied by searching for rows that match some search condition.
2. Depending on the result of the first query, the application code decides how to continue (perhaps to go ahead with the operation, or report an error to the user and abort). 
3. If the application decides to go ahead, it makes a write (INSERT, UPDATE, DELETE) to the database and commits the transaction.

          + The effect of this write changes the precondition of the decision of step 2. This effect, where a write in one transaction changes the result of a search query in another, is called a _phantom_.

  + ### Serializability


    + Serializable isolation is usually regarded as the strongest isolation level. It guarantees that even though transactions may execute in parallel, the end result is the same as if they had executed one at a time, _serially_, without any concurrency.

    + Most databases that provide serializability today use one of three techniques:


      + 1. Literally executing transaction in a serial order.
2. Two-phase locking, which for several decades was the only viable option.
3. Optimistic concurrency control techniques such as serializable snapshot isolation.

    + ### Actual Serial Executation


      + The simplest way of avoiding concurrency problems is to remove the concurrency entirely: to execute one transaction at time, in serial order, on a single thread.

      + A system designed for single-threaded execution can sometimes perform better than a system that supports concurrency, because it can avoid the coordination overhead or locking. However, its throughput is limited to that of a single CPU core.

    + ### Two-Phase Locking (2PL)


      + In two-phase locking, several transactions are allowed to concurrently read the same object as long as nobody is writing to it. But as soon as anyone wants to write an object, exclusive access is required:

        + If transaction A has read an object and transaction B wants to write to that object, B must wait until A commits or aborts before it can continue.

        + If transaction A has written an object and transaction B wants to read that object, B must wait until A commits or aborts before it can continue.

      + In a nutshell, in 2PL, writers don't just block other writers, but also block readers and vice versa.

      + The blocking of readers and writers is implemented by having a lock on each object in the database. The lock can either be in shared mode or in exclusive mode. The lock is used as follows:

        + If a transaction wants to read an object, it must first acquire the lock in shared mode. Several transactions are allowed to hold the lock in shared mode simultaneously, but if another transaction already has an exclusive lock on the object, these transactions must wait.

        + If a transaction wants to write an object, it must first acquire the lock in exclusive mode. No other transaction may hold the lock at the same time, so if there is any existing lock on the object, the transaction must wait.

        + If a transaction first reads the then writes an object, it may upgrade its shared lock to an exclusive lock. The upgrade works the same as getting an exclusive lock directly.

        + After a transaction has acquired the lock, it must continue to hold the lock until the end of the transaction (commit or abort). This is where the name "two-phase" comes from: the first phase (while the transaction is executing) is when the locks are acquired, and the second phase (at the end of the transaction) is when all the locks are released.

      + The big downside of two-phase locking is performance: transaction throughput and response time of queries are significantly worse under two-phase locking than under weak isolation. This is partly due to the overhead of acquiring and releasing all those locks, but more importantly due to reduced concurrency.

    + ### Serializable Snapshot Isolation (SSI)

      + Serializable snapshot isolation (SSI) provides full serializability, but has only a small performance penalty compared to snapshot isolation.

      + As the name suggests, SSI is based on snapshot isolation---that is, all reads within a transaction are made from a consistent snapshot of the database.

      + #### Decisions based on an outdated premise


        + Under snapshot isolation, the transaction is taking an action based on a _premise_ (a fact was true at the beginning of the transaction). Later, when the transaction wants to commit, the original data may have changed---the premise may no longer be true.

        + In order to provide serializable isolation, the database must detect situations in which a transaction may have acted on an outdated premise and abort the transaction in that case. To know if a query result might have changed, the database needs to consider two cases:

          + Detecting reads of a stale MVCC object version (uncommitted write occurred before the read).

            + The database needs to track when a transaction ignores another transaction's writes due to MVCC visibility rules. When the transaction wants to commit, the database checks whether any of the ignored writes have now been committed. If so the transaction must be aborted.

          + Detecting writes that affect prior reads (the write occurs after the read).

            + When a transaction writes to the database it must look in the indexes for any other transactions that have recently read the affected data.

      + Compare to 2PL, the big advantage of serializable snapshot isolation is that one transaction doesn't need to block waiting for locks held by another transaction. This makes query latency much more predictable and less variable.

      + Compare to serial execution, SSI is not limited to the throughput of a single CPU core.

  + 

## Chapter 8: The Trouble with Distributed Systems


  + An assumption: anything that can go wrong will go wrong.

  + ### Faults and Partial Failures


    + In a distributed system, there may well be some parts of the system that are broken in some unpredictable way, even though other parts of the system are working fine. This is know as _**partial failure**_.

    + Partial failures are underterministic.

    + Nondeterminism and possibility of partial failure is what makes distributed systems hard to work with.

    + If we want to make distributed systems work, we must accept the possibility of partial failure and build fault-tolerance mechanisms into the software. In other words, we need to build a reliable system from unreliable components,

  + ### Unreliable Networks


![](/assets/ddia/unreliable_networks.png)

    + If you send a request and don't get a response, it's not possible to distinguish whether the request was lost, the remote node is down, or the response was lost. The usual way of handling this issue is a **_timeout_**: after some time you give up waiting and assume that the response is not going to arrive. However, when a timeout occurs, you still don't know whether the remote node got your request or not. There's no "correct" value for timeouts---they need to be determined experimentally.

    + When one part of the network is cut off from the rest due to a network fault, that is sometimes called a _network partition_ or _netsplit_.

    + Rapid feedback about a remote node being down is useful, but you cannot count on it. Even if TCP acknowledges that a packet was delivered, the application may have crashed before handling it.

    + If a timeout is the only sure way of detecting a fault, then how long should the timeout be? There is unfortunately no simple answer. A long timeout means a long wait until a node is declared dead. A short timeout detects faults faster, but carries a higher risk of incorrectly declaring a node dead when in fact it has only suffered a temporary slowdown.

    + Prematurely declaring a node dead is problematic: if the node is actually alive and in the middle of performing some action, and another node takes over, the action may end up being performed twice.

    + When a node is declared dead, its responsibilities need to be transferred to other nodes, which places additional load on other nodes and network. If the system is already struggling with high load, declaring nodes dead prematurely can make the problem worse. Transferring a node's load to other overloaded nodes can cause a cascading failure.

    + Why do datacenter networks and the internet use packet switching? The answer is that they are optimized for _bursty traffic_. TCP dynamically adapts the rate of data transfer to the available network capacity.

    + More generally, you can think of variable delays as a consequence of dynamic resource partitioning.

  + ### Unreliable Clocks


    + In a distributed system, time is a tricky business, because communication is not instantaneous.

    + Modern computers have at least two different kinds of clocks: a _time-of-day clock_ and a _monotonic clock_.

      + A _time-of-day clock_ returns the current date and time according to some calendar (also known as wall-clock time), they are usually synchronized with NTP (Network Time Protocol), which means that at timestamp from one machine (ideally) means the same as a timestamp on another machine.

      + A _monotonic clock_ is suitable for measuring a duration (time interval), such as a timeout or a service's response time. The name comes from the fact that they are guaranteed to always move forward (whereas a time-of-day clock may jump back in time). In particular, it makes no sense to compare monotonic clock values from two different computers, because they don't mean the same thing.

    + Logical clocks and physical clocks

      + _logical clocks_ are based on incrementing counters rather than an oscillating quartz crystal, which is a safer alternative for ordering events than timestamp.

      + _physical clocks_ are those who measure actual elapsed time, such as time-of-day and monotonic clocks.

    + ### Process Pauses

      + A process (or thread) might be paused for a long time.

        + JVM has a garbage collector that occasionally needs to stop all running threads.

        + In virtualized environments, a VM can be suspend and resumed.

        + ...

      + There are so many situations in which a running thread is preempted at any point and resumed at some later time, without the tread even noticing.

      + The negative effects of process pauses can be mitigated without resorting to expensive real-time scheduling guarantees.

        + An emerging idea is to treat GC pauses like brief planned outage of a node, an to let other nodes handle requests from client while one node is collecting its garbage.

        + A variant of this idea is to use the garbage collector only if short-lived objects (which are fast to collect) and to restart processes periodically, before they accumulate enough long-lived objects to require a full GC of long-lived objects.

  + ### Knowledge, Truth, and Lies


    + A node in the network cannot know anything for sure---it can only make guesses based on the messages it receives (or doesn't receive) via the network. A node can only find out what state another node is in by exchanging messages with it.

    + If a node continues acting as the chosen one, even though the majority of nodes have declared it dead, it could cause problems in a system that is not carefully designed. 幂等

    + Proving an algorithm correct does not mean its implementation on a real system will necessarily always behave correctly.

## Chapter 9: Consistency and Consensus


  + The best way of building fault-tolerant systems is to find some general-purpose abstractions with useful guarantees, implement them once, and then let applications rely on those guarantees.

  + One of the most important abstractions for distributed system is **_consensus_**: that is, getting all of the nodes to agree on something.

  + ### Consistency Guarantees


    + Eventual consistency: if you stop writing to the database and wait for some unspecified length of time, then eventually all read requests will return the same value. In other words, the inconsistency is temporary, and eventually resolves itself. A better name for eventual consistency may be _convergence_, as we expect all replicas to eventually converge to the same value.

  + ### Linearizability


    + The basic idea of linearizability (also known as atomic consistency, strong consistency, immediate consistency or external consistency) is to make the system appear as if there were only one copy of the data, and all operations on it are atomic.

    + In a linearizable system, the value read is the most recent, update-to-date value, and doesn't come from a stale cache or replica. In other words, linearizablity is a _recency guarantee_.

  + ### Ordering Guarantees


    + Ordering helps preserve **_causality_**. Causality imposes an ordering on events: cause comes before effect. If a system obeys the ordering imposed by causality, we say that it is _causally consistent_.

    + Although causality is an important theoretical concept, actually keeping track of all causal dependencies can become impracticable. There is a better way: we can use _sequence numbers_ or _timestamps_ to order events.

    + **_Lamport timestamp_** provices a total ordering consistent with causality. In Lamport timestamp, each node has a unique identifier, and each node keeps a counter of the number of operations it has processed. The Lamport timestamp is then simply a pair of _(counter, node ID)_. Two node may sometimes have the same counter value, but by including the node ID in the timestamp, each timestamp is made unique.

      + The key idea about Lamport timestamps, which makes them consistent with causality, is the following: every node and every client keeps track of the _maximum_ counter value it has seen so far, and includes that maximum on every request. When a node receives a request or response with a maximum counter value greater than its own counter value, it immediately increases its own counter to that maximum.

    + A big problem about total ordering is that the total order of operations only emerges after you have collected all of the operations.

    + Total order broadcast is asynchronous: messages are guaranteed to be delivered reliably in a fixed order, but there is no guarantee about when a message will be delivered. Total order broadcast requires messages to be delivered exactly once, in the same order, to all nodes.

  + ### Distributed Transactions and Consensus


    + Consensus is one of the most important and fundamental problems in distributed computing. Its goal is to get several nodes to agree on something.

    + In a distributed system, we must assume that node may crash, so reliable consensus is impossible.

    + ### Atomic Commit and Two-Phase Commit (2PC)

      + Two phase commit is an algorithm for achieving atomic transaction commit across multiple nodes---i.e., to unsure that either all nodes commit or all nodes abort.

![](/assets/ddia/successful_two_phase_commit.png)

![](./assets/ddia/coordinator_crashes_in_two_phase_commit.png)

    + ### Fault-Tolerant Consensus

      + The consensus problem is normally formalized as follows: one or more nodes may propose values, and the consensus algorithm decides on one of those values. In this formalism, a consensus algorithm must satisfy the following properties:

        + Uniform agreement: no two nodes decide differently.

        + Integrity: no node decides twice.

        + Validity: if a node decides value v, then v was proposed by some node.

        + Termination: every node that does not crash eventually decides some value.

    + ### Membership and Coordination Services

      + Projects like ZooKeeper and etcd are often described as "distributed key-value stores" or "coordination and configuration services". ZooKeeper and etcd are designed to hold small amounts of data that can fit entirely in memory. That small amount of data is replicated across all the nodes using a fault-tolerant total order broadcast algorithm.

      + 

## Chapter 10: Batch Processing


  + ### System of Record and Derived Data


    + On a high level, systems that store and process data can be grouped into two broad categories:

      + **Systems of record**. A system of record, also known as _source of truth_, holds the authoritative version of your data. Each fact is represented exactly once (the representation is typically _normalized_).

      + **Derived data systems**. Data in a derived system is the result of taking some existing data from another system and transforming or processing it in some way. If you lose derived data, you can recreate it from the original source. Technically speaking, derived data is _redundant_, in the sense that it duplicates existing information.  However, it is often essential for getting good performance on read queries. Derived data is commonly _denormalized_

  + A batch processing system takes a large amount of input data, run a job to process it, and produces some output data. Jobs often take a while (from a few minutes to several days), so there normally isn't a user waiting for the job to finish. The primary performance measure of a batch job is usually _throughput_.

  + In batch processing world, the inputs and outputs of a job are files (perhaps on a distributed filesystem). When the input is a file (a sequence of bytes), the first processing step is usually to parse it into a sequence of records.

  + A nice property of the batch processing systems is that they provide a strong reliability guarantee: failed tasks are automatically retried, and partial output from failed tasks are automatically discarded.

## Chapter 11: Stream Processing
  + In batch processing, input is bounded. However, in stream processing, the input is unbounded---that is, the inputs of our job are never-ending streams of data. In this case, a job is never complete, because at any time there may still be more work coming in.

  + In general, a "stream" refers to data that is incrementally made available over time.

  + In batch processing, a file is written once and then potentially read by multiple jobs. Analogously, in streaming terminology, an event is generated once by a producer (also known as a publisher or sender), and then potentially processed by multiple consumer (subscribers or recipients). In a filesystem (used in batch processing), a filename identifies a set of related records; in a streaming system, related events are usually grouped together into a topic or stream.

  + ### Transmitting Event Streams


    + ### Message Systems

      + A common approach for notifying consumers about new event is to use a messaging system: a producer sends a message containing the event, which is then pushed to consumers.

      + Within publish/subscribe model, different systems take a wide range of approaches, and there is no one right answer for all purposes. To differentiate the systems, it is particularly helpful to ask the following two questions:

        + What happens if the producers send messages faster than the consumers can process them?

          + Broadly speaking, there are three options:

            + 1. drop messages
2. buffer messages in a queue
3. apply backpressure (also known as flow control; i.e., blocking the producer from sending more messages)

        + What happens if nodes crash or temporarily go offline---are any message lost?

          + Whether message lost is acceptable depends very much on the application

      + #### Direct messaging from producers to consumers

        + A number of messaging systems use direct network communication between producers and consumers without going via intermediary nodes, such as UDP multicast, brokerless messaging libraries, webhooks, etc.

        + Direct messaging systems generally require the application code to be aware of the possibility of message loss. If a consumer is offline, it may miss messages that were sent while it is unreachable.

      + #### Message brokers

        + A widely used alternative of direct messaging systems is to send messages via a _message broker_ (also known as a _message queue_), which is essentially a kind of database that is optimized for handling message streams.

        + By centralizing the data in the broker, these systems can more easily tolerate clients that come and go, and the question of durability is moved to the broker instead.

      + #### Multiple Consuemers

        + When multiple consumers read messages in the same topic, two main patterns if messaging are used:

          + **Load balancing**: each message is delivered to one of the consumers, so the consumers can share the work of processing the messages in the topic.

          + **Fan-out**: each message is delivered to all of the consumers.

      + #### Acknowledgements and redelivery

        + Consumers can crash at any time. In order to ensure that the message is not lost, message brokers use acknowledgements: a client must explicitly tell the broker when it has finished processing a message so that the broker can remove it from the queue.

        + If the broker didn't receive an acknowledgement, it assumes that the message was not processed, and therefore it delivers the message again to another consumer.

          + It could happen that the massage actually was fully processed, but the acknowledgement was lost in the network. Handling this case requires an atomic commit protocol.

        + Message redelivery may have a bad effect on the ordering of messages. Message reordering is not a problem if messages are completely independent (each consumer has their own queue) of each other, but it can be important if there are _causal dependencies_ between messages.

    + ### Partitioned Logs

      + Even message brokers that durably write messages to disk quickly delete them again after they have been delivered to consumers. However, in database and filesystems, everything that is written to a database or file is normally expected to be permanently recorded.

      + The idea behind _log-based message brokers_ is combining the durable storage approach of database with low-latency notification facilities of messaging.

      + In order to scale to higher throughput than a single disk can offer, the log can be partitioned. A topic can then be defined as a group of partitions that all carry messages of the same type. Within each partition, the broker assigns a monotonically increasing sequence number, or offset, to every message. Such a sequence number makes sense because a partition is read-only, so the messages within a partition are totally ordered. There is no guarantee across different partitions.


![](/assets/ddia/producers_send_messages_by_appending_them_to_a_topic_partition_file_and_consumers_read_these_files_sequentially.png)

      + Since partitioned logs typically preserve message ordering only within a single partition, all messages that need to be ordered consistently need to be routed to the same partition.

      + Consuming a partition sequentially makes it easy to tell which messages have been processed: all messages with an offset less than a consumer's current offset have already been processed, and all messages with a greater offset have not yet been seen. Thus, the broker does not need to track acknowledgements for every single message---it only needs to periodically record the consumer offsets.

      + If a consumer node fails, another node in the consumer group is assigned the failed consumer's partitions, and it starts consuming messages at the last recorded offset.

      + If you only ever append to the log, you will eventually run out of disk space. To reclaim disk space, the log is actually divided into segments, and from time to time old segments are deleted or moved to archive storage.

  + ### Databases and Streams


    + ### Change Data Capture

      + _Change data capture (CDC)_ is the process of observing all data changes written to a database and extracting them in a form in which they can be replicated to other systems.

      + CDC is a mechanism for ensuring that all changes made to the system of record are also reflected in the derived data systems so that the derived systems have a accurate copy of the data.

      + Essentially, change data capture makes one database the leader (to one from which the changes are captured), and turns the others into followers.

    + ### Event Sourcing

      + Event sourcing involves storing all changes to the application state as a log of change events.

    + 

    + 
