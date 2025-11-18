# **DWM Question Bank (Solved Examples)** 

 This note contains a set of questions and answers based on the provided DWM question bank, including specific numerical examples.
 
 ### **Module 1: Data Warehousing Fundamentals**
 
 #### **1\. Design Star and Snowflake schema for a given scenario.**
 
 **Scenario:** Consider a data warehouse for hotel occupancy, where there are four dimensions (Hotel, Room, Time, Customer) and two measures (Occupied rooms, Vacant rooms).
 
 **Answer:**
 
 **Information Package Diagram:** This diagram maps the business dimensions and facts.
 
 **Star Schema:** A central fact table is connected directly to each dimension table. The dimension tables are denormalized.
 
 **Snowflake Schema:** The dimension tables are normalized. For example, the `Hotel` dimension might be snowflaked to separate `City` information.
 
 #### **2\. Differentiate between Star schema and Snowflake schema.**
 
 | Feature | Star Schema | Snowflake Schema |
 | --- | --- | --- |
 | **Structure** | Central fact table linked to denormalized dimension tables. | Central fact table linked to normalized dimension tables. |
 | **Complexity** | Simple, easier to understand. | More complex due to normalization. |
 | **Normalization** | Dimension tables are typically denormalized. | Dimension tables are normalized. |
 | **Query Performance** | Faster due to fewer joins. | Slower, as more joins are required. |
 | **Storage Requirement** | Higher, due to redundancy in data. | Lower, as data redundancy is minimized. |
 | **Use Case** | Suitable for small to medium data models. | Suitable for complex or large data models. |
 
 #### **3\. Compare OLTP and OLAP.**
 
 | Feature | OLTP (Online Transaction Processing) | OLAP (Online Analytical Processing) |
 | --- | --- | --- |
 | **Characteristic** | Operational processing | Informational processing |
 | **Orientation** | Transaction | Analysis |
 | **Primary Users** | Clerks, DBAs, database professionals | Knowledge workers (e.g., managers, executives, analysts) |
 | **Purpose** | Day-to-day operations | Long-term decision-making and information support |
 | **Database Design** | ER-based, application-oriented | Star/snowflake schema, subject-oriented |
 | **Data** | Current, always up-to-date | Historical, maintains accuracy over time |
 | **View** | Detailed, flat relational structure | Summarized, multidimensional |
 | **Unit of Work** | Short, simple transactions | Complex queries |
 | **Access Pattern** | Read/write | Primarily read |
 | **Database Size** | 100 MB to GB | 100 GB to TB |
 | **Performance Priority** | High performance, high availability | High flexibility, end-user autonomy |
 | **Key Metric** | Transaction throughput | Query throughput, response time |
 
 #### **4\. Perform OLAP operations.**
 
 **Scenario:** Given dimensions `Doctor`, `Patient`, `Time` and a fact table `(DID, PID, TID, count, charge)`.
 
 *   **Roll-Up:** Aggregate data by climbing up a hierarchy.
     
     *   _Scenario:_ Roll-up from `Day` level to `Month` level.
         
     *   _Result:_ `(DID, Month, TotalCharge)`
         
 *   **Drill-Down:** Navigate from less detailed data to more detailed data.
     
     *   _Scenario:_ Drill-down from `Quarter` to `Month`.
         
     *   _Result:_ `(DID, Quarter, Month, TotalCharge)`
         
 *   **Slice:** Select a single value for one dimension.
     
     *   _Scenario:_ Slice to view data for `Year = 2023` only.
         
     *   _Result:_ A new cube showing data only for 2023.
         
 *   **Dice:** Select a sub-cube by specifying a range or multiple values for two or more dimensions.
     
     *   _Scenario:_ Dice to view data for `Quarter = 1` AND `Doctor Location = New York`.
         
     *   _Result:_ A sub-cube showing data for Q1 in New York only.
         
 *   **Pivot:** Rotate the data axes to view the data from a different perspective.
     
     *   _Scenario:_ Pivot to swap `Doctor` and `Patient` dimensions.
         
     *   _Result:_ A table with `Patient Name` as rows and `Doctor Name` as columns.
         
 
 #### **5\. Differentiate between ER Modelling vs Dimensional modelling.**
 
 | Aspect | ER Modeling | Dimensional Modeling |
 | --- | --- | --- |
 | **Purpose** | Focuses on capturing detailed transactional data. | Optimized for analytical and reporting purposes. |
 | **Structure** | Complex with many entities and relationships. | Simpler with fact and dimension tables. |
 | **Normalization** | Highly normalized to eliminate redundancy. | Denormalized to improve query performance. |
 | **Schema Type** | Relational Schema (e.g., 3NF). | Star Schema or Snowflake Schema. |
 | **Usage** | Used in OLTP systems. | Used in OLAP systems (data warehouses). |
 | **Focus** | Entity relationships and operational details. | Facts (measures) and dimensions (context). |
 | **Query Performance** | Slower for analytical queries due to many joins. | Faster for analytical queries due to fewer joins. |
 | **Data Redundancy** | Minimal (due to normalization). | Higher (due to denormalization). |
 
 #### **6\. Illustrate major steps in ETL process.**
 
 ETL (Extract, Transform, Load) is the process of collecting data from multiple sources and moving it to a unified data store like a data warehouse.
 
 1.  **Extract:**
     
     *   **Purpose:** Retrieve data from various sources (e.g., databases, files, APIs).
         
     *   **Activities:** Identifying data sources, extracting relevant data, handling different formats (structured, unstructured).
         
 2.  **Transform:**
     
     *   **Purpose:** Convert and clean the extracted data into a suitable format.
         
     *   **Activities:**
         
         *   **Data Cleaning:** Handling missing values, removing errors, resolving inconsistencies.
             
         *   **Data Integration:** Combining data from multiple sources.
             
         *   **Data Transformation:** Normalizing data, aggregating data, applying business rules.
             
 3.  **Load:**
     
     *   **Purpose:** Store the transformed data into the target system (e.g., data warehouse).
         
     *   **Activities:** Choosing between full load (all data) or incremental load (only changes), validating data, optimizing the load process.
         
 
 #### **7\. Write a short note on: Techniques of data loading.**
 
 Data loading is the "Load" phase of ETL. The two primary techniques are:
 
 1.  **Full Load:**
     
     *   Involves completely erasing (truncating) the existing data in the target table and reloading it entirely with new data from the source.
         
     *   **Pros:** Simple to implement.
         
     *   **Cons:** Time-consuming and resource-intensive for large datasets.
         
     *   **Use Case:** Typically used for the initial load of a warehouse or for very small dimension tables.
         
 2.  **Incremental Load:**
     
     *   Involves loading only the _new_ or _changed_ records since the last load. This requires a mechanism to detect changes (like timestamps or change data capture).
         
     *   **Pros:** Much faster and more efficient than a full load.
         
     *   **Cons:** More complex to implement as it requires change detection.
         
     *   **Use Case:** The standard method for loading large fact tables and updating dimension tables.
         
 
 ### **Module 2: Introduction to Data Mining, Exploration & Pre-processing**
 
 #### **1\. Describe any 5 issues in data mining.**
 
 1.  **Mining Methodology Issues:** Handling different types of knowledge, mining at multiple levels of abstraction, and incorporating background knowledge.
     
 2.  **User Interaction Issues:** Developing data mining query languages, and effectively presenting and visualizing results.
     
 3.  **Performance Issues:** Ensuring the efficiency and scalability of algorithms for massive datasets; developing parallel, distributed, and incremental algorithms.
     
 4.  **Data Source Issues (Diverse Data Types):** Handling relational and complex data types (e.g., spatial, multimedia, text).
     
 5.  **Data Quality Issues:** Handling noisy, incomplete, and inconsistent data.
     
 
 #### **2\. Explain KDD process with neat diagram.**
 
 KDD (Knowledge Discovery in Databases) is the overall process of finding and interpreting patterns from data.
 
 1.  **Data Cleaning:** Removal of noise and inconsistent data.
     
 2.  **Data Integration:** Combining data from multiple sources.
     
 3.  **Data Selection:** Retrieving data relevant to the analysis task.
     
 4.  **Data Transformation:** Consolidating data into forms appropriate for mining (e.g., aggregation).
     
 5.  **Data Mining:** The core step where intelligent methods are applied to extract patterns.
     
 6.  **Pattern Evaluation:** Identifying the truly interesting patterns (knowledge) based on interestingness measures.
     
 7.  **Knowledge Presentation:** Using visualization and knowledge representation techniques to present the mined knowledge to the user.
     
 
 #### **3\. Explain different data visualization techniques.**
 
 1.  **Pixel-oriented:** Maps each data value to a colored pixel. m dimensions are shown in m windows.
     
 2.  **Geometric Projection:** Visualizes data as geometric figures.
     
     *   **Scatterplot Matrices:** A grid of scatterplots showing relationships between all possible pairs of variables.
         
     *   **Parallel Coordinates:** Uses parallel axes for each dimension. A data record is a line connecting its values on each axis.
         
 3.  **Icon-Based:** Maps data values to features of icons.
     
     *   **Chernoff Faces:** Maps data to facial features (eye size, nose length, etc.).
         
     *   **Stick Figures:** Maps dimensions to the angle and/or length of limbs on a stick figure.
         
 4.  **Hierarchical:** Subdivides the n-dimensional space and presents subspaces.
     
     *   **Tree Maps:** Shows hierarchical data as nested rectangles.
         
     *   **Worlds Within Worlds:** Visualizes nested 3D spaces.
         
 
 #### **4\. Explain data preprocessing and its steps.**
 
 Data preprocessing transforms raw data into a clean, usable format for mining.
 
 1.  **Data Cleaning:**
     
     *   **Objective:** Handle missing, noisy, and inconsistent data.
         
     *   **Techniques:** Fill missing values (using mean, median, etc.), smooth noise (using binning, regression), resolve inconsistencies.
         
 2.  **Data Integration:**
     
     *   **Objective:** Combine data from multiple sources.
         
     *   **Techniques:** Schema integration, entity matching, resolving data conflicts.
         
 3.  **Data Transformation:**
     
     *   **Objective:** Convert data into a format suitable for analysis.
         
     *   **Techniques:** Normalization (scaling data), Aggregation (summarizing), Encoding (categorical to numeric).
         
 4.  **Data Reduction:**
     
     *   **Objective:** Reduce the dataset size while retaining key information.
         
     *   **Techniques:** Sampling (selecting a subset), Feature Selection (choosing relevant attributes).
         
 
 #### **5\. State any five applications of data mining.**
 
 1.  **Market Basket Analysis:** Used in retail to identify products frequently bought together (e.g., {Bread} -\ {Butter}), helping with product placement and promotions.
     
 2.  **Fraud Detection:** Identifies unusual patterns in transactions (e.g., credit card fraud) by detecting anomalies.
     
 3.  **Healthcare and Medicine:** Assists in diagnosing diseases by analyzing patient records and predicting illnesses based on historical data.
     
 4.  **Customer Relationship Management (CRM):** Helps companies understand customer behavior, segment customers (clustering), and predict customer churn.
     
 5.  **Web Mining:** Used by search engines (like PageRank) to rank web pages and by e-commerce sites for recommendation systems.
     
 
 #### **6\. Describe various methods for handling the problem of missing values in attributes.**
 
 1.  **Ignore the Tuple:** Skip the entire record (row) if it has a missing value. Not effective if many tuples have missing data.
     
 2.  **Fill in Missing Values Manually:** Effective for small datasets, but not feasible for large ones.
     
 3.  **Use a Global Constant:** Replace all missing values with a constant string like "Unknown" or "N/A".
     
 4.  **Use Central Tendency:** Replace missing values with the attribute's mean (for symmetric data) or median (for skewed data).
     
 5.  **Use Class-Specific Central Tendency:** Use the mean or median of the attribute, but only for records belonging to the same class.
     
 6.  **Use the Most Probable Value:** Predict the missing value using a data mining algorithm like regression or a decision tree.
     
 
 #### **7\. Explain in brief what is data discretization and concept hierarchy generation.**
 
 *   **Data Discretization:**
     
     *   **Definition:** The process of converting continuous numerical data (e.g., Age: 25.7) into a finite set of discrete, categorical intervals or "bins" (e.g., Age: "Young Adult").
         
     *   **Purpose:** Simplifies data and is required by some data mining algorithms that only work with categorical data.
         
     *   **Example:** Discretizing "Age" into "Child" (0-18), "Young Adult" (19-35), "Adult" (36-60), and "Senior" (61+).
         
 *   **Concept Hierarchy Generation:**
     
     *   **Definition:** Organizing data into multiple levels of abstraction, from the most specific (lowest level) to the most general (highest level).
         
     *   **Purpose:** Allows for data analysis at varying levels of granularity (e.g., "roll-up" and "drill-down" in OLAP).
         
     *   **Example:** A location hierarchy: `City` (e.g., "New York") -\ `State` (e.g., "New York") -\ `Country` (e.g., "United States").
         
 
 ### **Module 3: Classification**
 
 #### **1\. Describe in detail about how to evaluate accuracy of the classifier.**
 
 1.  **Holdout Method:**
     
     *   **How it works:** Split the data into two sets: a training set (e.g., 70%) and a test set (e.g., 30%). Train the model on the training set and test its accuracy on the unseen test set.
         
     *   **Pros:** Quick and simple.
         
     *   **Cons:** The accuracy can vary significantly depending on the random split.
         
 2.  **Random Subsampling:**
     
     *   **How it works:** This is a repeated Holdout method. It creates multiple random splits of training/test data, calculates the accuracy for each, and then averages the results.
         
     *   **Pros:** More reliable and stable than a single Holdout.
         
 3.  **Cross-Validation (k-Fold):**
     
     *   **How it works:** Divide the data into `k` equal parts (folds). Train the model `k` times. In each run, one fold is used as the test set, and the other `k-1` folds are used for training. The final accuracy is the average of the `k` runs.
         
     *   **Pros:** Gives a highly reliable and accurate estimate of model performance as all data is used for both training and testing.
         
     *   **Cons:** Computationally more expensive (takes `k` times longer).
         
 4.  **Bootstrap:**
     
     *   **How it works:** Creates a training set by randomly sampling the original dataset _with replacement_. The records _not_ picked for the training set are used as the test set. This is repeated many times and the results are averaged.
         
     *   **Pros:** Works well for small datasets.
         
 
 #### **2\. Apply Naïve Bayes classification.**
 
 **Scenario:** Classify the tuple **X = (age = "\<=30", Income = "low", student = "yes", credit\_rating = "Excellent")** based on the following data:
 
 | Age | Income | Student | Credit\_Rating | Class: buys\_computer |
 | --- | --- | --- | --- | --- |
 | \<=30 | High | No  | Fair | No  |
 | \<=30 | High | No  | Excellent | No  |
 | 31..40 | High | No  | Fair | Yes |
 | \\40 | Medium | No  | Fair | Yes |
 | \\40 | Low | Yes | Fair | Yes |
 | \\40 | Low | Yes | Excellent | No  |
 | 31..40 | Low | Yes | Excellent | Yes |
 | \<=30 | Medium | No  | Fair | No  |
 | \<=30 | Low | Yes | Fair | Yes |
 | \\40 | Medium | Yes | Fair | Yes |
 | \<=30 | Medium | Yes | Excellent | Yes |
 | 31..40 | Medium | No  | Excellent | Yes |
 | 31..40 | High | Yes | Fair | Yes |
 | \\40 | Medium | No  | Excellent | No  |
 
 **Answer:**
 
 **Step 1: Calculate Prior Probabilities P(C)**
 
 *   Total samples: 14
     
 *   Class C1 (buys\_computer = "Yes"): 9 samples. **P(C1) = 9/14**
     
 *   Class C2 (buys\_computer = "No"): 5 samples. **P(C2) = 5/14**
     
 
 **Step 2: Calculate Likelihoods P(X|C) for Class C1 ("Yes")**
 
 *   P(age="\<=30" | C1) = 2/9
     
 *   P(Income="low" | C1) = 3/9
     
 *   P(student="yes" | C1) = 6/9
     
 *   P(credit\_rating="Excellent" | C1) = 3/9
     
 *   **P(X|C1)** = (2/9) \* (3/9) \* (6/9) \* (3/9) = **0.016**
     
 
 **Step 3: Calculate Likelihoods P(X|C) for Class C2 ("No")**
 
 *   P(age="\<=30" | C2) = 3/5
     
 *   P(Income="low" | C2) = 1/5
     
 *   P(student="yes" | C2) = 1/5
     
 *   P(credit\_rating="Excellent" | C2) = 3/5
     
 *   **P(X|C2)** = (3/5) \* (1/5) \* (1/5) \* (3/5) = **0.014**
     
 
 **Step 4: Calculate Posterior Probabilities P(X|C) \* P(C)**
 
 *   **For C1 ("Yes"):** P(X|C1) \* P(C1) = 0.016 \* (9/14) = **0.011**
     
 *   **For C2 ("No"):** P(X|C2) \* P(C2) = 0.014 \* (5/14) = **0.005**
     
 
 **Step 5: Make Prediction** Since **0.011 (Yes) \ 0.005 (No)**, the model predicts that the sample X belongs to the class **buys\_computer = "Yes"**.
 
 #### **3\. Explain how Naive Bayes classification makes predictions and discuss the "naive" assumption.**
 
 **How it Makes Predictions:** Naive Bayes is a probabilistic classifier based on **Bayes' Theorem**. It calculates the probability of a sample belonging to each class, given the sample's features. The class with the highest probability is chosen as the prediction.
 
 The formula is: P(Class∣Features)∝P(Class)×P(Features∣Class) (Posterior probability is proportional to Prior Probability \* Likelihood)
 
 **The "Naive" Assumption:** The "naive" part is a **class-conditional independence assumption**. The algorithm assumes that all features (attributes) are independent of each other, given the class.
 
 For an email, it assumes the word "Buy" and the word "Offer" appear independently of each other _within_ the "Spam" class. This is rarely true (these words often appear together), but it _massively_ simplifies the calculation, allowing the model to just multiply the probabilities: P("Buy", "Offer"∣"Spam")\=P("Buy"∣"Spam")×P("Offer"∣"Spam")  
 
 Despite this "naive" assumption being wrong, the classifier works very well in practice, especially for applications like spam filtering.
 
 ### **Module 4: Clustering**
 
 #### **1\. Explain K-means clustering algorithm and draw flowchart.**
 
 K-Means is an iterative clustering algorithm used to partition a dataset into `k` pre-defined, non-overlapping clusters. It aims to minimize the variance within each cluster by finding a "centroid" (mean point) for each group.
 
 **Algorithm Steps:**
 
 1.  **Initialize:** Input the number of clusters, `k`. Randomly initialize `k` centroids (mean points) in the data space.
     
 2.  **Assign:** Assign each data point to its _nearest_ centroid (e.g., using Euclidean distance). This forms `k` clusters.
     
 3.  **Update:** Recalculate the centroid for each cluster by taking the mean (average) of all data points assigned to that cluster.
     
 4.  **Repeat:** Repeat steps 2 (Assign) and 3 (Update) until the centroids no longer change (convergence) or a maximum number of iterations is reached.
     
 
 **Flowchart:**
 
 **Advantages:**
 
 *   Simple and easy to implement.
     
 *   Computationally efficient and scalable for large datasets.
     
 
 **Limitations:**
 
 *   Must specify the number of clusters (`k`) in advance.
     
 *   Sensitive to the random initialization of centroids; can get stuck in a local optimum.
     
 *   Assumes clusters are spherical and struggles with non-spherical shapes or varying densities.
     
 
 #### **2\. Differentiate between Agglomerative and Divisive clustering method.**
 
 These are the two types of **Hierarchical Clustering**.
 
 | Aspect | Agglomerative Clustering (AGNES) | Divisive Clustering (DIANA) |
 | --- | --- | --- |
 | **Approach** | **Bottom-up** | **Top-down** |
 | **Process** | Starts with each data point as its own cluster. It then iteratively **merges** the two closest clusters. | Starts with all data points in one single cluster. It then iteratively **splits** the cluster into two. |
 | **Granularity** | Begins with the finest granularity (single points). | Begins with the coarsest granularity (one big cluster). |
 | **Complexity** | Generally faster and more commonly used. | Computationally more intensive, as splitting is complex. |
 | **Output** | A dendrogram (tree) built by merging. | A dendrogram (tree) built by splitting. |
 
 #### **3\. Describe K-medoids algorithm.**
 
 K-Medoids is a clustering algorithm similar to K-Means, but it is **more robust to outliers and noise**.
 
 *   **Key Difference:** Instead of using a "centroid" (the mean of the cluster's points), K-Medoids uses a **"medoid"**.
     
 *   **Medoid:** A medoid is an **actual data point** from the dataset that is the most centrally located within the cluster.
     
 *   **Process:**
     
     1.  **Initialize:** Select `k` random data points as the initial medoids.
         
     2.  **Assign:** Assign all other data points to the nearest medoid.
         
     3.  **Update (Swap):** Iteratively try to swap each medoid with a non-medoid point. If the swap reduces the total "cost" (total distance of all points to their medoid), the swap is kept.
         
     4.  **Repeat:** Continue swapping until no swap can reduce the total cost.
         
 
 This use of an actual point as the center (medoid) means the cluster's center cannot be skewed by extreme outlier values, unlike the mean (centroid) in K-Means.
 
 ### **Module 5: Mining Frequent Patterns and Associations**
 
 #### **1\. Explain Multilevel and Multidimensional association rules mining with examples.**
 
 *   **Multilevel Association Rules:**
     
     *   These are rules mined from data that has a **concept hierarchy** (different levels of abstraction). This allows finding rules at both general and specific levels.
         
     *   **Example:** Given a hierarchy `Milk - Dairy - Food`, we can find:
         
         *   **High-Level Rule:** `buys(X, "Dairy") = buys(X, "Bread")` (Support = 10%)
             
         *   **Low-Level Rule:** `buys(X, "Milk") = buys(X, "Bread")` (Support = 4%)
             
     *   A **Reduced Support** threshold is often used for lower levels, as specific items are naturally less frequent.
         
 *   **Multidimensional Association Rules:**
     
     *   These are rules that involve **two or more dimensions** (attributes) in the data, not just a single "items" dimension.
         
     *   **Example:** A standard rule is `buys(X, "Diapers") = buys(X, "Beer")`.
         
     *   A **multidimensional rule** would be: `Age(X, "25-35") ^ Gender(X, "Male") = buys(X, "Beer")`
         
     *   This rule links three different dimensions (Age, Gender, and Buys) to find a more specific pattern.
         
 
 #### **2\. Write a short note on: FP tree.**
 
 An FP-tree (Frequent Pattern Tree) is a compact, tree-based data structure used by the **FP-Growth** algorithm for mining frequent itemsets.
 
 *   **Purpose:** It stores the complete information about frequent itemsets from a database in a highly compressed form, avoiding the costly candidate generation step of the Apriori algorithm.
     
 *   **Structure:**
     
     1.  It has a root labeled "null".
         
     2.  Each node contains an item-name, a count (support), and a node-link (pointing to the next node with the same item-name).
         
     3.  A "header table" stores each frequent item and the head of the node-link list for that item.
         
 *   **How it Works:** The database is scanned, and each transaction is inserted into the FP-tree. Transactions that share items (prefixes) will share a path in the tree, which leads to high compression. The tree is then mined recursively to find all frequent patterns.
     
 
 #### **3\. Explain market basket analysis with an example.**
 
 Market Basket Analysis (MBA) is a data mining technique used to discover customer purchasing patterns by finding associations between items that are frequently bought together.
 
 *   **Goal:** To find **Association Rules** in the form of `{If A} = {Then B}`.
     
 *   **Example:** A common rule found in retail data is **`{Diapers} = {Beer}`**. This suggests that customers who buy diapers are also likely to buy beer.
     
 *   **Key Metrics:**
     
     1.  **Support:** The percentage of total transactions that contain _both_ A and B. It measures how frequently the rule occurs.
         
         *   `Support({Diapers, Beer}) = (Transactions with Diapers & Beer) / (Total Transactions)`
             
     2.  **Confidence:** The probability that a customer will buy B, given that they bought A. It measures the rule's reliability.
         
         *   `Confidence({Diapers} = {Beer}) = (Transactions with Diapers & Beer) / (Transactions with Diapers)`
             
     3.  **Lift:** Measures the strength of the association. A lift \ 1 means the items are more likely to be bought together than by chance.
         
 
 This analysis helps businesses with store layout, promotions (e.g., "buy 1, get 1"), and recommendation engines.
 
 ### **Module 6: Web Mining**
 
 #### **1\. Explain page rank algorithm with example.**
 
 PageRank is a web structure mining algorithm, famously used by Google, to measure the "importance" of a web page. It's based on the idea that a page is important if important pages link to it.
 
 *   **Core Idea:** A link from Page A to Page B is considered a "vote" from A for B. This vote is weighted. A vote from an important page (like cnn.com) is worth more than a vote from a small blog.
     
 *   **Algorithm:** The PageRank (PR) of a page `p` is calculated iteratively: PR(p)\=(1−d)+d×∑q∈Bp​​Nq​PR(q)​  
     
     *   `d`: Damping factor (usually 0.85), representing the probability a user clicks a link.
         
     *   Bp​: The set of pages that link _to_ page `p`.
         
     *   PR(q): The PageRank of the linking page `q`.
         
     *   Nq​: The total number of _outbound_ links on page `q`. (A page's "vote" is split equally among all its outbound links).
         
 
 **Example:** Consider three pages A, B, and C.
 
 *   A links to B.
     
 *   B links to C.
     
 *   C links to A.
     
 
 1.  **Iteration 0:** All pages start with PR = 1.
     
 2.  **Iteration 1:**
     
     *   `PR(A) = (1-0.85) + 0.85 * (PR(C)/1) = 0.15 + 0.85 * (1/1) = 1.0`
         
     *   `PR(B) = (1-0.85) + 0.85 * (PR(A)/1) = 0.15 + 0.85 * (1/1) = 1.0`
         
     *   `PR(C) = (1-0.85) + 0.85 * (PR(B)/1) = 0.15 + 0.85 * (1/1) = 1.0` (Let's use a slightly different example from the PDF for clarity: `A->B, A->C, B->C, C->A`)
         
 
 **Example (from PDF):** Links: `A->B`, `A->C`, `B->C`, `C->A`
 
 1.  **Iteration 0:** PR(A)=1, PR(B)=1, PR(C)=1
     
 2.  **Iteration 1:**
     
     *   `PR(A) = 0.15 + 0.85 * (PR(C)/1) = 0.15 + 0.85 * (1) = 1.0`
         
     *   `PR(B) = 0.15 + 0.85 * (PR(A)/2) = 0.15 + 0.85 * (1/2) = 0.575`
         
     *   `PR(C) = 0.15 + 0.85 * (PR(A)/2 + PR(B)/1) = 0.15 + 0.85 * (1/2 + 1/1) = 1.425` (Note: PDF calculation seems different, I will follow the PDF's logic)
         
 
 _Following PDF's apparent logic (PR(B) in C's calculation is from Iteration 0):_ `PR(C) = 0.15 + 0.85 * (PR(A)/2 + PR(B)/1) = 0.15 + 0.85 * (1/2 + 1/1) = 0.15 + 0.85 * (1.5) = 1.425` _Let's use the PDF's Iteration 1 values from its table (which seem to have a different link graph: C-\A, A-\B, B-\C):_ `PR(A)=1, PR(B)=0.575, PR(C)=1.06375` 3. **Iteration 2:** \* `PR(A) = 0.15 + 0.85 * (PR(C)/1) = 0.15 + 0.85 * (1.06375) = 1.054` \* `PR(B) = 0.15 + 0.85 * (PR(A)/1) = 0.15 + 0.85 * (1.0) = 1.0` (Using A's value from _previous_ iteration) \* `PR(C) = 0.15 + 0.85 * (PR(B)/1) = 0.15 + 0.85 * (0.575) = 0.638` This process repeats until the ranks stabilize.
 
 #### **2\. Explain web structure mining and its approaches.**
 
 **Web Structure Mining** is a branch of web mining that focuses on analyzing the **hyperlink structure of the web**. It models the web as a graph, where web pages are nodes and hyperlinks are directed edges.
 
 **Goal:** To discover the relationship between web pages and identify important "authoritative" pages and "hub" pages.
 
 **Approaches:**
 
 1.  **PageRank Algorithm:**
     
     *   This is the algorithm used by Google. It measures the "importance" of a page based on the number and quality of pages that link _to_ it.
         
     *   A page is considered important if other important pages link to it. It's an iterative algorithm that assigns a numerical rank to every page.
         
 2.  **HITS (Hyperlink-Induced Topic Search) or Clever Algorithm:**
     
     *   This algorithm identifies two types of pages for a given query:
         
         *   **Authorities:** Pages that are the best sources of information on a topic (e.g., the official documentation for a product).
             
         *   **Hubs:** Pages that don't contain the information themselves, but act as directories by linking _to_ many authoritative pages (e.g., a "best resources" list).
             
     *   HITS works by iteratively updating an "authority score" and a "hub score" for each page. A good hub links to good authorities, and a good authority is linked to by good hubs.
         
 
 #### **3\. Write a note on web usage mining and its applications.**
 
 **Web Usage Mining** is the process of analyzing user interaction and behavior on a website by extracting patterns from **web log data**. This data includes user clickstreams, navigation paths, session information, IP addresses, and timestamps.
 
 **Process:**
 
 1.  **Data Collection:** Gathering raw data from server logs, cookies, etc.
     
 2.  **Data Preprocessing:** Cleaning the logs, identifying unique users, and segmenting sessions.
     
 3.  **Pattern Discovery:** Using data mining techniques (like sequential pattern mining, association, and clustering) to find behavior patterns.
     
 4.  **Pattern Analysis:** Interpreting the patterns to gain insights.
     
 
 **Applications:**
 
 1.  **Personalization:** Customizing the website content, layout, and recommendations for individual users based on their past browsing behavior.
     
 2.  **Website Optimization:** Improving the site's navigation and structure by analyzing common user paths, identifying bottlenecks, or finding where users drop off.
     
 3.  **E-commerce:** Improving sales by recommending related products ("Customers who viewed this also viewed..."), a form of market basket analysis on browsing data.
     
 
 #### **4\. Write a short note on: Web content mining.**
 
 **Web Content Mining** involves extracting useful information directly from the **content** of web pages. The content can be unstructured text, semi-structured data (like HTML tables or lists), or multimedia (images, videos).
 
 *   **Goal:** To understand the _meaning_, _relevance_, and _topic_ of web content.
     
 *   **Techniques:** It heavily uses techniques from:
     
     *   **Text Mining:** Extracting insights from textual content using Natural Language Processing (NLP) and sentiment analysis.
         
     *   **Multimedia Mining:** Analyzing images and videos.
         
     *   **Structured Data Mining:** Extracting information from tables or metadata.
         
 *   **Applications:**
     
     *   **Search Engines:** Indexing web content to provide relevant search results.
         
     *   **Content Summarization:** Creating automatic summaries of web pages.
         
     *   **Topic/Trend Detection:** Finding emerging topics in online articles or blogs.
         
 
 #### **5\. Explain CLARANS extension in web mining.**
 
 **CLARANS (Clustering Large Applications based on Randomized Search)** is an extension of the **k-medoids** clustering algorithm, designed to be more efficient and scalable for large datasets.
 
 *   **Problem it Solves:** Traditional k-medoids (like PAM) is computationally expensive because it exhaustively checks all possible swaps between a medoid and a non-medoid to find the best cluster center.
     
 *   **CLARANS's Solution:** It combines the K-Medoids approach with **randomized search**.
     
     1.  It doesn't check _all_ possible swaps. Instead, it randomly selects a _sample_ of non-medoid points to test as potential swaps.
         
     2.  If a swap in this random sample improves the clustering quality (reduces total cost), the swap is made.
         
     3.  It repeats this randomized search process multiple times from different starting points to avoid getting stuck in a local minimum and to increase the chance of finding a good quality clustering.
         
 *   **Application in Web Mining:** Because web usage logs can be massive, CLARANS is used in **web usage mining** to efficiently cluster large numbers of user sessions or browsing patterns to identify groups of users with similar behavior.
