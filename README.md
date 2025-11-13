# Design Choices — Data Warehouse

## 1. Architecture Overview

This Data Warehouse is built using a multi-layer architecture Raw → Stage → Dimensions → Fact, which ensures transparent transformations, scalability, and high-quality analytics.

### Layers

- Raw Layer  
  Contains raw source tables (raw_customers, raw_products, raw_stores, raw_orders).  
  Data is not modified at this level — it serves as a single source of truth and a basis for full reloads.

- Stage Layer (`stg_`)  
  - Schema alignment  
  - Data cleaning  
  - Standardization of fields  
  - Unification of date formats and status values  

  This layer prepares the data for the star schema model.

- Core / Star Schema  
  The core model is implemented as a star schema and consists of four dimension tables and one fact table:
  - dim_customer  
  - dim_product  
  - dim_store  
  - dim_order  
  - fact_sales (sales fact table)

---

## 2. Fact Table: fact_sales

### Grain

Store × Product × Date

### Fields

- total_revenue  
- total_qty  
- orders_count  
- store_id, product_id, order_date, category and subcategory attributes  

### Rationale

- Easily answers most business questions: sales by store, product group, city, and date.  
- Reduces data volume compared to an order-line grain.  
- Well-suited for time-series analysis and aggregated reporting.

---

## 3. Dimension Tables

### dim_customer

Contains the customer profile: name, email, city, registration source, loyalty level, and status.  

Purpose: enables customer segmentation, RFM analysis, and retention analytics.

---

### dim_product

Describes the product: name, category, subcategory, size, base price, activity flag, and timestamps.  

Purpose: supports category-level sales analysis and SKU-level tracking.

---

### dim_store

Contains store attributes: name, city, address, opening/closing dates, status, and timezone.  

Purpose: supports regional analysis and correct handling of time-based reporting across time zones.

---

### dim_order

Captures order context: payment method, order status, promo code, date, and identifiers.  

Purpose: provides order-level detail without overloading the fact table, while still allowing deeper drill-down when needed.
