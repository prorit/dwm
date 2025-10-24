# Data Pre-processing

### **What is Data Pre-processing?**
Real-world data is messy. It can have missing values, text instead of numbers, or features with vastly different scales. Data pre-processing is the crucial step of cleaning and preparing this raw data so that machine learning models can understand it.

### **What is the goal of this experiment?**
To take a messy sample dataset and clean it by:
1. Handling missing numerical values.
2. Converting categorical (text) data into numbers.
3. Splitting data for training and testing.
4. Scaling the features.
---
### Understanding the Code (Step-by-Step)

1.  **Load Data and Separate:**

    ```python
    dataset = pd.read_csv("Data.csv")
    x = dataset.iloc[:, :-1].values  # Features (all columns except the last)
    y = dataset.iloc[:, -1].values   # Target (the last column)
    ```

2.  **Handle Missing Values:**

    ```python
    from sklearn.impute import SimpleImputer
    imputer = SimpleImputer(missing_values=np.nan, strategy='mean')
    imputer.fit(x[:, 1:3])
    x[:, 1:3] = imputer.transform(x[:, 1:3])
    ```

      * This finds the missing `np.nan` values in the "Age" and "Salary" columns.
      * It calculates the **mean** (average) of each column.
      * It then fills the missing spots with that column's mean.

3.  **Encode Categorical Data (Features):**

    ```python
    from sklearn.compose import ColumnTransformer
    from sklearn.preprocessing import OneHotEncoder
    ct = ColumnTransformer(transformers=[('encoder', OneHotEncoder(), [0])], remainder='passthrough')
    x = np.array(ct.fit_transform(x))
    ```

      * The "Country" column is text (`France`, `Spain`). Models need numbers.
      * **One-Hot Encoding** converts this one column into three new columns (one for each country). If a row was `France`, the new `France` column is `1` and the others are `0`.
      * `remainder='passthrough'` keeps the other columns (Age, Salary) untouched.

4.  **Encode Categorical Data (Target):**

    ```python
    from sklearn.preprocessing import LabelEncoder
    le = LabelEncoder()
    y = np.array(le.fit_transform(y))
    ```

      * The target column "Purchased" is also text (`Yes`/`No`).
      * **Label Encoding** is simpler and just converts these labels to numbers, for example, `Yes` becomes `1` and `No` becomes `0`.

5.  **Split Data into Training and Test Sets:**

    ```python
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size = 0.2, random_state = 1)
    ```

      * This splits the data so you can train your model on one part (`_train`) and test its performance on a separate, unseen part (`_test`). `test_size=0.2` means 20% of the data is used for testing.

6.  **Feature Scaling:**

    ```python
    from sklearn.preprocessing import StandardScaler
    sc = StandardScaler()
    x_train[:, 3:] = sc.fit_transform(x_train[:, 3:])
    x_test[:, 3:] = sc.transform(x_test[:, 3:])
    ```

      * This rescales the numerical columns (Age and Salary) so they have a similar range. This prevents one feature from dominating the others just because its numbers are bigger.
---

### Example:

```python
# --- Step 1: Import necessary libraries ---
import numpy as np
import pandas as pd
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import LabelEncoder, OneHotEncoder, StandardScaler
from sklearn.compose import ColumnTransformer
from sklearn.model_selection import train_test_split

# --- Step 2: Load the dataset and separate features from the target ---
# Make sure 'Data.csv' is in the same folder as your script.
dataset = pd.read_csv('Data.csv')
X = dataset.iloc[:, :-1].values  # Features (all columns except the last one)
y = dataset.iloc[:, -1].values   # Target (the last column)

# --- Step 3: Handle missing numerical data ---
# We will replace missing numbers (NaN) with the mean of the column.
imputer = SimpleImputer(missing_values=np.nan, strategy='mean')
imputer.fit(X[:, 1:3]) # Fit on Age and Salary columns
X[:, 1:3] = imputer.transform(X[:, 1:3])

# --- Step 4: Encode categorical features ---
# Convert the 'Country' column into numerical data using One-Hot Encoding.
ct = ColumnTransformer(transformers=[('encoder', OneHotEncoder(), [0])], remainder='passthrough')
X = np.array(ct.fit_transform(X))

# --- Step 5: Encode the target variable ---
# Convert 'Yes'/'No' labels into 1/0.
le = LabelEncoder()
y = le.fit_transform(y)

# --- Step 6: Split the dataset into Training and Test sets ---
# 80% of data will be for training, 20% for testing.
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=1)

# --- Step 7: Feature Scaling ---
# Scale the Age and Salary columns to a standard range.
sc = StandardScaler()
X_train[:, 3:] = sc.fit_transform(X_train[:, 3:])
X_test[:, 3:] = sc.transform(X_test[:, 3:])

# --- Step 8: Print the processed data ---
print("--- Processed Training Features (X_train) ---")
print(X_train)
print("\n--- Processed Test Features (X_test) ---")
print(X_test)
```
