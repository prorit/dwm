# 📚 Distributed Databases & NoSQL: Exam Syllabus Guide

## Module 1: Distributed Databases

### 1\. Explain Data Fragmentation in Distributed Databases.

**Fragmentation** is the process of dividing a single logical relation (table) into smaller segments called fragments, which can be stored at different sites. This improves efficiency by placing data closer to where it is most frequently used.

  * **Types of Fragmentation:**
    1.  **Horizontal Fragmentation:** Divides the relation into tuples (rows). It uses a selection operation ($\sigma$) based on a predicate (condition).
          * *Example:* Splitting a `Customer` table into `Customer_NewYork` and `Customer_London` based on the `City` column.
          * *Reconstruction:* Done using the **Union** operation.
    2.  **Vertical Fragmentation:** Divides the relation into attributes (columns). It uses the projection operation ($\pi$). The Primary Key must be included in all fragments to allow reconstruction.
          * *Example:* Splitting `Employee` into `Emp_Personal` (ID, Name, Address) and `Emp_Professional` (ID, Salary, Dept).
          * *Reconstruction:* Done using the **Join** operation on the Primary Key.
    3.  **Hybrid (Mixed) Fragmentation:** A combination of horizontal and vertical fragmentation. A table is first fragmented horizontally, and those fragments are then fragmented vertically (or vice versa).

### 2\. Explain Allocation techniques for Distributed Database Design.

**Data Allocation** determines where the fragments or the entire database should be physically stored across the computer network.

  * **Allocation Strategies:**
    1.  **Fragmented (Partitioned) Allocation:** The database is fragmented, and each fragment is stored at exactly one site. There is **no replication**.
          * *Pros:* Low storage cost; easy updates (no synchronization needed).
          * *Cons:* Low availability (if a site fails, data is lost); poor performance for queries accessing remote data.
    2.  **Full Replication:** A complete copy of the entire database is stored at every site in the network.
          * *Pros:* High availability; fast local reading.
          * *Cons:* Very high storage cost; slow updates (must update all copies to maintain consistency).
    3.  **Partial Replication:** Only frequently accessed fragments are replicated at specific sites, while others are stored as single copies.
          * *Pros:* Balances storage costs and availability.
          * *Cons:* Complex management of copies.

### 3\. Explain Distributed DBMS Architecture in details.

Distributed DBMS architecture defines how the system components interact.

  * **Client-Server Architecture:**
      * **Client:** Handles the user interface and local processing. It sends database requests to the server.
      * **Server:** Handles data storage, query processing, transaction management, and optimization.
      * *Multiple Client-Single Server:* All clients access one central DB (simpler).
      * *Multiple Client-Multiple Server:* Clients access data distributed across multiple servers.
  * **Peer-to-Peer Architecture:**
      * There is no distinction between client and server. Every site (node) has full DBMS functionality.
      * Each site acts as a server for other sites and a client for its own local users.
      * More complex but offers better scalability and reliability.
  * **Multi-Database System (MDBS) Architecture:**
      * A collection of autonomous, pre-existing databases integrated to look like one system.
      * **Loosely Coupled:** Users must know the schema of each local DB.
      * **Tightly Coupled:** A Global Conceptual Schema (GCS) unifies all local schemas, providing location transparency.

### 4\. Differentiate between Centralized and Distributed Database.

| Feature | Centralized Database | Distributed Database |
| :--- | :--- | :--- |
| **Location** | All data resides at a single physical site/server. | Data is spread across multiple sites connected by a network. |
| **Failure** | Single point of failure. If the site crashes, the system stops. | High reliability. If one site fails, others continue to function. |
| **Scalability** | Vertical scaling (add more power to one machine). Harder to scale. | Horizontal scaling (add more machines). Easier to scale. |
| **Complexity** | Low complexity in concurrency and recovery. | High complexity (requires 2PC, distributed locking, etc.). |
| **Access Speed** | Slower for remote users. | Faster if data is located near the user. |

-----

## Module 2: Distributed Database Handling

### 5\. Explain phases in Distributed Query processing with neat diagram.

Distributed query processing involves four layers to transform a high-level query into a low-level execution plan.

1.  **Query Decomposition:**
      * Input: Calculus query on Global Relations.
      * Action: Normalizes the query, analyzes it for errors, eliminates redundancy, and rewrites it into Relational Algebra.
      * Output: Algebraic Query on Global Relations.
2.  **Data Localization:**
      * Input: Algebraic Query on Global Relations.
      * Action: Uses the **Fragmentation Schema** to determine which fragments contain the requested data. It replaces global relations with fragments (unions/joins).
      * Output: Fragment Query.
3.  **Global Query Optimization:**
      * Input: Fragment Query.
      * Action: Finds the most efficient strategy to execute the query. It focuses on minimizing **Communication Cost** (data transfer between sites) and decides the order of joins (often using Semijoins).
      * Output: Distributed Execution Plan.
4.  **Distributed Execution:**
      * Input: Execution Plan.
      * Action: The plan is sent to local sites. Each site performs local optimization and executes its sub-query.

### 6\. Explain ACID properties.

ACID properties ensure the reliability of transactions.

1.  **Atomicity:** The "All or Nothing" rule. A transaction is treated as a single unit; either all its operations happen, or none do. If a failure occurs halfway, the system rolls back to the start.
2.  **Consistency:** A transaction must transform the database from one valid state to another. It ensures that database invariants (like constraints and foreign keys) are preserved.
3.  **Isolation:** Multiple transactions executing concurrently must not interfere with each other. The intermediate state of one transaction should not be visible to others until it commits.
4.  **Durability:** Once a transaction commits, its changes are permanent, even in the event of a system crash. This is typically achieved via write-ahead logging.

### 7\. Explain 2PC (Two-Phase Commit) in detail with neat diagram.

2PC ensures atomicity in distributed systems where multiple sites must agree to commit.

  * **Roles:** One site acts as the **Coordinator**, others are **Participants**.
  * **Phase 1: The Voting Phase (Prepare)**
    1.  Coordinator sends a `PREPARE` message to all participants.
    2.  Participants execute the transaction locally (write to log) but do not commit.
    3.  If successful, Participant sends `VOTE_COMMIT` and enters "Ready" state. If failed, sends `VOTE_ABORT`.
  * **Phase 2: The Decision Phase (Commit/Abort)**
    1.  If Coordinator receives `VOTE_COMMIT` from **ALL** participants:
          * It sends a `GLOBAL_COMMIT` message.
          * Participants commit changes and release locks.
    2.  If Coordinator receives `VOTE_ABORT` from **ANY** participant (or times out):
          * It sends a `GLOBAL_ABORT` message.
          * Participants rollback.
  * *Drawback:* **Blocking.** If the Coordinator fails after sending PREPARE, participants are locked in a "Ready" state and cannot proceed.

### 8\. Explain 3PC (Three-Phase Commit) in detail with neat diagram.

3PC adds an extra phase to prevent the blocking problem of 2PC.

  * **Phase 1: Voting Phase:** Same as 2PC (Coordinator asks for votes).
  * **Phase 2: Pre-Commit Phase (New):**
      * If all voted Commit, Coordinator sends `PRE_COMMIT`.
      * Participants acknowledge this. *Crucially, this ensures all sites know that a commit is possible before actually doing it.*
  * **Phase 3: Do-Commit Phase:**
      * Coordinator sends `DO_COMMIT`.
      * Participants finalize the transaction.
  * *Benefit:* If the Coordinator fails, a new Coordinator can check if anyone received a `PRE_COMMIT`. If so, they can safely finish the commit; otherwise, they abort. No one is blocked indefinitely.

### 9\. Explain Distributed concurrency control techniques and recovery.

  * **Distributed Concurrency Control:**
      * **Locking (2PL):** A transaction must acquire locks on items before accessing them. In Distributed 2PL, a Lock Manager at each site manages local locks.
      * **Timestamp Ordering (TO):** Each transaction is assigned a unique timestamp. Operations are ordered based on these timestamps. If an older transaction tries to access data modified by a younger one, it is rolled back.
  * **Recovery in Distributed Databases:**
      * **Logging:** Each site maintains a Write-Ahead Log (WAL).
      * **Handling Site Failure:** When a site comes back online, it checks its log. If a transaction was committed, it Redoes; if incomplete, it Undoes.
      * **Handling Network Partitioning:** If the network splits, the system must decide which partition can continue processing (usually the majority partition) to maintain consistency.

### 10\. Differentiate between 2PC and 3PC Protocol.

| Feature | 2PC (Two-Phase Commit) | 3PC (Three-Phase Commit) |
| :--- | :--- | :--- |
| **Phases** | Two: Voting, Decision. | Three: Voting, Pre-Commit, Do-Commit. |
| **Blocking** | **Blocking Protocol.** If Coordinator fails, sites may wait indefinitely. | **Non-Blocking Protocol.** Timeouts allow sites to resolve the state. |
| **Overhead** | Lower communication overhead (fewer messages). | Higher overhead (more round-trip messages). |
| **Reliability** | Less reliable in case of site failure. | More reliable/resilient to failures. |

### 11\. Write a short note on Query Evaluation plan.

A **Query Evaluation Plan** (or Execution Plan) is a sequence of primitive operations (like selection, projection, join) used to execute a query.

  * The Query Optimizer generates multiple plans and estimates the cost for each.
  * It selects the plan with the **least cost** (fewest I/O operations or lowest network transfer).
  * The plan is often represented as a **Tree**, where leaf nodes are relations and internal nodes are operators.

### 12\. Write a short note on Query processing issues in Heterogenous Database.

Heterogeneous databases use different hardware, software, or data models. Issues include:

1.  **Schema Translation:** Mapping different local schemas (e.g., relational vs. object-oriented) to a global schema.
2.  **Data Type Mismatch:** One DB might store "Salary" as a Float, another as a String.
3.  **Semantic Heterogeneity:** Naming conflicts (e.g., "ID" in one DB is "Emp\_No" in another) or scaling differences (dollars vs. rupees).
4.  **Query Translation:** Converting the global query into the specific query language (SQL, XQuery, etc.) of the local system.

### 13\. Different parameters for measuring cost OR Transaction Manager Responsibilities.

  * **Cost Parameters:**
    1.  **Access Cost (I/O):** Time to read/write data blocks from disk.
    2.  **CPU Cost:** Time spent processing data (sorting, joining) in memory.
    3.  **Communication Cost:** (Most important in Distributed DB) Time and bandwidth used to transfer data between sites.
  * **Responsibilities of Transaction Manager (TM):**
    1.  **Coordination:** Starting and terminating transactions.
    2.  **ACID Enforcement:** ensuring atomicity and isolation.
    3.  **Communication:** Talking to the Scheduler and Data Manager.
    4.  **Recovery:** maintaining logs to recover from failures.

-----

## Module 3: Data Interoperability – XML and JSON

### 14\. Explain Basic JSON syntax with data types.

**JSON (JavaScript Object Notation)** is a lightweight text format for data interchange.

  * **Syntax Rules:**
      * Data is in name/value pairs (`"key": value`).
      * Data is separated by commas.
      * Curly braces `{}` hold **Objects**.
      * Square brackets `[]` hold **Arrays**.
  * **Data Types:**
    1.  **String:** `"name": "John"`
    2.  **Number:** `"age": 30` (Integer or Float)
    3.  **Boolean:** `"registered": true`
    4.  **Null:** `"middleName": null`
    5.  **Object:** `"address": {"city": "Mumbai", "zip": 400001}`
    6.  **Array:** `"hobbies": ["reading", "coding"]`

### 15\. What is XML? Explain XML Schema document with example.

**XML (eXtensible Markup Language)** is a markup language designed to store and transport data. It uses tags (like HTML) but allows users to define their own tags.

  * **XML Schema (XSD):** An XML-based alternative to DTD. It describes the structure of an XML document. It supports **Data Types** (integer, string, date), which allows for stronger validation.
  * **Example 1:**
      * *XML:*
        ```xml
        <note>
           <to>User</to>
           <from>Admin</from>
           <priority>1</priority>
        </note>
        ```
      * *XSD:*
        ```xml
        <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
          <xs:element name="note">
            <xs:complexType>
              <xs:sequence>
                <xs:element name="to" type="xs:string"/>
                <xs:element name="from" type="xs:string"/>
                <xs:element name="priority" type="xs:integer"/>
              </xs:sequence>
            </xs:complexType>
          </xs:element>
        </xs:schema>
        ```
  * **Example 2:**
    * *XML:*
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
    * *DTD Rules:*
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
    * *XSD:*
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


### 16\. Differentiate between XML and JSON.

| Feature | XML | JSON |
| :--- | :--- | :--- |
| **Format** | Markup Language (Tags). | JavaScript Object style. |
| **Verbosity** | Heavy/Verbose (Closing tags require more characters). | Lightweight (Compact). |
| **Parsing** | Slower (requires DOM/SAX parsers). | Faster (native to JS). |
| **Data Types** | No inherent types (everything is text unless XSD used). | Supports Number, String, Boolean, Array natively. |
| **Usage** | Complex documents, configuration files. | Web APIs, Mobile apps, NoSQL storage. |

-----

## Module 4: NoSQL Distribution Model

### 17\. Explain CAP theorem NoSQL Database.

The **CAP Theorem** states that a distributed computer system can only provide **two** of the following three guarantees:

[Image of CAP Theorem Diagram]

1.  **Consistency (C):** Every read receives the most recent write or an error. (All nodes have the same data at the same time).
2.  **Availability (A):** Every request receives a (non-error) response, without the guarantee that it contains the most recent write.
3.  **Partition Tolerance (P):** The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

<!-- end list -->

  * *NoSQL Choice:* Since P is mandatory in distributed systems, NoSQL DBs choose either **CP** (MongoDB, HBase) or **AP** (Cassandra, CouchDB).

### 18\. Explain Replication and Sharding in NoSQL.

  * **Replication:** Copying data to multiple servers to ensure High Availability.
      * *Master-Slave:* One node takes writes, others replicate for reads.
      * *Peer-to-Peer:* Any node can take writes.
  * **Sharding:** Partitioning data across multiple servers (shards) to ensure Scalability.
      * Data is split based on a **Shard Key**.
      * Each shard holds a subset of the total data.
      * Allows the system to handle more data than fits on a single machine.

### 19\. Explain benefits of NoSQL.

1.  **Elastic Scalability:** Designed for horizontal scaling (adding commodity servers) rather than vertical scaling.
2.  **Flexible Schema:** No predefined schema is required. You can add fields on the fly. Useful for unstructured data.
3.  **High Performance:** Optimized for specific data models (e.g., Key-Value pairs) allowing faster lookups than complex SQL joins.
4.  **Big Data Capability:** Can handle high velocity, volume, and variety of data (3Vs).

### 20\. Differentiate between SQL and NoSQL.

| Feature | SQL (Relational) | NoSQL (Non-Relational) |
| :--- | :--- | :--- |
| **Structure** | Table-based (Rows/Columns). | Document, Key-Value, Graph, Column. |
| **Schema** | Rigid/Fixed. | Dynamic/Flexible. |
| **Scalability** | Vertical (Scale Up). | Horizontal (Scale Out). |
| **Relations** | Supports JOINs. | JOINs typically handled in app code. |
| **Transactions** | ACID (Strong consistency). | BASE (Eventual consistency). |

### 21\. Differentiate between ACID and BASE.

  * **ACID (SQL):** Focuses on consistency.
      * **A**tomicity, **C**onsistency, **I**solation, **D**urability.
      * System is pessimistic; assumes failures are rare and data must always be perfect.
  * **BASE (NoSQL):** Focuses on availability.
      * **B**asically **A**vailable: System guarantees availability.
      * **S**oft state: State may change over time, even without input (due to replication).
      * **E**ventually consistent: The system will eventually become consistent once inputs stop.

### 22\. Write a short note on NoSQL data modelling.

NoSQL modeling differs from RDBMS normalization.

  * **Aggregates:** Data is often stored together in a single document (nested) rather than split into multiple tables.
  * **Denormalization:** Data duplication is encouraged to optimize for **read performance** (avoids joins).
  * **Application-Side Joins:** Relationships are often resolved by the application code rather than the database.
  * **Schemaless:** Fields can differ between documents in the same collection.

-----

## Module 5: NoSQL using MongoDB

### 23\. Explain MongoDB Sharding.

Sharding in MongoDB distributes data across a cluster.

  * **Components:**
    1.  **Shard:** A replica set that holds a subset of the data.
    2.  **Mongos (Query Router):** Interfaces with applications. It directs operations to the appropriate shard.
    3.  **Config Servers:** Store metadata and configuration settings for the cluster (mapping of chunks to shards).
  * **Shard Key:** A field chosen to partition the data. MongoDB divides data into **Chunks** based on this key and distributes chunks evenly.

### 24\. Explain MongoDB CRUD Operations.

1.  **Create:** `db.collection.insertOne({name: "A", age: 10})` or `insertMany([...])`.
2.  **Read:** `db.collection.find({age: {$gt: 18}})` retrieves documents matching criteria.
3.  **Update:** `db.collection.updateOne({name: "A"}, {$set: {age: 11}})` modifies existing documents.
4.  **Delete:** `db.collection.deleteOne({name: "A"})` removes documents.

### 25\. Explain MongoDB Sharding (Repeated) and basic data types.

*(Sharding is covered in Q23. Focusing on Types)*:
MongoDB uses **BSON** (Binary JSON). Common types:

  * **String:** UTF-8 text.
  * **Integer/Double:** Numeric values.
  * **Boolean:** true/false.
  * **Array:** Lists of values.
  * **Object:** Embedded documents.
  * **ObjectId:** Unique primary key (automatic).
  * **Date:** ISODate format.

-----

## Module 6: Trends in Advance Databases

### 26\. What is Graph database? What are the features of Graph database?

A Graph Database uses graph structures with nodes, edges, and properties to represent and store data.

  * **Features:**
    1.  **Nodes:** Entities (e.g., Person, City).
    2.  **Edges:** Relationships (e.g., LIVES\_IN, KNOWS). Edges are "first-class citizens" and stored directly.
    3.  **Properties:** Key-value pairs attached to nodes or edges.
    4.  **Index-Free Adjacency:** Traversing relationships is extremely fast because pointers are stored physically; no index lookups needed for joins.

### 27\. Explain Spatial Database in details.

A Spatial Database is optimized to store and query data that represents objects defined in a geometric space (GIS).

  * **Data Types:**
      * **Point:** (x, y) coordinates (e.g., GPS location).
      * **LineString:** A path (e.g., a road or river).
      * **Polygon:** A closed area (e.g., a park or country boundary).
  * **Indexing:** Uses specialized indices like **R-Trees** or **Quad-Trees** to efficiently query spatial data (e.g., "Find all restaurants within 5km").
  * **Operations:** Distance calculation, Intersection, containment.

### 28\. Explain Temporal database in details.

A Temporal Database handles time-varying data, allowing queries about the past, present, and future.

  * **Time Dimensions:**
    1.  **Valid Time:** The time period when a fact is true in the real world. (e.g., John was Manager from 2010 to 2012).
    2.  **Transaction Time:** The time period when the fact was stored in the database.
  * **Types:**
      * *Uni-Temporal:* Supports only one dimension.
      * *Bi-Temporal:* Supports both Valid and Transaction time. Allows "Time Travel" queries (e.g., "What did we think John's salary was back in 2011?").

### 29\. Explain Graph Database using Neo4j (Case Study).

**Neo4j** is the most popular Graph Database.

  * **Model:** It uses the **Label Property Graph** model.
      * Nodes have **Labels** (e.g., `:Person`).
      * Relationships have **Types** (e.g., `:FRIEND`).
  * **Language:** Uses **Cypher** Query Language (ASCII-Art style).
      * *Example:* `MATCH (p:Person)-[:FRIEND]->(f) RETURN p, f`
  * **Use Cases:** Social Networks, Fraud Detection (linking rings of thieves), Recommendation Engines (User -\> Bought -\> Product).
  * **Storage:** It uses native graph storage (pointers), making deep traversals (joins) constant time operations.
