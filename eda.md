# Exploratory Data Analysis (EDA)
  
### **What is EDA?**
Exploratory Data Analysis is the first step you take when you get a new dataset. The goal is to "get to know" your data. You summarize its main characteristics, find patterns, spot obvious errors, and see how different variables relate to each other. It's like being a detective and finding initial clues

### **What is the goal of this experiment?**
To explore the famous "Iris" flower dataset to understand its structure and the relationships between the flower's features (like sepal length and width).

### Understanding the Code (Step-by-Step)

1.  **Import Libraries:**

    ```python
    import pandas as pd
    import seaborn as sb
    import matplotlib.pyplot as plt
    ```

      * `pandas` is for working with data in tables (called DataFrames).
      * `seaborn` and `matplotlib.pyplot` are for creating plots and graphs.

2.  **Load the Dataset:**

    ```python
    iris_d = sb.load_dataset("iris")
    ```

      * This loads the built-in "iris" dataset from the Seaborn library into a pandas DataFrame called `iris_d`.

3.  **Initial Inspection:**

    ```python
    iris_d.head()      # Shows the first 5 rows
    iris_d.tail()      # Shows the last 5 rows
    iris_d.shape       # Shows (number of rows, number of columns)
    iris_d.info()      # Gives a summary of the data types and non-null values
    iris_d.describe()  # Calculates basic statistics (mean, min, max, etc.) for numerical columns
    ```

      * These commands are the quickest way to get a first look at your data.

4.  **Visualizations (The Core of EDA):**

      * **Scatter Plot:**
        ```python
        plt.scatter(iris_d['sepal_length'], iris_d['sepal_width'], color='red')
        ```
        This plot helps you see the relationship between two variables. Here, it plots sepal length against sepal width.
      * **Histogram:**
        ```python
        plt.hist(iris_d['sepal_width'], bins=15)
        ```
        This shows the *distribution* of a single variable. It tells you how many flowers fall into different ranges of sepal width.
      * **Box Plot:**
        ```python
        sb.boxplot(x="sepal_width", data=iris_d)
        ```
        A box plot is great for seeing the median, quartiles, and identifying **outliers** (unusually high or low values).
      * **Q-Q Plot (Probability Plot):**
        ```python
        stats.probplot(iris_d['petal_length'], dist="norm", plot=plt)
        ```
        This plot is used to check if your data follows a specific distribution, in this case, a **normal distribution** (a bell curve). If the blue dots follow the red line closely, the data is normally distributed.
