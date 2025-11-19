# 📚 Distributed Databases & NoSQL: Exam Syllabus Guide

## Module 1: Distributed Databases

### 1\. Explain Distributed DBMS Architecture.

The architecture of a Distributed Database Management System (DDBMS) defines the organization and interaction of system components. It is generally divided into three abstraction levels:

1.  **Internal Level (Physical Level):**
      * Deals with physical storage at each specific site.
      * Manages local schema, data placement, and local transaction management.
2.  **Conceptual Level:**
      * Provides a **Global Conceptual Schema (GCS)**: A logical view of the entire database as if it were centralized.
      * Ensures **Transparency**: Hides details of fragmentation and replication from the user.
3.  **External Level (User Level):**
      * Defines user views.
      * Users interact without knowing data location (Location Transparency).

**Types of Architectures:**

  * **Client-Server:** Tasks split between UI/Request (Client) and Data Management (Server).
  * **Peer-to-Peer:** All nodes are equal; no central controller.
  * **Multi-DBMS:** A federation of autonomous, potentially heterogeneous databases.

### 2\. Explain Homogenous and Heterogeneous Distributed Database Systems.

| Feature | Homogeneous DDBMS | Heterogeneous DDBMS |
| :--- | :--- | :--- |
| **Definition** | All sites use the **same** DBMS software (e.g., all Oracle). | Sites run **different** DBMS software (e.g., Oracle + SQL Server + MongoDB). |
| **Schema** | Shares a common global schema. | Each site has its own schema; requires a global mapping. |
| **Complexity** | Low. Data exchange is straightforward. | High. Requires middleware/gateways for translation. |
| **Pros** | High performance, easier to manage. | Allows integration of legacy systems. |
| **Cons** | Less flexible. | Complex query processing & transaction management (2PC is hard). |

### 3\. Explain Client/Server Database Architecture.

Processing is divided between two modules:

![Image of Client Server Database Architecture](https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcRazDIYqrOA1wmq7Ews2ldKv_KBXHno6QLjtilSlF5RahR-94lvr6cU-7WYzXH28aqFTO-W8TjVN4r4bCxIzzQXKr4xN00m7u3YN3U_EIudOCWJ6bU)
*Image of Client Server Database Architecture*

  * **Client (Front-end):**
      * Manages the User Interface (UI).
      * Does *not* manage data directly.
      * Sends SQL requests to the server.
  * **Server (Back-end):**
      * Manages storage, access control, and query processing.
      * Returns only the result set to the client.

**Configurations:**

  * *Multiple Client-Single Server:* Standard setup.
  * *Multiple Client-Multiple Server:* Server may distribute queries if data is distributed.

> **Advantage:** Reduces network traffic (raw data stays on the server; only results travel).

### 4\. What are data replication and allocation techniques?

**A. Data Replication**
Storing copies of the same data at multiple sites to improve availability.

  * **Full Replication:** Entire DB is at every site (High availability, slow updates).
  * **Partial Replication:** Only frequently accessed fragments are replicated.
  * **No Replication:** Each fragment exists at only one site.

**B. Allocation Techniques**
Deciding where to store specific data fragments.

  * **Centralized:** All data at one site (Single point of failure).
  * **Fragmented (Partitioned):** Data split and stored where used most.
  * **Replicated:** Copies maintained at multiple sites.
  * *Dynamic vs Static:* Moving data based on access patterns vs. fixed placement.

### 5\. Explain Database Fragmentation (Horizontal, Vertical, Mixed).

Fragmentation divides a global relation into smaller logical units.

  * **Horizontal Fragmentation:**
      * Splits table by **rows** (tuples) based on a condition (Selection $\sigma$).
      * *Example:* `City='NY'` rows in one fragment, `City='LA'` in another.
      * *Reconstruction:* Union ($\cup$).
  * **Vertical Fragmentation:**
      * Splits table by **columns** (attributes) (Projection $\Pi$).
      * *Requirement:* Every fragment must keep the Primary Key.
      * *Reconstruction:* Join ($\bowtie$) on Primary Key.
  * **Mixed (Hybrid):** Applying both horizontal and vertical fragmentation.

-----

## Module 2: Distributed Database Handling

### 6\. Discuss the phases of distributed query processing.

Distributed query processing turns a high-level query into an efficient execution strategy.

1.  **Query Decomposition:** Parses SQL into algebraic queries; performs normalization and rewriting.
2.  **Data Localization:** Uses the Fragment Schema to map the global query onto specific fragments (determines which sites contain the data).
3.  **Global Query Optimization:** Finds the strategy with the least cost (minimizing communication). Output is a Global Execution Plan.
4.  **Distributed Query Execution:** Local optimization occurs at each site; sub-queries execute, and data is transferred to the result site.

### 7\. Describe Query Evaluation Plan.

A sequence of primitive operations annotated with execution instructions.

  * **Algorithms:** Specifies method (e.g., Merge Join vs. Hash Join).
  * **Indices:** Specifies access paths (e.g., Use B+ Tree on `ID`).
  * **Ordering:** Determines sequence of operations.
  * **Data Transfer:** Decides between shipping raw data vs. shipping results.

### 8\. Parameters for measuring Query Cost.

In distributed systems, the cost function is:

$$\text{Total Cost} = \text{CPU Cost} + \text{I/O Cost} + \text{Communication Cost}$$

1.  **Communication Cost (Dominant Factor in WAN):**
    $$Cost = (T_{transmission} \times \text{Data Size}) + T_{latency}$$
2.  **I/O Cost:** Disk access time (Seek time + Rotational latency).
3.  **CPU Cost:** Processing time for sorting, joining, etc.

### 9\. Responsibilities of Transaction Manager (TM).

  * **Initiation:** Assigns unique Transaction ID (TID).
  * **Sub-transaction Generation:** Breaks global transaction into site-specific sub-transactions.
  * **Coordination:** Works with the Scheduler/Lock Manager.
  * **Commit/Abort:** Implements protocols (like 2PC) for atomicity.
  * **Recovery:** Maintains Undo/Redo logs.

### 10\. Methods of Concurrency Control.

  * **Locking Based (2PL - Two Phase Locking):**
      * *Growing Phase:* Acquire locks (cannot release).
      * *Shrinking Phase:* Release locks (cannot acquire).
  * **Timestamp Ordering (TO):**
      * No locks. Transactions assigned a Timestamp ($TS$).
      * Conflict resolution: Older transactions usually get priority; younger conflicting transactions are restarted.
  * **Binary Locking:** Data is either Locked (1) or Unlocked (0). (Simpler, but lower concurrency).

### 11\. Explain 2PC (Two-Phase Commit) in detail.

Ensures atomicity: "All sites commit, or all abort."

  * **Phase 1: The Voting (Prepare):**
      * Coordinator sends `PREPARE`.
      * Participants execute locally, write logs, and vote `READY` or `ABORT`.
  * **Phase 2: The Decision (Commit):**
      * If all vote `READY` $\rightarrow$ Coordinator sends `COMMIT`.
      * If any vote `ABORT` (or timeout) $\rightarrow$ Coordinator sends `ABORT`.

### 12\. Compare 2PC and 3PC.

| Feature | 2PC (Two-Phase Commit) | 3PC (Three-Phase Commit) |
| :--- | :--- | :--- |
| **Phases** | 2 (Prepare, Commit) | 3 (CanCommit, PreCommit, DoCommit) |
| **Blocking** | **Blocking:** If Coordinator fails, participants wait indefinitely. | **Non-Blocking:** "PreCommit" state prevents infinite waiting. |
| **Fault Tolerance** | Lower. | Higher (handles site failures gracefully). |
| **Usage** | Industry Standard (Practical). | Rare (High network overhead). |

-----

## Module 3: Data Interoperability – XML and JSON

### 13\. XML Document ('Restaurant Menu') and DTD.

**XML Document:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE menu SYSTEM "menu.dtd">
<menu>
  <section category="Starters">
    <item veg="yes">
      <name>Paneer Tikka</name>
      <cost>250</cost>
    </item>
  </section>
  <section category="Main Course">
    <item veg="no">
      <name>Chicken Curry</name>
      <cost>400</cost>
    </item>
  </section>
</menu>
```

**DTD Rules (`menu.dtd`):**

```xml
<!ELEMENT menu (section+)>
<!ELEMENT section (item+)>
<!ATTLIST section category CDATA #REQUIRED>
<!ELEMENT item (name, cost)>
<!ATTLIST item veg (yes|no) "yes">
<!ELEMENT name (#PCDATA)>
<!ELEMENT cost (#PCDATA)>
```

### 14\. Explain XML Schema (XSD).

XSD is an XML-based alternative to DTD that supports **Data Types** (Integer, Date, Boolean).

**Example XSD Snippet:**

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="cost" type="xs:integer"/>
  <xs:element name="name" type="xs:string"/>
</xs:schema>
```

### 15\. JSON Data Types and Valid Example.

**Data Types:**

1.  **String:** `"name": "Rahul"`
2.  **Number:** `"age": 25`
3.  **Boolean:** `"isStudent": true`
4.  **Array:** `"subjects": ["Math", "CS"]`
5.  **Object:** `"address": {"city": "Mumbai"}`
6.  **Null:** `"middle_name": null`

**Valid JSON:**

```json
{
  "id": 101,
  "name": "Rahul",
  "isStudent": true,
  "subjects": ["Maths", "CS"],
  "address": null
}
```

-----

## Module 4: NoSQL Distribution Model

### 16\. Compare SQL and NoSQL.

| Feature | SQL (RDBMS) | NoSQL |
| :--- | :--- | :--- |
| **Schema** | Fixed, Rigid. | Dynamic, Flexible. |
| **Scaling** | Vertical (Better hardware). | Horizontal (More servers). |
| **Transaction** | ACID (Strong Consistency). | BASE (Eventual Consistency). |
| **Structure** | Tables & Rows. | Key-Value, Document, Graph. |

### 17\. Explain CAP Theorem.

A distributed system can efficiently provide only **two** of the three guarantees:

![Image of CAP Theorem Venn Diagram](https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcT25ITEIfnySqsF88Rw_QG85VIQtqC5p-l-l9sc_46jIrKxD46vIy5Jf1aMoXIDav3Panb7e5exIoqkciAPO-rh6t1E6cBxiOcVOjHXrFkEBv8ZFIc)
*Image of CAP Theorem Venn Diagram*

1.  **Consistency (C):** All nodes see the same data at the same time.
2.  **Availability (A):** Every request gets a response (even if data is stale).
3.  **Partition Tolerance (P):** System works even if network messages drop.

<!-- end list -->

  * **CP:** MongoDB, HBase.
  * **AP:** Cassandra, DynamoDB.
  * **CA:** Traditional RDBMS (Not possible in distributed networks).

### 18\. Explain ACID vs BASE.

  * **ACID (SQL):** Atomicity, Consistency, Isolation, Durability. (Focus: Reliability).
  * **BASE (NoSQL):**
      * **B**asically **A**vailable.
      * **S**oft state (State may change without input).
      * **E**ventual consistency (Data will become consistent over time).

### 19\. Explain Replication and Sharding in NoSQL.

  * **Sharding (Scale-out):** Splitting data into chunks (shards) across servers. Increases **Write** capacity.
  * **Replication (Fault Tolerance):** Copying data to multiple servers. Increases **Read** capacity and reliability.

-----

## Module 5: NoSQL using MongoDB

### 20\. MongoDB CRUD Operations.

```javascript
// Create
db.users.insertOne({name: "A", age: 20})

// Read
db.users.find({age: {$gt: 18}})

// Update (Set specific field)
db.users.updateOne({name: "A"}, {$set: {age: 25}})

// Delete
db.users.deleteOne({name: "A"})
```

### 21\. MongoDB `update()` vs `save()`.

  * **update():** Modifies specific fields of an existing document.
  * **save():**
      * If `_id` exists: **Replaces** the entire document.
      * If `_id` is missing: **Inserts** a new document.

### 22\. Explain MongoDB Sharding Components.

  * **Shard:** A MongoDB instance holding a subset of data.
  * **Shard Key:** Indexed field (e.g., `user_id`) used to partition data.
  * **Chunks:** Data blocks divided by Shard Key range.
  * **Query Router (Mongos):** Directs client queries to the correct shard.

-----

## Module 6: Trends in Advance Databases

### 23\. Graph Database (Neo4j).

Optimized for highly connected data (e.g., Social Networks).

  * **Nodes:** Entities (Person, Movie).
  * **Relationships:** Edges (FRIENDS\_WITH). Must have direction.
  * **Properties:** Key-value pairs on nodes/edges.
  * **Cypher Query:** `MATCH (p:Person)-[:FRIENDS]->(f) RETURN p`

### 24\. Spatial Database.

Stores geometric space data.

  * **Vector Data:** Points (GPS), Lines (Roads), Polygons.
  * **Raster Data:** Satellite imagery (pixels).
  * **Indexing:** Uses **R-Trees** or Quad-Trees.

### 25\. Temporal Database.

Handles time-sensitive data.

  * **Valid Time:** When the fact was true in reality.
  * **Transaction Time:** When the data was stored in the DB.
  * **Bi-temporal:** Tracks both.
