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

---

### Example:

```python
# --- Step 1: Install apyori if you haven't already ---
# In Jupyter or Spyder, you might need to run: !pip install apyori

# --- Step 2: Import libraries ---
import pandas as pd
from apyori import apriori

# --- Step 3: Load and prepare the data ---
# Make sure 'Market_Basket_Optimisation.csv' is in the same folder.
# The data needs to be a list of lists, where each inner list is one transaction.
dataset = pd.read_csv('Market_Basket_Optimisation.csv', header=None)
transactions = []
for i in range(len(dataset)):
    # Convert each transaction (row) into a list of strings
    transactions.append([str(dataset.values[i, j]) for j in range(dataset.shape[1]) if str(dataset.values[i, j]) != 'nan'])

# --- Step 4: Train the Apriori model ---
# min_support: Considers items that appear in at least 0.3% of transactions.
# min_confidence: A rule "A -> B" must be correct at least 20% of the time.
# min_lift: Measures how much more likely item B is purchased when A is purchased.
# min_length=2: We want rules with at least two items (e.g., A -> B).
rules = apriori(transactions=transactions, min_support=0.003, min_confidence=0.2, min_lift=3, min_length=2)

# --- Step 5: Display the results in a readable format ---
results = list(rules)
print("--- Discovered Association Rules ---")

for item in results:
    # Extract items from the rule
    pair = item[0]
    items = [x for x in pair]
    
    # Print the rule and its metrics
    print(f"Rule: {items[0]} -> {items[1]}")
    print(f"Support: {item[1]:.4f}")
    print(f"Confidence: {item[2][0][2]:.4f}")
    print(f"Lift: {item[2][0][3]:.4f}")
    print("="*40)
```
