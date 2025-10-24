# Apriori Association Rule Mining

### **What is Apriori Association Rule Mining?** 
This is a technique for discovering interesting relationships between items in a large dataset. It's famously used for **Market Basket Analysis** to find which products are frequently bought together (e.g., "People who buy diapers also tend to buy beer").
### **What is the goal of this experiment?** 
To analyze a list of grocery transactions and find association rules, like "IF a customer buys {spaghetti}, THEN they are also likely to buy {mineral water}."

---

### Understanding the Code (Step-by-Step)

1.  **Install the Library:**

    ```python
    !pip install apyori
    ```

      * This installs the special library needed to run the Apriori algorithm.

2.  **Load and Prepare Data:**

    ```python
    dataset = pd.read_csv('Market_Basket_Optimisation.csv', header = None)
    transactions = []
    for i in range(0, 7501):
        transactions.append([str(dataset.values[i,j]) for j in range(0, 20)])
    ```

      * The `apriori` function requires the data to be in a specific format: a **list of lists**, where each inner list represents one transaction (one shopping basket). This loop converts the pandas DataFrame into that format.

3.  **Run the Apriori Algorithm:**

    ```python
    from apyori import apriori
    rules = apriori(transactions = transactions, min_support = 0.003, min_confidence = 0.2)
    ```

      * This is where the magic happens. The `apriori` function is called on the list of transactions.
      * **`min_support = 0.003`**: This is a threshold. It tells the algorithm to only consider itemsets that appear in at least 0.3% of all transactions. This filters out very rare items.
      * **`min_confidence = 0.2`**: This sets a minimum confidence level for the rules. It means for a rule "IF A THEN B", item B must appear in at least 20% of the transactions that contain item A.

4.  **View the Results:**

    ```python
    results = list(rules)
    print(results)
    ```

      * This converts the output into a list and prints it. Each item in the list (`RelationRecord`) is a rule found by the algorithm, along with its support and confidence values. You can examine these rules to find interesting patterns.
