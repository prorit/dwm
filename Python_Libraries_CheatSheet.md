# 🧠 Python Libraries & Concepts (Cheat Sheet)

---

## Table of Contents
1. [Pandas](#1-pandas-import-pandas-as-pd)
2. [Matplotlib & Seaborn](#2-matplotlib--seaborn-plotting)
3. [Scikit-learn](#3-scikit-learn-from-sklearn)
4. [Apyori](#4-apyori-from-apyori-import-apriori)
5. [PageRank (NumPy)](#5-pagerank-numpy-import-numpy-as-np)
6. [Star Schema (SQLite)](#6-star-schema-sqlite-import-sqlite3)

---

## 1. **Pandas** (`import pandas as pd`)
Used for loading and manipulating data (EDA, Pre-processing, OLAP).

### --- Loading Data ---
```python
df = pd.read_csv('your_file_name.csv')
```

### --- Inspecting Data (EDA) ---
```python
print(df.head())       # Show first 5 rows
print(df.info())       # Show column types and missing values
print(df.describe())   # Show stats (mean, min, max) for numeric columns
```

### --- Selecting Data (`iloc` = index location) ---
```python
# Select all rows, all columns EXCEPT the last one
X = df.iloc[:, :-1].values

# Select all rows, ONLY the last column
y = df.iloc[:, -1].values

# Select specific columns (e.g., 3 and 4 for K-Means)
X = df.iloc[:, [3, 4]].values
```

### --- OLAP Operations ---
```python
# Roll-up (e.g., total sales by year)
rollup = df.groupby('Year')['Sales'].sum()

# Slice (e.g., all data for 'North' region)
slice_df = df[df['Region'] == 'North']

# Dice (e.g., 'Laptop' sales in the 'North')
dice_df = df[(df['Product'] == 'Laptop') & (df['Region'] == 'North')]
```

---

## 2. **Matplotlib & Seaborn** (Plotting)
Used for visualizing data (EDA, K-Means).

```python
import matplotlib.pyplot as plt
import seaborn as sns
```

### --- Histogram ---
```python
plt.hist(df['column_name'])
plt.title('My Title')
plt.xlabel('X-axis Label')
plt.ylabel('Y-axis Label')
plt.show()
```

### --- Scatter Plot ---
```python
plt.scatter(df['column_1'], df['column_2'])
plt.show()
```

### --- Box Plot ---
```python
sns.boxplot(x='category_column', y='number_column', data=df)
plt.show()
```

### --- K-Means Cluster Plot ---
```python
plt.scatter(X[y_kmeans == 0, 0], X[y_kmeans == 0, 1], label='Cluster 1')
plt.scatter(X[y_kmeans == 1, 0], X[y_kmeans == 1, 1], label='Cluster 2')
# ... repeat for all clusters ...
plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1],
            s=300, c='yellow', label='Centroids')
plt.legend()
plt.show()
```

---

## 3. **Scikit-learn** (`from sklearn...`)
Main library for **Machine Learning** (Pre-processing, Models, Metrics).

### --- Handle Missing Values ---
```python
from sklearn.impute import SimpleImputer
import numpy as np

imputer = SimpleImputer(missing_values=np.nan, strategy='mean')
X[:, 1:3] = imputer.fit_transform(X[:, 1:3])
```

### --- Encode Text Labels (Target) ---
```python
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
y = le.fit_transform(y)
```

### --- Encode Categorical Features ---
```python
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer

ct = ColumnTransformer(transformers=[('encoder', OneHotEncoder(), [0])],
                       remainder='passthrough')
X = ct.fit_transform(X)
```

### --- Train/Test Split ---
```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
```

### --- Feature Scaling ---
```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
X_train = sc.fit_transform(X_train)
X_test = sc.transform(X_test)
```

### --- Decision Tree Classifier ---
```python
from sklearn.tree import DecisionTreeClassifier

classifier = DecisionTreeClassifier(criterion='entropy', random_state=0)
classifier.fit(X_train, y_train)
y_pred = classifier.predict(X_test)
```

### --- K-Means Clustering ---
```python
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=5, init='k-means++', random_state=0, n_init=10)
y_kmeans = kmeans.fit_predict(X)
wcss_score = kmeans.inertia_
centers = kmeans.cluster_centers_
```

### --- Metrics ---
```python
from sklearn.metrics import accuracy_score, confusion_matrix

acc = accuracy_score(y_test, y_pred)
print(f"Accuracy: {acc:.2f}")

cm = confusion_matrix(y_test, y_pred)
print(cm)
```

---

## 4. **Apyori** (`from apyori import apriori`)
Used for **Association Rule Mining**.

### --- Prepare Data ---
```python
transactions = []
for i in range(len(dataset)):
    transactions.append([str(dataset.values[i, j])
                         for j in range(dataset.shape[1])
                         if str(dataset.values[i, j]) != 'nan'])
```

### --- Run Apriori ---
```python
from apyori import apriori

rules = apriori(transactions=transactions,
                min_support=0.003,
                min_confidence=0.2,
                min_lift=3,
                min_length=2)

results = list(rules)
```

---

## 5. **PageRank (NumPy)** (`import numpy as np`)
Used for PageRank algorithm and general matrix operations.

```python
import numpy as np

def pagerank(link_matrix, d=0.85):
    num_pages = link_matrix.shape[0]
    out_degree = np.sum(link_matrix, axis=0)
    for j in range(num_pages):
        if out_degree[j] == 0:
            link_matrix[:, j] = 1
            out_degree[j] = num_pages
    M = link_matrix / out_degree
    r = np.full(num_pages, 1.0 / num_pages)
    for _ in range(100):
        old_r = r.copy()
        r = ((1 - d) / num_pages) + d * M @ r
    return r
```

### --- Example ---
```python
link_matrix = np.array([
    [0, 0, 1],  # A receives link from C
    [1, 0, 0],  # B receives link from A
    [0, 1, 0]   # C receives link from B
])
ranks = pagerank(link_matrix)
```

---

## 6. **Star Schema (SQLite)** (`import sqlite3`)
Used to create a small data warehouse structure.

```python
import sqlite3

conn = sqlite3.connect('my_warehouse.db')
cursor = conn.cursor()

# --- Create Dimension Table ---
cursor.execute('''
CREATE TABLE IF NOT EXISTS DimProduct (
    product_key INTEGER PRIMARY KEY,
    product_name TEXT,
    category TEXT
);
''')

# --- Create Fact Table ---
cursor.execute('''
CREATE TABLE IF NOT EXISTS FactSales (
    sales_id INTEGER PRIMARY KEY,
    product_key INTEGER,
    units_sold INTEGER,
    FOREIGN KEY (product_key) REFERENCES DimProduct(product_key)
);
''')

conn.commit()
conn.close()
```
