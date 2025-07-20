# 🍕 Pizza Sales Analytics Dashboard

An interactive Power BI dashboard built as part of an **end-to-end Azure Data Engineering project**, transforming raw pizza order data into actionable insights. The project showcases data ingestion, transformation, aggregation, and visualization using the Microsoft Azure ecosystem.

---

## 🔧 Project Architecture Overview

> ✅ **Data Source:** On-prem SQL Server  
> ✅ **Ingestion:** Azure Data Factory (Self-hosted Integration Runtime + Copy Activity)  
> ✅ **Storage:** Azure Blob Storage (Raw Zone)  
> ✅ **Transformation:** Azure Databricks (PySpark, Spark SQL)  
> ✅ **Aggregation:** Flattened tables with time-based metrics  
> ✅ **Visualization:** Power BI Desktop (.pbix)

---

## 📊 Dashboard Overview

This Power BI dashboard provides a 360° view of pizza sales trends, helping uncover performance insights across product categories, time, and customer patterns.

### 🔑 Key Performance Indicators (KPIs)

| Metric                             | Description                                                                |
| ---------------------------------- | -------------------------------------------------------------------------- |
| 📟 **Total Pizza Sold** (16K)      | Total number of pizzas sold.                                               |
| 👥 **Total Customers** (10K)       | Count of unique order IDs.                                                 |
| 💰 **Total Sales** (INR 267.46K)   | Revenue calculated as `unit_price × quantity`.                             |
| 📦 **Avg Order Value** (INR 26.7)  | Average spend per customer order.                                          |
| ⏰ **Peak Order Hour** (1 PM)      | Hour of day with the most orders placed.                                   |
| 🍕 **Top-Selling Pizza**           | The Classic Deluxe Pizza, based on revenue.                                |

---

## 🔽 Interactive Slicers

Enable data filtering and contextual insights:

- ✅ **Day of the Week** (Mon-Sun)  
- ✅ **Month** (Jan-Dec)  
- ✅ **Pizza Name**  
- ✅ **Pizza Category** (Veggie, Chicken Supreme, Classic, etc.)  
- ✅ **Pizza Size** (S, M, L, XL, XXL)

---

## 📈 Visual Components Breakdown

### 1. **Sales Breakdown by Pizza Size**
- **L (Large):** ₹126.0K (47.12%)
- **M (Medium):** ₹80.4K (30.05%)
- **S (Small):** ₹56.2K (21%)
- **XL & XXL:** ₹4.7K (1.74%)

### 2. **Total Revenue by Pizza Category**
- **Classic:** ₹73.53K
- **Chicken Supreme:** ₹65.60K
- **Veggie:** ₹65.58K
- **Others:** ₹62.75K

### 3. **Top Selling Pizzas by Revenue**
Top-performing pizzas:
- The Thai Chicken
- The Barbecue Chicken
- The Classic Deluxe
- The California Chicken
- The Four Cheese
- The Southwest Chicken

### 4. **Pizza Sales by Month**
- **Highest Month:** March (₹24.6K)
- **Monthly Revenue Range:** ₹21K–₹24K

### 5. **Weekly Order Trends**
- **Peak Days:** Friday (~2.7K) and Saturday (~2.4K)

---

## 🧠 Technical Highlights

### ✅ Data Aggregation in PySpark (Databricks)

#### 1. Load Raw Data
```python
df = spark.read.option("header", True).csv("/mnt/raw/pizzasales.csv", inferSchema=True)
```

#### 2. Add Time-Derived Columns
```python
from pyspark.sql.functions import col, hour, date_format, month

df = df.withColumn("order_hour", hour(col("order_time"))) \
       .withColumn("order_day", date_format("order_date", "EEEE")) \
       .withColumn("order_month", month(col("order_date"))) \
       .withColumn("day_name", date_format(col("order_date"), "EEEE")) \
       .withColumn("month_name", date_format(col("order_date"), "MMMM"))
```

#### 3. Calculate Total Price
```python
df = df.withColumn("total_price", col("unit_price") * col("quantity"))
```

#### 4. Create Aggregated Table
```python
agg_df = df.groupBy("pizza_name", "pizza_category", "pizza_size", "month_name", "day_name", "order_hour") \
           .agg(
               countDistinct("order_id").alias("total_orders"),
               sum("quantity").alias("total_pizza_sold"),
               sum("total_price").alias("total_sales")
           )
```

#### 5. Export to CSV
```python
agg_df.coalesce(1).write.option("header", True).csv("/mnt/aggregated/pizza_sales_agg.csv", mode="overwrite")
```

### ✅ Power BI Enhancements

- **DAX Filters:** Configured visual-level top-N filters.
- **Time Slicers:** Order hour-based slicers enable time-of-day analysis.
- **Custom Visual Formatting:** Transparent KPI cards, rounded borders, consistent themes.

---
## 🔍 Insights
- Identify top-selling pizzas for promotions.
- Optimize staff planning using peak hour insights.
- Segment sales by size, category, and time.
- Visualize growth trends for decision-making.


![Pizza Sales](https://github.com/rj14b/Pizza-Sales-Analysis/blob/main/image/Pizza%20Analysis.png?raw=true)
