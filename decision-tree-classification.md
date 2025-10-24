# Decision Tree Classification

### **What is Decision Tree Classification?** 
A Decision Tree is a popular and easy-to-understand machine learning algorithm for **classification** (predicting a category, like "Yes" or "No"). It creates a model that looks like a flowchart, where each node asks a question about a feature.

### **What is the goal of this experiment?**
To predict whether a person will purchase a product (`Purchased` = 1) or not (`Purchased` = 0) based on their `Age` and `EstimatedSalary`.

---

### Understanding the Code (Step-by-Step)

1.  **Load and Split Data:**

    ```python
    dataset = pd.read_csv('Social_Network_Ads.csv')
    X = dataset.iloc[:, :-1].values
    y = dataset.iloc[:, -1].values
    # Then, perform train_test_split as in the previous experiment
    ```

2.  **Feature Scaling:**

    ```python
    from sklearn.preprocessing import StandardScaler
    # The code scales X_train and X_test here.
    ```

      * As before, this is done to make sure `Age` and `EstimatedSalary` are treated equally by the model.

3.  **Train the Decision Tree Model:**

    ```python
    from sklearn.tree import DecisionTreeClassifier
    classifier = DecisionTreeClassifier(criterion = 'entropy', random_state = 0)
    classifier.fit(X_train, y_train)
    ```

      * An instance of the `DecisionTreeClassifier` is created.
      * `criterion='entropy'` is a mathematical function used to decide the best way to split the data at each node.
      * `.fit(X_train, y_train)` is the **training** step. The algorithm learns the patterns from the training data.

4.  **Make a Prediction:**

    ```python
    print(classifier.predict(sc.transform([[20, 87000]])))
    ```

      * This is an example of predicting for a single new person (age 20, salary 87000). Note that `sc.transform` is used to scale the new data in the same way the training data was scaled.

5.  **Evaluate the Model's Performance:**

    ```python
    from sklearn.metrics import confusion_matrix, accuracy_score
    y_pred = classifier.predict(X_test)
    cm = confusion_matrix(y_test, y_pred)
    print(cm)
    accuracy_score(y_test, y_pred)
    ```

      * The model predicts the outcome for all the data in the test set (`X_test`).
      * A **Confusion Matrix** (`cm`) is created to see where the model was right and wrong. It will be a 2x2 matrix.
      * `accuracy_score` calculates the overall percentage of correct predictions. In this case, `0.91` means it was 91% accurate on the test data.
