# K-Means Clustering

### **What is K-Mean Clustering?** 
Clustering is an **unsupervised learning** technique, meaning it finds patterns in data without a pre-defined target label. K-Means is the most common clustering algorithm. It groups the data into a specified number (K) of clusters based on their similarity.
### **What is the goal of this experiment?** 
To segment mall customers into groups based on their `Annual Income` and `Spending Score`. This could help a marketing team understand their customers better.

---

### Understanding the Code (Step-by-Step)

1.  **Load Data:**

    ```python
    dataset = pd.read_csv('Mall_Customers.csv')
    X = dataset.iloc[:, [3, 4]].values # Selects only the income and score columns
    ```

2.  **Find the Best Number of Clusters (K) using the Elbow Method:**

    ```python
    from sklearn.cluster import KMeans
    wcss = []
    for i in range(1, 11):
        kmeans = KMeans(n_clusters = i, init = 'k-means++', random_state = 42)
        kmeans.fit(X)
        wcss.append(kmeans.inertia_)
    plt.plot(range(1, 11), wcss)
    plt.show()
    ```

      * This code tries running K-Means with K=1, then K=2, all the way to K=10.
      * `kmeans.inertia_` (also called WCSS) measures how compact the clusters are. A lower value is better.
      * The plot shows WCSS for each K. The point where the line starts to bend (the "elbow") is the best trade-off between having few clusters and having compact clusters. For this dataset, the elbow is at **K=5**.

3.  **Run K-Means with K=5:**

    ```python
    kmeans = KMeans(n_clusters = 5, init = 'k-means++', random_state = 42)
    y_kmeans = kmeans.fit_predict(X)
    ```

      * A new K-Means model is created with `n_clusters = 5`.
      * `.fit_predict(X)` trains the model and assigns each customer (each row in `X`) to one of the 5 clusters. The results are stored in `y_kmeans`.

4.  **Visualize the Clusters:**

    ```python
    plt.scatter(...) # This code is repeated for each of the 5 clusters
    plt.scatter(kmeans.cluster_centers_[:, 0], kmeans.cluster_centers_[:, 1], s = 300, c = 'yellow', label = 'Centroids')
    plt.show()
    ```

      * This creates a scatter plot where each cluster is a different color.
      * `kmeans.cluster_centers_` are the coordinates of the center of each cluster, which are plotted as large yellow stars. The plot clearly shows the 5 distinct customer segments.
