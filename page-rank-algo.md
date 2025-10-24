# PageRank Algorithm

### **What is PageRank Algorithm?**
The PageRank algorithm is a famous algorithm that was originally used by Google to rank the importance of web pages. The main idea is that a page is considered important if many other important pages link to it. It assigns a numerical score to each page, with a higher score indicating more importance. 🌐

### **What is the goal of this experiment?**
To calculate the importance (the PageRank score) for a small, imaginary network of web pages.

---

### Understanding the Algorithm (Step-by-Step)

1. **Represent the Web:** We start by representing the web pages and their links as a **graph**. If page 'A' links to page 'B', we draw an arrow from A to B.
2. **Initial Guess:** We assume all pages have the same initial rank (e.g., 1 divided by the total number of pages).
3. **Iterative Calculation:** In each step (iteration), we update the rank of every page based on the ranks of the pages that link to it. The rank of a page 'B' gets a fraction of the rank of every page 'A' that links to it. This fraction is the rank of 'A' divided by the number of outgoing links from 'A'.
4. **Damping Factor:** We add a "damping factor" (usually `d=0.85`) to simulate a user randomly jumping to any page. This prevents the algorithm from getting stuck in loops and ensures all pages get some rank.
5. **Convergence:** We repeat the calculation step many times until the ranks stop changing significantly. The final scores are the PageRank values.

---

### Example:

```python
# --- Step 1: Import necessary libraries ---
import numpy as np

def pagerank(link_matrix, d=0.85, max_iterations=100, tolerance=1.0e-6):
    """
    Calculates the PageRank for a given link matrix.

    Args:
    link_matrix (np.array): A square matrix where M[i, j] = 1 if node j links to node i.
    d (float): The damping factor (usually 0.85).
    max_iterations (int): Maximum number of iterations to prevent infinite loops.
    tolerance (float): The convergence tolerance to stop early.

    Returns:
    np.array: A vector containing the PageRank score for each page.
    """
    num_pages = link_matrix.shape[0]
    if num_pages == 0:
        return np.array([])

    # Create the probability transition matrix (M)
    # This is done by normalizing columns by their sum (out-degree)
    out_degree = np.sum(link_matrix, axis=0)
    
    # Handle dangling nodes (pages with no outgoing links)
    # We pretend they link to all pages equally to conserve rank
    for j in range(num_pages):
        if out_degree[j] == 0:
            link_matrix[:, j] = 1
            out_degree[j] = num_pages
            
    M = link_matrix / out_degree
    
    # Initialize the PageRank vector (r)
    r = np.full(num_pages, 1.0 / num_pages)
    
    # --- Step 2: Iteratively calculate PageRank ---
    for i in range(max_iterations):
        old_r = r.copy()
        
        # The core PageRank formula
        r = ((1 - d) / num_pages) + d * M @ old_r
        
        # Check for convergence
        if np.linalg.norm(r - old_r, 1) < tolerance:
            print(f"Converged after {i+1} iterations.")
            return r
            
    print("Reached max iterations without converging.")
    return r

# --- Step 3: Define an example web graph and run the algorithm ---
# Example: 4 pages (A, B, C, D)
# A -> B, C
# B -> C
# C -> A
# D -> C
# The link_matrix[i, j] is 1 if page j links to page i
#       From: A B C D
# To A [[0, 0, 1, 0],
# To B  [1, 0, 0, 0],
# To C  [1, 1, 0, 1],
# To D  [0, 0, 0, 0]]
link_matrix = np.array([
    [0, 0, 1, 0],
    [1, 0, 0, 0],
    [1, 1, 0, 1],
    [0, 0, 0, 0]
])

# Calculate PageRank
page_ranks = pagerank(link_matrix)

# --- Step 4: Display the results ---
print("\n--- Final PageRank Scores ---")
for i, rank in enumerate(page_ranks):
    # chr(65+i) converts 0 to 'A', 1 to 'B', etc.
    print(f"Page {chr(65+i)}: {rank:.4f}")

```

From the results, you would see that Page C is the most important, as it receives links from three other pages.
