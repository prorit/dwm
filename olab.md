# OLAP Operations

### **What are OLAP Operations?** 
OLAP stands for **Online Analytical Processing**. It refers to a set of operations that allow you to analyze multidimensional data quickly. Think of your data as a cube (a "data cube") where each axis is a dimension (like Product, Time, Location). OLAP operations let you look at this cube from different angles. 🎲

* **Roll-up:** To aggregate data to a higher level (e.g., from city sales to country sales).
* **Drill-down:** The opposite of roll-up; to see more detailed data (e.g., from yearly sales to monthly sales).
* **Slice:** To look at a single slice of the cube (e.g., sales for only the "Electronics" category).
* **Dice:** To select a sub-cube by picking specific values for multiple dimensions (e.g., sales of "Electronics" in "New York" for the year "2024").

### **What is the goal of this experiment?**
To simulate these OLAP operations on a sample sales dataset using the pandas library in Python.

---

### Example:

```python
# --- Step 1: Import necessary libraries ---
import pandas as pd
import numpy as np

# --- Step 2: Create a sample sales DataFrame to simulate a data cube ---
data = {
    'Year': [2023, 2023, 2023, 2023, 2024, 2024, 2024, 2024],
    'Region': ['North', 'North', 'South', 'South', 'North', 'North', 'South', 'South'],
    'Product': ['Laptop', 'Mouse', 'Laptop', 'Mouse', 'Laptop', 'Mouse', 'Laptop', 'Mouse'],
    'Sales': np.random.randint(100, 1000, 8) * 100
}
df = pd.DataFrame(data)
print("--- Original Data Cube ---")
print(df)

# --- Step 3: Perform OLAP Operations ---

# Roll-up: Aggregate sales by Year (from Region/Product level up to Year level).
print("\n--- OLAP: Roll-up (Total Sales per Year) ---")
rollup_df = df.groupby('Year')['Sales'].sum().reset_index()
print(rollup_df)

# Drill-down: The original DataFrame is already a "drilled-down" view.
# To demonstrate, we can group by all dimensions to see the most detailed view.
print("\n--- OLAP: Drill-down (Sales per Year, Region, and Product) ---")
drilldown_df = df.groupby(['Year', 'Region', 'Product'])['Sales'].sum().reset_index()
print(drilldown_df)

# Slice: Select a slice of the data for only the 'North' region.
print("\n--- OLAP: Slice (Sales for Region = 'North') ---")
slice_df = df[df['Region'] == 'North']
print(slice_df)

# Dice: Select a sub-cube for 'Laptop' sales in the 'South' region.
print("\n--- OLAP: Dice (Sales for Product='Laptop' AND Region='South') ---")
dice_df = df[(df['Product'] == 'Laptop') & (df['Region'] == 'South')]
print(dice_df)
```
