# 📚 ADBMS QB

## Module 1: Distributed Databases (3 Marks)

### Q 1.1: What are data replication and allocation techniques for Distributed database design?

Data replication and allocation are critical techniques for designing distributed databases, focusing on performance, reliability, and scalability.

#### 1. Data Replication (Copying Data)

Replication involves storing multiple copies of the same data across different sites.

  * **Full Replication:** All data is copied to every site. **Pros:** High availability and fast reads. **Cons:** High storage cost and slow updates (since all copies must be updated).
  * **Partial Replication:** Only a subset of data is copied to selected sites based on access needs. **Pros:** Reduced storage compared to full replication. **Cons:** May require accessing remote sites for non-local data.
  * **No Replication (Non-replicated):** Each piece of data is stored in one unique place. **Pros:** Simplest to manage and update. **Cons:** Data is unavailable if the hosting site fails.

#### 2. Data Allocation (Where Data is Stored)

Allocation determines where data (or data fragments) is physically placed across the system.

  * **Fragmented Allocation:** Data is logically split into smaller pieces (**fragments**) and each fragment is stored at different sites. This aligns data closer to where it's most used. **Pros:** Faster access to specific data, reduced data transfer. **Cons:** Complex to manage; reconstructing the original data requires combining fragments.
  * **Replicated Allocation:** Copies of fragments are placed at multiple sites, combining fragmentation with replication. **Pros:** Reliable, high availability. **Cons:** Higher storage and update overhead.
  * **Centralized Allocation:** All data resides in a single, central location. **Pros:** Simple to implement. **Cons:** Single point of failure, slower access for distant users.

-----

## Module 2: Distributed Database Handling (8 Marks)

### Q 2.1: Discuss the phases of distributed query processing with a neat diagram.

Distributed query processing is typically divided into **four main layers** to manage the complexity of executing a query across multiple sites.

-----

#### 1. Query Decomposition

This is the **first layer**, where the input relational **calculus query** (high-level query on global relations) is transformed into an **algebraic query** (lower-level query) on global relations. This step uses the **Global Schema** and is the same for both centralized and distributed systems.

The decomposition process involves four steps:

1.  **Normalization:** The query is rewritten into a canonical form (e.g., Conjunctive Normal Form) suitable for manipulation.
2.  **Analysis:** The normalized query is semantically checked to detect and reject incorrect (e.g., type incorrect or semantically incorrect) queries.
3.  **Elimination of Redundancy/Simplification:** Redundant predicates are removed to simplify the query.
4.  **Restructuring/Rewriting:** The calculus query is translated into an equivalent, efficient algebraic query.

-----

#### 2. Data Localization

The **second layer** accepts the algebraic query on global relations as input. Its main role is to transform the query on global relations into a query on **fragments** (subsets of data) by applying the data distribution rules. It uses the **Fragment Schema**.

  * This process essentially converts the query from operating on abstract logical relations to concrete physical fragments located at different sites, often using a **localization program** (or reconstruction program).

-----

#### 3. Global Query Optimization

The **third layer** takes the algebraic query on fragments as input. The goal is to find an optimal execution strategy that minimizes a cost function (e.g., communication cost, CPU cost). It primarily focuses on:

  * Finding the best ordering of relational operators.
  * Determining the necessary **communication operations** (e.g., network transfers), often promoting the use of **semi-join** to reduce data transfer.

The output is a **Distributed Query Execution Plan**. It uses the **Allocation Schema**.

-----

#### 4. Distributed Execution

The **last layer** executes the optimized plan across all sites involved. Each portion of the work at a specific location is called a **local query**. This local query is then optimized again using the local site's schema before execution.

-----

### Q 2.2: List responsibilities of Transaction Manager in Distributed Transaction Model.

The **Transaction Manager** (or Transaction Coordinator) in a distributed transaction model is responsible for coordinating the execution and termination of transactions across multiple sites to ensure that the global transaction properties are maintained.

Key responsibilities include:

  * **Transaction Demarcation:** Starting and ending (committing or rolling back) a transaction.
  * **Subtransaction Distribution:** Distributing the transaction into smaller components (**subtransactions**) and sending them to the appropriate sites for execution.
  * **Coordination of Termination:** Coordinating the final outcome of the transaction, ensuring it either commits at **all** participating sites or aborts at **all** sites (**Atomicity**).
  * **Protocol Management:** Following specific protocols, such as **Two-Phase Commit (2PC)**, to safely manage the distributed commit/abort process.
  * **Concurrency Management:** Participating in concurrency control to prevent interference when multiple transactions access shared data.
  * **Deadlock Detection/Resolution:** Identifying and resolving situations where transactions are mutually blocked from proceeding.
  * **Consistency Assurance:** Ensuring the database adheres to all rules and constraints across all sites (**Consistency**).
  * **Failure Handling/Recovery:** Logging activities for recovery purposes and managing recovery from site or network failures.

-----

### Q 2.3: Explain ACID properties.

The **ACID** properties are a set of guarantees that ensure database transactions are processed reliably, particularly ensuring **data consistency** and **reliability** even in distributed systems.

-----

#### 1. Atomicity (A)

  * **Definition:** Atomicity ensures that a transaction is treated as a single, indivisible unit of work. It guarantees that either **all** of the operation's effects are reflected in the database, or **none** of them are.
  * **Example:** In a funds transfer, both the debit from one account and the credit to another must happen entirely, or neither happens. If one part fails, the entire transaction is rolled back.
  * **Distributed Context:** Achieved using protocols like **Two-Phase Commit (2PC)** to ensure all participating nodes agree to commit or abort together.

-----

#### 2. Consistency (C)

  * **Definition:** Consistency guarantees that a transaction moves the database from one **valid state** to another. It ensures that all defined rules, constraints (like unique IDs, foreign keys), and business logic are maintained before and after the transaction.
  * **Example:** A constraint that an account balance must be non-negative must hold after the transaction completes. If the transaction violates this, it fails.
  * **Distributed Context:** Requires coordinating constraint enforcement and synchronization mechanisms across all nodes to maintain a consistent **global state**.

-----

#### 3. Isolation (I)

  * **Definition:** Isolation ensures that concurrent transactions do not interfere with each other. Each transaction operates as if it were the only one running, preventing inconsistent reads or updates from intermediate states of other transactions.
  * **Example:** If two users simultaneously attempt to book the last item in inventory, isolation ensures only one transaction successfully allocates the item, preventing double-booking.
  * **Distributed Context:** Managed through techniques like **locking** or **multi-version concurrency control (MVCC)** on individual nodes to prevent conflicts over shared data.

-----

#### 4. Durability (D)

  * **Definition:** Durability guarantees that once a transaction is successfully **committed**, its changes are permanent and survive any subsequent system failures, such as a crash or power outage.
  * **Example:** Once a payment is confirmed and committed, the updated balance will remain correct, even if the database system immediately crashes.
  * **Distributed Context:** Achieved by writing transaction changes to stable storage (like transaction logs) and **replicating** the data across multiple nodes to ensure redundancy and recovery capability.

-----

### Q 2.4: Explain 2PC in detail with a neat diagram.

The **Two-Phase Commit (2PC) protocol** is a distributed transaction protocol designed to ensure the **atomicity** of a transaction across multiple participating nodes in a distributed database system. It ensures that all participants either commit or abort the transaction collectively.

The protocol involves a **Coordinator** (usually the site where the transaction originates) and multiple **Participants** (sites where sub-transactions are executed).

-----

#### Phases of 2PC

The protocol consists of two main phases:

**Phase 1: Prepare Phase (Voting Phase)**

1.  **Preparation Request:** The **Coordinator** sends a **`Prepare T`** message to all **Participants** after the transaction's last step is reached, asking if they are ready to commit the transaction. The coordinator logs `<prepare T>` to stable storage.
2.  **Participant Vote:** Each Participant executes the transaction locally up to the commit point and decides whether it can commit.
      * **If ready to commit:** The Participant writes a **`<ready T>`** record to its local log (forcing it to stable storage) and sends a **`Vote-Commit`** (or **`ready T`**) message back to the Coordinator. This vote irrevocably obligates the site to commit if instructed.
      * **If unable to commit:** The Participant writes a **`<no T>`** record to its log and sends a **`Vote-Abort`** (or **`abort T`**) message to the Coordinator.

**Phase 2: Commit/Abort Phase (Decision Phase)**

1.  **Decision Making:** The Coordinator collects all votes.
      * **Commit Decision:** If the Coordinator receives **`Vote-Commit`** from **ALL** Participants, it decides to commit. It writes a **`<commit T>`** record to its log (forcing it to stable storage).
      * **Abort Decision:** If the Coordinator receives a **`Vote-Abort`** from **ANY** Participant (or if any Participant times out), it decides to abort. It writes an **`<abort T>`** record to its log (forcing it to stable storage).
2.  **Final Action:** The Coordinator sends the final **`Commit`** or **`Abort`** message to all Participants.
3.  **Local Finalization:** Each Participant executes the final instruction locally (commits or rolls back the transaction) and notes the final decision in its log.

#### Diagram of 2PC Protocol

This diagram illustrates the message flow:

#### Disadvantage: The Blocking Problem

The major drawback of 2PC is the potential for **blocking** in the event of a Coordinator failure, particularly after the Coordinator has received all **`ready T`** votes but before it sends the final decision.

  * If a Participant is left in the "in-doubt" state (only a `<ready T>` record exists locally), it cannot unilaterally decide to commit or abort because that might violate atomicity.
  * The Participant must wait for the Coordinator to recover, which can lead to it holding locks on resources indefinitely, blocking other transactions.

-----

### Q 2.5: Write the different parameters for measuring the cost of query.

The cost of executing a query, particularly in a distributed database system, is measured by considering the consumption of various system resources.

The different parameters for measuring query cost include:

1.  **Disk Accesses (I/O Cost):** The number of disk I/O operations is typically the main component of cost in traditional systems. A more accurate breakdown includes:

      * The **number of block transfers/reads**.
      * The **number of blocks written**.
      * The **number of seek operations** (time to position the read-write head).
      * **Rotational latency** (time to spin the data under the head).

2.  **CPU Cost (Processing Cost):** The time required for the CPU to execute the query operations. This includes:

      * The time required for **processing operations of the query** at various sites.
      * The CPU cost involved in performing various relational algebra operations.

3.  **Communication Cost (Distributed Systems):** In distributed systems, this is a major factor as operations require data exchange between sites. This includes:

      * The **time needed for exchanging data** between sites participating in the query execution (network latency).
      * The actual **amount of data being shipped** over the network.
      * The **cost of transmitting a data block** between sites.

4.  **Parallelism Gain:** This is technically a benefit, but a key factor is the **potential gain in performance from having several sites process parts of the query in parallel**. Query optimization aims to maximize this gain.

5.  **Utilization of Resources:** General utilization of various computer resources like disk space, buffer space, etc.

-----

### Q 2.6: Explain query evaluation plan.

A **query-evaluation plan** (or **query-execution plan**) is a sequence of elementary operations (called **evaluation primitives**) that can be used to explicitly evaluate a database query.

  * **Evaluation Primitive:** This is essentially a relational-algebra operation (like selection $\sigma$, projection $\Pi$, or join $\bowtie$) augmented with detailed **instructions on how to perform it**.

      * For example, an evaluation primitive for a selection operation might specify to use a particular index structure (`index 1`) to find the required tuples.

  * **Purpose:** The query optimizer typically generates multiple alternative query plans for a single query, as different plans can have vastly different execution costs. The plan chosen is the one with the lowest estimated cost.

  * **Execution:** The **query-execution engine** takes the selected query-evaluation plan, executes the sequence of specified primitive operations, and returns the final results to the user.

-----

## Module 3: Data Interoperability – XML and JSON (6 Marks)

### Q 3.1: Explain JSON Data Types. Give an example of a JSON document.

**JSON** (JavaScript Object Notation) is a lightweight, text-based data interchange format used for storing and transporting data, largely due to its human-readability and ease of machine parsing. It is language-independent.

JSON data is structured using two basic forms: **key/value pairs** (for objects) and **ordered lists of values** (for arrays).

-----

#### JSON Data Types

JSON supports the following six primitive and complex data types:

| Type | Description | Structure | Example |
| :--- | :--- | :--- | :--- |
| **1. String** | A sequence of Unicode characters enclosed in **double quotes**. | `"key": "value"` | `"name": "Alice"` |
| **2. Number** | A numeric value, represented as an **integer** or a **floating-point** number. | `"key": value` | `"age": 25` |
| **3. Object** | An **unordered** collection of **key-value pairs**, enclosed in curly braces `{}`. The key is a string. | `{"key": { ... }}` | `{"address": { "city": "Wonderland" }}` |
| **4. Array** | An **ordered** collection of values (elements), enclosed in square brackets `[]`. Values can be of any type. | `"key": [ value1, value2 ]` | `"courses": ["Math", "Science"]` |
| **5. Boolean** | A logical value, which can be either `true` or `false`. | `"key": true/false` | `"isStudent": false` |
| **6. Null** | A special value representing the absence of a value. | `"key": null` | `"graduated": null` |

-----

#### Example of a JSON Document

```json
{
  "name": "Alice",
  "age": 25,
  "isStudent": false,
  "address": {
    "street": "123 Main St",
    "city": "Wonderland",
    "zip": "12345"
  },
  "courses": ["Math", "Science", "Literature"],
  "grades": {
    "Math": 95,
    "Science": 88,
    "Literature": 92
  },
  "graduated": null
}
````

This document represents an "Alice" entity using various JSON data types.

---

### Q 3.2: Create XML document of 'Restaurant Menu Card' has food items, categorized into Starters, Drinks and Main Course. Each food item element contains name, cost, calories and veg/non-veg flag. Write DTD rules for the above XML Document.

#### 1. XML Document (`menu.xml`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE menu SYSTEM "menu.dtd">
<menu>
  <starters>
    <item>
      <name>Spring Rolls</name>
      <cost>5.99</cost>
      <calories>250</calories>
      <veg>true</veg>
    </item>
    <item>
      <name>Chicken Wings</name>
      <cost>8.99</cost>
      <calories>300</calories>
      <veg>false</veg>
    </item>
  </starters>
  <drinks>
    <item>
      <name>Orange Juice</name>
      <cost>2.49</cost>
      <calories>120</calories>
      <veg>true</veg>
    </item>
  </drinks>
  <maincourse>
    <item>
      <name>Veg Pizza</name>
      <cost>12.99</cost>
      <calories>800</calories>
      <veg>true</veg>
    </item>
    <item>
      <name>Grilled Chicken</name>
      <cost>14.99</cost>
      <calories>450</calories>
      <veg>false</veg>
    </item>
  </maincourse>
</menu>
```

*Note: The XML structure provided in the source uses nested elements to represent attributes. The source also places a DTD attribute `type` on the `<veg>` element to enforce `true`/`false` values. The above XML simplifies to elements for easier DTD creation, following the structure provided in the source materials.*

---

#### 2. DTD Rules (`menu.dtd`)

A **Document Type Definition (DTD)** defines the legal building blocks and structure of an XML document.

```dtd
<!ELEMENT menu (starters, drinks, maincourse)>

<!ELEMENT starters (item+)>
<!ELEMENT drinks (item+)>
<!ELEMENT maincourse (item+)>

<!ELEMENT item (name, cost, calories, veg)>

<!ELEMENT name (#PCDATA)>
<!ELEMENT cost (#PCDATA)>
<!ELEMENT calories (#PCDATA)>

<!ELEMENT veg (#PCDATA)>

<!ATTLIST veg type (true | false) "true">
```

*Note: The `+` indicates one or more occurrences. `#PCDATA` stands for Parsed Character Data (text).*

---

### Q 3.3: Write XML schema for 'Restaurant menu card' has food items categorized into starters, drinks Chinese south and Punjabi. Each food item element contains name, cost, calories and veg/non-veg flag.

An **XML Schema** is a powerful alternative to DTD that is itself written in XML and allows for strong **typing** and more detailed constraints.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">

  <xs:complexType name="FoodItemType">
    <xs:sequence>
      <xs:element name="name" type="xs:string"/>
      <xs:element name="cost" type="xs:decimal"/>
      <xs:element name="calories" type="xs:positiveInteger"/>
      <xs:element name="veg_non-veg_flag" type="xs:string"/>
    </xs:sequence>
  </xs:complexType>

  <xs:element name="Food_items">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="starters" type="FoodItemType" maxOccurs="unbounded" minOccurs="0"/>

        <xs:element name="drinks" type="FoodItemType" maxOccurs="unbounded" minOccurs="0"/>

        <xs:element name="chinese" type="FoodItemType" maxOccurs="unbounded" minOccurs="0"/>
        
        <xs:element name="south" type="FoodItemType" maxOccurs="unbounded" minOccurs="0"/>
        
        <xs:element name="punjabi" type="FoodItemType" maxOccurs="unbounded" minOccurs="0"/>

        <xs:element name="Food_items_Detail">
          <xs:complexType>
            <xs:sequence>
              <xs:element name="Food_item" type="FoodItemType"/> 
            </xs:sequence>
          </xs:complexType>
        </xs:element>

      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

*Note: The structure provided in the original sources for this specific question is highly redundant and inconsistent. This answer uses an improved, modular `FoodItemType` but retains the required element names (starters, drinks, chinese, south, punjabi, cost, calories, etc.) and types (string, decimal, positiveInteger) as requested/demonstrated in the provided material, setting `maxOccurs="unbounded"` to allow multiple items in each category.*

---

### Q 3.4: Explain XML Schema document with example.

**XML (Extensible Markup Language)** is a text-based markup language derived from SGML, primarily used to store and transport data, not for presentation like HTML.

An **XML Schema** is a formal method for defining the structure, content, and data types of elements and attributes within an XML document.

#### Key Features and Role

* **Structure and Content Definition:** It strictly defines which elements are allowed, how they are nested, and how many times they can occur (e.g., `minOccurs`, `maxOccurs`).
* **Strong Typing:** Unlike DTDs where all values are treated as strings, XML Schema supports specific data types like `xs:string`, `xs:decimal`, `xs:positiveInteger`, etc. This ensures data validity and accuracy.
* **XML Syntax:** The schema document itself is written using XML syntax, making it consistent with the format it validates and easier for XML-based tools to process.
* **Namespaces:** It integrates with XML Namespaces, allowing complex documents to safely combine elements from different sources/vocabularies.

#### Example: XML Schema for a Simple Bank Account

This schema defines a single `bank` element that contains one or more `account` elements. Each account must have an `account-number`, a `branch-name`, and a `balance` with specific data types.

```xml
<xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  
  <xsd:element name="bank" type="BankType"/>
  
  <xsd:element name="account">
    <xsd:complexType>
      <xsd:sequence>
        <xsd:element name="account-number" type="xsd:string"/>
        <xsd:element name="branch-name" type="xsd:string"/>
        <xsd:element name="balance" type="xsd:decimal"/> 
      </xsd:sequence>
    </xsd:complexType>
  </xsd:element>

  <xsd:complexType name="BankType">
    <xsd:sequence>
      <xsd:element ref="account" minOccurs="0" maxOccurs="unbounded"/>
    </xsd:sequence>
  </xsd:complexType>
</xsd:schema>
```

*Note: The namespace `xsd:schema` references the XML Schema definition itself, which is the standard mechanism for declaring schema components.*

---

## Module 4: NoSQL Distribution Model (10 Marks)

### Q 4.1: Compare between SQL and NoSQL

The comparison between traditional **SQL (Relational) Databases** and **NoSQL (Non-Relational) Databases** highlights differences in data model, schema, scalability, and consistency philosophy.

| Feature         | SQL (RDBMS)                                                                                            | NoSQL (Non-Relational)                                                                                                        |
| :-------------- | :----------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **Type/Model**  | Relational Database Management System (RDBMS) / Table-based.                                           | Non-relational or Distributed Database. Models: Key-Value, Document, Column-Family, Graph.                                    |
| **Schema**      | **Pre-defined/Rigid Schema**. Requires tables and data structure to be defined upfront.                | **Dynamic/Flexible Schema** (Schema-less). Allows for heterogeneous data structures.                                          |
| **Language**    | Uses **Structured Query Language (SQL)** for CRUD operations.                                          | Varies by DB (e.g., JSON query, Cypher, CQL). Often uses implicit query methods.                                              |
| **Scalability** | **Vertical Scaling**: Increasing capacity on a single server (e.g., more RAM, CPU).                    | **Horizontal Scaling (Scaling Out)**: Adding more commodity servers/nodes to a cluster (e.g., Sharding).                      |
| **Consistency** | Emphasizes **ACID** properties (Atomicity, Consistency, Isolation, Durability) for strong consistency. | Focuses on **CAP theorem** (Consistency, Availability, Partition Tolerance) for relaxed/eventual consistency.                 |
| **Joins**       | Excellent support for **Joins** across normalized tables.                                              | Generally **No support for Joins**; relationships are managed by embedding/nesting data within documents.                     |
| **Best Suited** | Complex queries and transactions where data integrity is paramount (e.g., banking).                    | Hierarchical, voluminous, or rapidly changing data, and for modern web/mobile applications (e.g., social networks, big data). |

---

### Q 4.2: Explain Replication and sharding in NoSQL.

**Replication** and **Sharding** are essential distribution models in NoSQL databases, used together or separately, to achieve scalability, fault tolerance, and high availability.

#### 1. Replication

Replication is the process of creating and maintaining **multiple identical copies of data** (replicas) across different nodes in a distributed system.

* **Primary Goal:** **High Availability** and **Fault Tolerance** (data redundancy). If one node fails, others can seamlessly take over serving requests.
* **Types of Replication:**

  * **Master-Slave (Primary-Secondary):** One node (the **master/primary**) handles all writes/updates, and the other nodes (**slaves/secondaries**) replicate the data and typically handle read requests. This helps with **read scalability** but the master is a bottleneck for writes.
  * **Peer-to-Peer:** All nodes are equal and can accept both reads and **writes**. Nodes must coordinate to synchronize changes and resolve conflicts. This offers better write scalability and resilience (no single point of failure) but introduces complexity in maintaining consistency.

#### 2. Sharding

Sharding is the process of splitting a large dataset (or table) into smaller, unique pieces called **shards** (or partitions), and distributing these shards across multiple physical servers.

* **Primary Goal:** **Horizontal Scalability** and **Performance** by spreading data and processing load across multiple servers.
* **Mechanism:** Data is partitioned based on a **shard key** (e.g., customer ID or geographic region), which determines which server a specific piece of data resides on.
* **Types of Sharding:**

  * **Horizontal Sharding:** Divides the data by **rows** (e.g., different users/regions go to different shards).
  * **Vertical Sharding:** Divides the data by **columns** (e.g., profile info on one shard, order history on another).

#### Combining Replication and Sharding

They are often used together:

* Data is first **sharded** to distribute the load and handle sheer data volume (horizontal scaling).
* Each resulting shard is then **replicated** across multiple nodes (often using a master-slave replica set in MongoDB) for fault tolerance and high availability.

---

### Q 4.3: Explain CAP Theorem and NoSQL Databases.

The **CAP Theorem** (or Brewer's Theorem) is a fundamental principle in distributed computing stating that it is **impossible** for any distributed data system to simultaneously guarantee all three properties: **Consistency, Availability, and Partition Tolerance**.

---

#### The Three Properties

1. **Consistency (C):** Every read operation receives the most recent write. All nodes have the same data at the same time.
2. **Availability (A):** Every request receives a response (success or failure) in a reasonable amount of time, even if some nodes are down or slow. The system is always operational and responsive.
3. **Partition Tolerance (P):** The system continues to operate despite arbitrary network failures that lead to a "split-brain" scenario, where parts of the system cannot communicate with others. **In a large-scale distributed system, partition tolerance is virtually mandatory.**

#### The Trade-Offs

Since only two can be guaranteed, distributed systems must choose a combination to prioritize:

* **CP (Consistency + Partition Tolerance):** Prioritizes consistency. If a network partition occurs, the system must choose to block or deny requests to the minority partition until the partition heals, thus sacrificing availability to ensure correctness.

  * *Example NoSQL:* **HBase**, **MongoDB** (by default/configurable).
* **AP (Availability + Partition Tolerance):** Prioritizes availability. If a partition occurs, the system continues to process requests on all sides of the partition, allowing data divergence (stale reads). It sacrifices strong consistency for continuous operation.

  * *Example NoSQL:* **Cassandra**, **Riak**, **CouchDB** (eventual consistency).

---

#### CAP and NoSQL Design

NoSQL databases often choose **P** (Partition Tolerance) to enable massive horizontal scaling. Their core design reflects a deliberate tradeoff:

* **CP NoSQL** (e.g., MongoDB, HBase) are often favored for applications where data integrity is critical, and brief downtime is acceptable (e.g., ledger systems). They lean towards the **ACID** model.
* **AP NoSQL** (e.g., Cassandra, Riak) are favored for high-throughput applications where continuous operation is vital and temporary inconsistency is tolerable (e.g., social media feeds, IOT logging). They often adopt the **BASE** model.

---

### Q 4.4: Explain benefits of NoSQL.

NoSQL databases offer several advantages over traditional RDBMS, making them essential for modern, high-volume, and agile applications.

* **Scalability (Horizontal Scaling):** NoSQL databases are built to scale **horizontally** by simply adding more commodity servers to a cluster (**scaling out**). This allows them to handle massive volumes of data and traffic far more cost-effectively than vertically scaling a single RDBMS server.
* **Schema Flexibility/Agility:** They are **schema-less** or have a flexible schema. This eliminates the need for up-front schema design and allows developers to quickly integrate new features, data types, or fields without complex database migrations, accelerating the development cycle (Agility).
* **High Performance (Optimized Queries):** NoSQL models (like Key-Value and Document) are optimized for extremely fast reads and writes for specific query patterns. By storing complex, nested data in a single document (avoiding joins), query latency is often much lower than in a relational database.
* **Support for Diverse Data Models:** They support multiple data models (Key-Value, Document, Column-Family, Graph), allowing developers to choose the best fit for their specific data and query needs (**Polyglot Persistence**).
* **Handling Unstructured Data (Big Data):** Their flexible nature makes them highly suitable for managing and processing **Big Data** and real-time web applications that often deal with vast amounts of unstructured or semi-structured data.
* **Lower Cost (Commodity Hardware):** Horizontal scaling relies on distributing the load across many low-cost, commodity servers, which is generally more cost-effective than investing in a single, expensive, high-end server.

---

### Q 4.5: Explain different types of NoSQL database.

NoSQL databases are broadly classified into four major data models, each optimized for different data structures and access patterns.

| Type                       | Description                                                                                                                                                     | Structure / Data Model                                                                                                                            | Common Examples                    |
| :------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------- |
| **1. Key-Value Store**     | The simplest model, acting like a giant distributed **hash table**. Data is stored and retrieved solely based on a unique key.                                  | `Key` maps directly to a `Value`, where the value is treated as an opaque blob (string, JSON, BLOB).                                              | Redis, DynamoDB, Riak.             |
| **2. Document Database**   | Stores and retrieves data as **documents**, typically in formats like **JSON** or XML. These documents contain nested, self-describing data structures.         | Stores **documents** (like a JSON object) where the document itself is the logical unit of storage and retrieval, replacing the concept of a row. | MongoDB, CouchDB.                  |
| **3. Column-Family Store** | Also known as Wide-Column stores. They group related data into **column families**. Each row can have different columns, which are stored contiguously on disk. | Uses a 2D map structure: `Key` maps to `Column Families`, and families contain arbitrary columns with values and timestamps.                      | Cassandra, HBase, Google BigTable. |
| **4. Graph Database**      | Stores data entities and the relationships between them. Highly optimized for rapidly traversing complex connections.                                           | Data is modeled as **Nodes** (entities/objects) and **Edges** (relationships/links). Nodes and edges can have **Properties** (key-value pairs).   | Neo4j, InfiniteGraph, OrientDB.    |

---

### Q 4.6: Explain BASE Model.

The **BASE** acronym describes the properties of systems (primarily some NoSQL databases) that relax the strict ACID properties to achieve better horizontal scalability and availability.

BASE is an opposing philosophy to ACID, where consistency is sacrificed for availability and performance.

---

#### BASE Properties

1. **Basically Available (BA):** The system guarantees to be available to respond to any incoming request. Every non-failing node can return a response, even if it's a failure message, rather than simply blocking until consistency is achieved. This is achieved by relying on data replication.
2. **Soft State (S):** The system's state may change over time, even without external input, because consistency is not enforced immediately. The lack of immediate consistency means the data values may temporarily change.
3. **Eventual Consistency (E):** If no further updates occur on a given data item, all copies of the data will eventually converge to the same consistent value. The system does not guarantee when this synchronization will happen, but only that it *will* eventually occur.

#### Comparison to ACID

| Criteria        | ACID (SQL)                                         | BASE (NoSQL)                            |
| :-------------- | :------------------------------------------------- | :-------------------------------------- |
| **Focus**       | Strong **Consistency** and transaction durability. | High **Availability** and partitioning. |
| **Consistency** | **Strong** (immediate consistency).                | **Weak/Loose** (eventual consistency).  |
| **Scaling**     | Vertical.                                          | Horizontal.                             |

---

## Module 5: NoSQL using MongoDB (6 Marks)

### Q 5.1: Explain MongoDB CRUD Operations.

**CRUD** stands for **Create, Read, Update, and Delete**—the four basic operations for managing persistent storage in any database system. MongoDB, a Document Database, uses a flexible structure that operates on **documents** within **collections**.

| Operation                | Description                                                                                     | MongoDB Methods (Shell)                                                                      | Example                                                              |
| :----------------------- | :---------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------- | :------------------------------------------------------------------- |
| **1. Create (Insert)**   | Used to insert new documents into a collection. If the collection doesn't exist, it is created. | `db.collection.insertOne(<document>)` `db.collection.insertMany([<doc1>, <doc2>])`           | `db.students.insertOne({name: "Sumit", age: 20})`                    |
| **2. Read (Query/Find)** | Used to retrieve documents from a collection that match a specified query condition.            | `db.collection.find(<query>)` `db.collection.find().pretty()` (for formatted output)         | `db.students.find({age: {$gt: 20}})` (Find documents where age > 20) |
| **3. Update**            | Used to modify existing documents in a collection based on a specified selection criterion.     | `db.collection.updateOne(<filter>, <update>)` `db.collection.updateMany(<filter>, <update>)` | `db.students.updateOne({name: "Sumit"}, {$set: {age: 24}})`          |
| **4. Delete (Remove)**   | Used to permanently remove documents from a collection based on specified criteria.             | `db.collection.deleteOne(<filter>)` `db.collection.deleteMany(<filter>)`                     | `db.students.deleteOne({name: "Sumit"})`                             |

---

### Q 5.2: Explain MongoDB Sharding.

**Sharding** in MongoDB is the process of distributing data records across multiple machines (servers) to support massive data growth and high throughput requirements. It is MongoDB's core strategy for achieving **horizontal scaling**.

#### Why Sharding?

A single server eventually hits limitations:

* Insufficient memory/RAM for the active dataset (stresses I/O).
* High query loads can max out the CPU.
* Vertical scaling (buying bigger hardware) is too expensive and eventually reaches its limit.
* In replication alone, all writes are concentrated on the master node, creating a bottleneck.

Sharding solves this by breaking the database into small, independent parts across a cluster.

#### Architecture (Sharded Cluster)

A MongoDB sharded cluster consists of three main components:

1. **Shards (Data Servers):** These are the servers (typically deployed as replica sets for high availability) that store the actual data. The data is partitioned, and each shard holds only a **subset** of the cluster's entire dataset.
2. **Config Servers (Metadata):** These servers store the cluster's **metadata**, which includes the mapping of the dataset (chunks) to the respective shards. This information is critical for routing queries.
3. **Query Routers (`mongos`):** These are MongoDB instances that act as the interface between client applications and the sharded cluster. The `mongos` determines which shard has the required data and routes the query accordingly, aggregating results before returning them to the client.

#### Sharding Process

1. **Shard Key Selection:** A field (e.g., `user_id`, `zip_code`) is chosen as the **Shard Key**. MongoDB uses the value of this key to determine how documents are partitioned and distributed.
2. **Chunking:** The data is logically divided into smaller ranges based on the shard key, known as **chunks**.
3. **Data Distribution:** These chunks are then distributed and balanced across the various shards in the cluster.

---

### Q 5.3: Mention datatypes in MongoDB which function is used to save and update document in database.

#### 1. MongoDB Data Types

MongoDB stores data in **BSON** (Binary JSON) format, which is an extension of JSON and supports more data types. The following are basic data types used in MongoDB:

* **String:** Most common type, UTF-8 encoded.
* **Integer (32-bit or 64-bit):** Used for whole number values.
* **Double:** Used for floating-point values.
* **Boolean:** Stores `true` or `false`.
* **Null:** Stores a `null` value (absence of a value).
* **Array:** An ordered collection of values, enclosed in `[]`. Can store values of different types.
* **Object:** Used to store **Embedded Documents** (nested documents).
* **ObjectID:** A unique 12-byte identifier automatically generated for each document in the `_id` field.
* **Date:** Stores the current date and time (as a 64-bit integer, often UTC datetime).

#### 2. Functions Used to Save and Update Documents

MongoDB provides several methods for saving new documents and updating existing ones:

| Function                                                | Purpose                                                                                                                                                         |
| :------------------------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`db.collection.save(<document>)`**                    | A legacy shell method. It either **inserts** a new document (if the `_id` field is new or missing) or **replaces** an existing document (if the `_id` matches). |
| **`db.collection.updateOne(<filter>, <update>)`**       | Updates the values in a **single** document that matches the given `filter` criteria.                                                                           |
| **`db.collection.updateMany(<filter>, <update>)`**      | Updates the values in **multiple** documents that match the given `filter` criteria.                                                                            |
| **`db.collection.replaceOne(<filter>, <replacement>)`** | **Replaces** a single document entirely with the new document provided (except for the `_id` field).                                                            |

---

## Module 6: Trends in Advance Databases (6 Marks)

### Q 6.1: What are the features of Graph database?

A **Graph Database** stores data using a graph structure of nodes, relationships (edges), and properties, specializing in managing and querying highly connected data.

| Feature                               | Description                                                                                                                                                  | Example/Benefit                                                                                             |
| :------------------------------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------- |
| **1. Nodes and Edges**                | Data is composed of **Nodes** (entities like `Person`, `Product`) and **Edges** (**Relationships**) that link the nodes (like `KNOWS`, `BOUGHT`).            | Relationships are stored directly with the data, allowing for fast traversal of complex networks.           |
| **2. Properties**                     | Both Nodes and Edges can store descriptive information as **key-value pairs** (**Properties**).                                                              | Nodes can store `name: John, age: 27`. Edges can store `since: 2013` (on a `IS_FRIENDS_WITH` relationship). |
| **3. Flexible Structure (Schema)**    | There are no fixed tables or schemas. The structure can be easily changed over time by adding or modifying nodes, relationships, and properties.             | Allows for quick, agile development and evolution of the data model.                                        |
| **4. Relationship Speed (Traversal)** | Designed for **traversal** (following edges) rather than joins, making queries for interconnected data (e.g., shortest path, common friends) extremely fast. | Unlike RDBMS, it does **NOT** require complex **joins** to retrieve related data.                           |
| **5. Query Language**                 | Uses specialized query languages like **Cypher** (in Neo4j) which are declarative and visually represent the graph structure.                                | Makes complex queries that span multiple relationships much simpler to write and understand.                |

---

### Q 6.2: Explain Temporal database.

A **Temporal Database** is a database with built-in support for handling **time-sensitive data**—information about the past, present, and future states of the real world.

Traditional databases typically only store the current state, updating and overwriting old values. Temporal databases keep the historical context.

---

#### Temporal Aspects (Dimensions of Time)

Temporal databases typically record two key dimensions of time for a fact or a tuple:

1. **Valid Time:** The period during which a fact is **true in the real world**, regardless of when the data was recorded in the database.

   * *Example:* John started living in Mumbai on **June 21, 2015** (Valid Start Time), even if the update was entered later.

2. **Transaction Time:** The period during which a fact is **stored and considered current within the database system**. This is typically a timestamp generated automatically by the system.

   * *Example:* The record of John's move was entered into the database on **January 10, 2016** (Transaction Entered Time).

#### Types of Temporal Relations

* **Uni-Temporal Relation:** Stores either Valid Time **or** Transaction Time.
* **Bi-Temporal Relation:** Stores **both** Valid Time and Transaction Time (e.g., Valid From/Till and Transaction Entered/Superseded).

#### Advantages

* **Historical Information:** Allows queries to retrieve historical data (e.g., "What was John's salary in 2001?") using the **Valid Time** dimension.
* **Rollback Information:** Allows rollbacks to a previous database state using the **Transaction Time** dimension.
* **Applications:** Essential for financial systems, medical records, and inventory management, where tracking changes over time is crucial.

---

### Q 6.3: What is a graph database?

A **Graph Database** is a type of NoSQL database that is optimized to store, manage, and query data that is highly connected. It models data based on graph theory concepts.

#### Key Components:

1. **Nodes:** These represent the fundamental data entities or objects to be tracked (e.g., a Person, a Book, a Location).
2. **Edges (Relationships):** These are the connections or links between the nodes. They always have a direction (unidirectional or bidirectional) and define the association between two nodes (e.g., `IS_FRIENDS_WITH`, `HAS_READ`).
3. **Properties:** These are key-value pairs that store descriptive attributes for both nodes and edges (e.g., a node `Person` has property `age: 27`; an edge `HAS_READ` has property `rated: 5`).

#### Why Use It?

* **Intuitive Modeling:** Data is stored like sketching ideas on a whiteboard, without constraining it to a pre-defined table structure.
* **Fast Traversal:** Finding connections (paths) between entities is fast because relationships are physically stored as pointers, eliminating the slow join operations required by RDBMS.
* **Best for Connected Data:** Excellent for use cases like social networks, recommendation engines, and fraud detection, where the connections are the most important part of the data.

---

### Q 6.4: Explain Spatial database.

A **Spatial Database** (or **Geographic Information System - GIS**) is a database optimized to store, query, and manage data representing objects defined in a geographic or geometric space, such as points, lines, and polygons. These databases are fundamental for geographical information, mapping, and location-based services.

---

#### Spatial Data Types

The basic building blocks for representing geometric information:

* **Point:** Represents a single location defined by coordinates (e.g., latitude, longitude).
* **Line:** Represents a sequence of connected points forming a path or boundary (e.g., a road, a river).
* **Polygon:** Represents a closed shape used for areas (e.g., countries, lakes, buildings).

  * *Note: Data can be represented in **Vector data** (points, lines, polygons) or **Raster data** (a matrix of cells/pixels) formats.*

#### Core Components

1. **Spatial Indexing:** Uses specialized indexing techniques like **R-trees** or **Quad-trees** to efficiently organize and locate spatial data, vastly improving query speed.
2. **Spatial Query Language:** Extends standard SQL with geospatial functions to perform operations based on spatial criteria.
3. **Geospatial Functions (Operators):** Functions to perform geometric and topological calculations:

   * **Metric:** Calculate `Distance` or `Length`.
   * **Topological:** Check for relationships like `Intersects`, `Contains`, or `Overlaps`.

#### Applications

Spatial databases are crucial for urban planning, vehicle navigation systems (GPS), environmental monitoring, and logistics.
