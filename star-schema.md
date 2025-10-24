# Design Star Schema

### **What is Star Schema?**
A **Star Schema** is a way of organizing a database for a data warehouse. It's called a "star" because it looks like one: there is a central **Fact Table** connected to several **Dimension Tables**. ⭐

* **Fact Table:** Contains the main business measurements or "facts" (e.g., `Sales Amount`, `Quantity Sold`). It's the center of the star.
* **Dimension Tables:** Contain the context or descriptions for the facts (e.g., `Product Details`, `Store Locations`, `Time Information`). These are the points of the star.

### **What is the goal of this experiment?**
To design and create the database tables for a sales data warehouse, which can be used for business analysis and reporting.

---

### Understanding the Design (Step-by-Step)

1. **Identify the Business Process:** The process to analyze is product sales.
2. **Identify the Facts:** The key measurements are the number of units sold and the total revenue. These will go into the `FactSales` table.
3. **Identify the Dimensions:** The context for each sale is *what* was sold (Product), *where* it was sold (Store), and *when* it was sold (Time). These become our dimension tables: `DimProduct`, `DimStore`, and `DimTime`.
4. **Define Attributes:** Each dimension table will have columns (attributes) describing it. For example, `DimProduct` will have product name, category, and brand.
5. **Link Tables with Keys:** The Fact table is linked to the dimension tables using `foreign keys`. For instance, the `FactSales` table will have a `product_key` that refers to a specific product in the `DimProduct` table.
