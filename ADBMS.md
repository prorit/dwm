# 📚 Distributed Databases & NoSQL: Exam Syllabus Guide

### **Module 1: Distributed Databases**

#### **1. Explain Distributed DBMS Architecture.**

**Answer:**
The architecture of a Distributed Database Management System (DDBMS) defines how the system components are organized and how they interact. It is typically divided into three levels:

1.  **Internal Level (Physical Level):**
      * Deals with the physical storage of data at each site.
      * Handles local schema, data placement, and local transaction management.
2.  **Conceptual Level:**
      * Provides a **Global Conceptual Schema** (GCS) which is a logical view of the entire database as if it were centralized.
      * Hides the details of data distribution (fragmentation and replication) from the user.
3.  **External Level (User Level):**
      * Defines user views. Users interact with the database without knowing where data is located (Location Transparency).

**Types of Architectures:**

  * **Client-Server:** Tasks are split between client (UI/Request) and server (Data management).
  * **Peer-to-Peer:** All nodes are equal; each can act as a client or server. No central controller.
  * **Multi-DBMS:** A federation of autonomous databases (potentially heterogeneous) that cooperate.

#### **2. Explain Homogenous and Heterogeneous Distributed Database Systems.**

**Answer:**

  * **Homogeneous Distributed Databases:**

      * **Definition:** All sites in the system use the **same DBMS software** (e.g., all sites run Oracle or all run MySQL).
      * **Characteristics:** They share a common global schema. Data exchange and transaction management are easier because the software is compatible.
      * **Pros:** Easier to design and manage; high performance.
      * **Cons:** Less flexible; difficult to integrate legacy systems.

  * **Heterogeneous Distributed Databases:**

      * **Definition:** Different sites run **different DBMS software** (e.g., Site A runs Oracle, Site B runs SQL Server, Site C runs MongoDB).
      * **Characteristics:** Each site may have its own schema and query language. A translation layer (middleware or gateway) is often required to map the global schema to local schemas.
      * **Pros:** Allows integration of existing legacy databases.
      * **Cons:** Complex query processing (translation required); difficult transaction management (2PC is harder to implement).

#### **3. Explain Client/Server Database Architecture.**

**Answer:**
In this architecture, the processing is divided between two types of modules:

1.  **Client (Front-end):**
      * Responsible for the user interface and accepting user queries.
      * Does not manage data directly.
      * Sends database requests (SQL) to the server.
      * Example: A web application or a desktop GUI.
2.  **Server (Back-end):**
      * Responsible for data storage, access control, query processing, and transaction management.
      * Processes the request sent by the client and returns the result.
      * **Multiple Client-Single Server:** Standard setup.
      * **Multiple Client-Multiple Server:** The server may distribute the query to other servers if data is distributed.

**Advantages:**

  * Reduces network traffic (only query and result travel, not raw data).
  * Centralized data management (easier security and backup).

#### **4. What are data replication and allocation techniques for Distributed database design?**

**Answer:**
**Data Replication:**
The process of storing copies of the same data at multiple sites to increase availability and reliability.

  * **Full Replication:** The entire database is stored at every site. (High availability, slow updates).
  * **Partial Replication:** Only frequently accessed fragments are replicated.
  * **No Replication:** Each fragment exists at only one site.

**Allocation Techniques:**
The process of deciding *where* to store data fragments.

1.  **Centralized:** All data at one site (Simple, but single point of failure).
2.  **Fragmented (Partitioned):** Data is split into disjoint fragments and stored at specific sites where they are used most.
3.  **Replicated:** Copies of fragments are maintained at multiple sites.
      * **Static Allocation:** Data placement is fixed during design.
      * **Dynamic Allocation:** Data moves based on access patterns (complex).

#### **5. Explain Database fragmentation (Horizontal, Vertical, Mixed) and its benefits.**

**Answer:**
Fragmentation divides a global relation into smaller logical units called fragments.

1.  **Horizontal Fragmentation:**
      * Splits the table by **rows** (tuples) based on a condition (Selection operation $\sigma$).
      * *Example:* `Employee` table split into `Emp_NY` (City='NY') and `Emp_LA` (City='LA').
      * **Reconstruction:** Union ($\cup$) of fragments.
2.  **Vertical Fragmentation:**
      * Splits the table by **columns** (attributes) (Projection operation $\Pi$). Each fragment must include the Primary Key to allow reconstruction.
      * *Example:* `Employee` table split into `Emp_Personal` (ID, Name, Address) and `Emp_Work` (ID, Salary, Dept).
      * **Reconstruction:** Join ($\bowtie$) on Primary Key.
3.  **Mixed (Hybrid) Fragmentation:**
      * Applies both horizontal and vertical fragmentation.

**Benefits:**

  * **Efficiency:** Data is stored close to where it is used (Data Localization).
  * **Parallelism:** Queries can run on multiple fragments simultaneously.
  * **Security:** Sensitive columns (like Salary) can be stored at secure sites only.

-----

### **Module 2: Distributed Database Handling**

#### **6. Discuss the phases of distributed query processing with a neat diagram.**

**Answer:**
Distributed query processing transforms a high-level query into an efficient execution strategy.

1.  **Query Decomposition:**
      * Parses the calculus query (SQL) into an algebraic query.
      * Performs normalization, analysis, simplification, and rewriting.
2.  **Data Localization:**
      * Uses the **Fragment Schema** to map the global query onto specific fragments.
      * Determines which fragments are involved (e.g., Union of horizontal fragments).
3.  **Global Query Optimization:**
      * Finds the best execution strategy (ordering of joins, semijoins) to minimize cost (usually communication cost).
      * Output: Global Execution Plan.
4.  **Distributed Query Execution:**
      * Local optimization happens at each site.
      * Sub-queries are executed, and data is transferred to the final site.

#### **7. Describe query evaluation plan.**

**Answer:**
A **Query Evaluation Plan (or Execution Plan)** is a sequence of primitive operations (relational algebra operations like select, project, join) annotated with instructions on how to execute them.

  * It specifies **algorithms** for each operation (e.g., Merge Join vs. Hash Join).
  * It specifies **indices** to use (e.g., Use B+ Tree on ID).
  * It determines the **order** of operations.
  * In distributed systems, it also specifies **data transfer** strategies (shipping raw data vs shipping results).
  * The Query Optimizer generates multiple plans and selects the one with the least estimated cost.

#### **8. Write the different parameters for measuring the cost of a query.**

**Answer:**
In distributed databases, the cost is a combination of:

1.  **Communication Cost (Dominant Factor):**
      * Time required to transfer data over the network.
      * $Cost = T_{transmission} * (Size of data) + T_{latency}$.
2.  **I/O Cost (Disk Access):**
      * Time to read/write blocks from the disk.
      * Depends on seek time and rotational latency.
3.  **CPU Cost:**
      * Time taken by the processor to perform operations (sorting, joining, comparing).

<!-- end list -->

  * **Total Cost** = (Communication Cost) + (I/O Cost) + (CPU Cost). In Wide Area Networks (WAN), communication cost is usually the optimization target.

#### **9. List responsibilities of Transaction Manager in Distributed Transaction Model.**

**Answer:**

1.  **Transaction Initiation:** Starts the transaction and assigns a unique Transaction ID.
2.  **Sub-transaction Generation:** Breaks the global transaction into sub-transactions for different sites.
3.  **Coordination:** Coordinates with the Scheduler (Lock Manager) to acquire necessary locks.
4.  **Commit/Abort Management:** Implements protocols like 2PC to ensure atomicity (all sites commit or all abort).
5.  **Log Management:** Maintains logs (Undo/Redo) for recovery in case of failure.
6.  **Deadlock Handling:** Detects global deadlocks across sites.

#### **10. Explain the different methods of concurrency control in distributed database.**

**Answer:**

1.  **Locking Based (2PL - Two Phase Locking):**
      * **Growing Phase:** Transaction acquires all necessary locks (Read/Write). Cannot release any.
      * **Shrinking Phase:** Transaction releases locks. Cannot acquire any new ones.
      * **Strict 2PL:** Holds exclusive locks until the transaction commits to prevent cascading rollbacks.
2.  **Timestamp Ordering (TO):**
      * No locks are used.
      * Each transaction is given a unique timestamp ($TS$).
      * Conflicts are resolved by checking timestamps: Older transactions gets priority. If a younger transaction tries to access data accessed by an older one in a conflicting way, it is aborted and restarted.
3.  **Binary Locking:**
      * Data items have two states: Locked (1) or Unlocked (0). Simple but limits concurrency (no distinction between Read/Write).

#### **11. Explain 2PC (Two-Phase Commit) in detail.**

**Answer:**
2PC ensures atomicity in distributed transactions. It has a **Coordinator** (Transaction Manager) and **Participants** (Sites).

**Phase 1: The Voting (Prepare) Phase**

1.  Coordinator sends a `PREPARE` message to all participants.
2.  Each participant executes the transaction locally up to the point of committing.
3.  They write a log record (Undo/Redo).
4.  If successful, they vote `READY` (Yes). If failed, they vote `ABORT` (No).

**Phase 2: The Decision (Commit) Phase**

1.  **If all votes are READY:** Coordinator decides to **COMMIT**.
      * Sends `COMMIT` message to all participants.
      * Participants make changes permanent and release resources.
2.  **If any vote is ABORT (or timeout):** Coordinator decides to **ABORT**.
      * Sends `ABORT` message.
      * Participants rollback changes.
3.  Coordinator writes the final decision to the log.

#### **12. Compare 2PC and 3PC.**

**Answer:**

| Feature | 2PC (Two-Phase Commit) | 3PC (Three-Phase Commit) |
| :--- | :--- | :--- |
| **Phases** | 2 (Prepare, Commit) | 3 (CanCommit, PreCommit, DoCommit) |
| **Blocking** | **Blocking Protocol:** If Coordinator fails after sending Prepare, participants are blocked waiting for decision. | **Non-Blocking Protocol:** Introduces "PreCommit" state to avoid blocking on coordinator failure. |
| **Fault Tolerance**| Lower. Cannot handle simultaneous coordinator and participant failure well. | Higher. Designed to handle site failures gracefully. |
| **Complexity** | Simpler to implement. | Complex, high communication overhead. |
| **Usage** | Widely used in practice (industry standard). | Rarely used due to high overhead (latency). |

-----

### **Module 3: Data Interoperability – XML and JSON**

#### **13. Create an XML document of 'Restaurant Menu Card' and DTD.**

**Answer:**
**XML Document:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE menu SYSTEM "menu.dtd">
<menu>
  <section category="Starters">
    <item veg="yes">
      <name>Paneer Tikka</name>
      <cost>250</cost>
      <calories>300</calories>
    </item>
  </section>
  <section category="Drinks">
    <item veg="yes">
      <name>Mojito</name>
      <cost>150</cost>
      <calories>120</calories>
    </item>
  </section>
  <section category="Main Course">
    <item veg="no">
      <name>Chicken Curry</name>
      <cost>400</cost>
      <calories>550</calories>
    </item>
  </section>
</menu>
```

**DTD Rules (`menu.dtd`):**

```xml
<!ELEMENT menu (section+)>
<!ELEMENT section (item+)>
<!ATTLIST section category CDATA #REQUIRED>
<!ELEMENT item (name, cost, calories)>
<!ATTLIST item veg (yes|no) "yes">
<!ELEMENT name (#PCDATA)>
<!ELEMENT cost (#PCDATA)>
<!ELEMENT calories (#PCDATA)>
```

#### **14. Explain XML Schema (XSD) with example.**

**Answer:**

  * **XML Schema (XSD):** An XML-based alternative to DTD. It describes the structure of an XML document.
  * **Advantages over DTD:**
      * Written in XML (no separate syntax).
      * Supports **Data Types** (Integer, String, Date, Boolean).
      * Supports namespaces.

**XSD Example (for above menu):**

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="menu">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="section" maxOccurs="unbounded">
          <xs:complexType>
             <xs:sequence>
               <xs:element name="item" maxOccurs="unbounded">
                  <xs:complexType>
                    <xs:sequence>
                      <xs:element name="name" type="xs:string"/>
                      <xs:element name="cost" type="xs:integer"/>
                      <xs:element name="calories" type="xs:integer"/>
                    </xs:sequence>
                  </xs:complexType>
               </xs:element>
             </xs:sequence>
             <xs:attribute name="category" type="xs:string"/>
          </xs:complexType>
        </xs:element>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

#### **15. Explain JSON Data Types and Valid JSON.**

**Answer:**
**JSON Data Types:**

1.  **String:** Text in double quotes. `"name": "John"`
2.  **Number:** Integer or Float. `"age": 25`, `"score": 9.5`
3.  **Boolean:** `true` or `false`.
4.  **Array:** Ordered list in `[]`. `"colors": ["red", "blue"]`
5.  **Object:** Key-value pairs in `{}`. `"address": {"city": "Mumbai"}`
6.  **Null:** Empty value. `"middlename": null`

**Valid JSON Example:**

```json
{
  "id": 101,
  "name": "Rahul",
  "isStudent": true,
  "subjects": ["Maths", "CS"],
  "address": null
}
```

**MCQ Code:** `JSON.parse('{"name":"John"}')` returns a valid object.

-----

### **Module 4: NoSQL Distribution Model**

#### **16. Compare SQL and NoSQL.**

**Answer:**

| Feature | SQL (RDBMS) | NoSQL |
| :--- | :--- | :--- |
| **Schema** | Fixed, Rigid schema. | Dynamic, Flexible schema. |
| **Scalability** | Vertical (Scale-up: Add RAM/CPU). | Horizontal (Scale-out: Add servers). |
| **Data Model** | Relational (Tables, Rows). | Key-Value, Document, Graph, Column. |
| **Transaction** | ACID properties (Strong consistency). | BASE properties (Eventual consistency). |
| **Examples** | MySQL, Oracle, PostgreSQL. | MongoDB, Cassandra, Redis, Neo4j. |

#### **17. Explain CAP Theorem.**

**Answer:**
The CAP Theorem states that a distributed computer system can efficiently provide only **two** of the following three guarantees at the same time:

1.  **Consistency (C):** Every read receives the most recent write or an error. (All nodes see the same data at the same time).
2.  **Availability (A):** Every request receives a (non-error) response, without the guarantee that it contains the most recent write.
3.  **Partition Tolerance (P):** The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

<!-- end list -->

  * **CP Systems:** MongoDB, HBase (Trade Availability for Consistency).
  * **AP Systems:** Cassandra, DynamoDB (Trade Consistency for Availability).
  * **CA Systems:** RDBMS (Traditional, single-site).



#### **18. Explain ACID vs BASE.**

**Answer:**

  * **ACID (RDBMS):**

      * **Atomicity:** All or nothing.
      * **Consistency:** Valid state to valid state.
      * **Isolation:** Transactions do not interfere.
      * **Durability:** Data is saved permanently.
      * *Focus:* Reliability and Strong Consistency.

  * **BASE (NoSQL):**

      * **Basically Available:** The system guarantees availability (data may be stale).
      * **Soft state:** The state of the system may change over time, even without input (due to replication).
      * **Eventual consistency:** The system will eventually become consistent once updates propagate.
      * *Focus:* Scalability and Performance.

#### **19. Explain Replication and Sharding in NoSQL.**

**Answer:**

  * **Sharding (Horizontal Scaling):**
      * Dividing the data set into smaller chunks (shards) and storing them across multiple servers.
      * *Benefit:* Increases **Write** capacity and storage limit.
      * *Example:* Users A-M on Server 1, N-Z on Server 2.
  * **Replication (Availability):**
      * Copying the same data to multiple servers.
      * *Benefit:* Increases **Read** capacity and Fault Tolerance (if one server fails, another has the data).
      * *Master-Slave Replication:* Writes to Master, Reads from Slaves.

-----

### **Module 5: NoSQL using MongoDB**

#### **20. Explain MongoDB CRUD Operations.**

**Answer:**

1.  **Create:**
      * `db.collection.insertOne({name: "A"})`
      * `db.collection.insertMany([{name: "A"}, {name: "B"}])`
2.  **Read:**
      * `db.collection.find()` (Select all)
      * `db.collection.find({age: {$gt: 18}})` (With condition)
3.  **Update:**
      * `db.collection.updateOne({name: "A"}, {$set: {age: 20}})`
      * `db.collection.updateMany(...)`
4.  **Delete:**
      * `db.collection.deleteOne({name: "A"})`
      * `db.collection.deleteMany(...)`

#### **21. Explain MongoDB `update()` vs `save()` methods.**

**Answer:**

  * **`update()`:**
      * Modifies an existing document.
      * Requires a query condition and an update operator (like `$set`).
      * *Example:* `db.users.update({id: 1}, {$set: {name: "New"}})` - Updates only the name field.
  * **`save()`:**
      * Acts as an Insert or Replace.
      * If the document contains an `_id` that exists, it **replaces** the entire document.
      * If `_id` is missing or does not exist, it **inserts** a new document.
      * *Example:* `db.users.save({_id: 1, name: "New"})` - Replaces the whole document with ID 1.

#### **22. Explain MongoDB Sharding (Concepts: Shard Key, Chunks, Query Router).**

**Answer:**
Sharding is MongoDB's method for horizontal scaling.

1.  **Shard:** A single MongoDB instance (or replica set) that holds a subset of the data.
2.  **Shard Key:** An indexed field (e.g., `user_id`) used to partition data. It determines which shard a document goes to.
3.  **Chunks:** MongoDB divides the data into blocks called chunks based on the Shard Key range.
4.  **Config Servers:** Store metadata (which chunk is on which shard).
5.  **Query Router (Mongos):** Interface for the client. It checks Config Servers and routes the client's query to the correct Shard(s).

-----

### **Module 6: Trends in Advance Databases**

#### **23. What is a Graph Database? Describe with Neo4j.**

**Answer:**
A **Graph Database** uses graph structures for semantic queries with nodes, edges, and properties to represent and store data. It excels at managing highly connected data (e.g., Social Networks).

**Neo4j Concepts:**

1.  **Nodes:** Entities (e.g., Person, Movie). Represented by circles.
2.  **Relationships (Edges):** Connect nodes (e.g., `FRIENDS_WITH`, `ACTED_IN`). Must have a direction and type.
3.  **Properties:** Key-value pairs stored on Nodes or Relationships (e.g., `name: "John"`).
4.  **Labels:** Group nodes into sets (e.g., `:Person`).
5.  **Cypher:** The query language for Neo4j (ASCII-art style).
      * *Example:* `MATCH (p:Person)-[:FRIENDS]->(f) RETURN p, f`

#### **24. Explain Spatial Database.**

**Answer:**

  * **Definition:** Optimized to store and query data that represents objects defined in geometric space (maps, GPS data).
  * **Data Types:**
      * **Vector Data:** Points (GPS), Lines (Roads), Polygons (Countries).
      * **Raster Data:** Grid of cells/pixels (Satellite images).
  * **Indexing:** Uses specialized indices like **R-Trees** or **Quad-Trees** for fast spatial querying (e.g., "Find all ATMs within 1km").

#### **25. Explain Temporal Database.**

**Answer:**
A database with built-in support for handling time-sensitive data. It tracks *when* data happened.

  * **Valid Time:** The time period when a fact was true in the **real world**. (e.g., John lived in Mumbai from 2010 to 2015).
  * **Transaction Time:** The time period when the data was stored in the **database**. (e.g., Data entered on 2024-01-01).
  * **Bi-temporal:** Supports both.
  * **Benefit:** Allows historical queries ("What was John's salary in 2012?").
