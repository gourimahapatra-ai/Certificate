# ❄️ Snowflake Cost Optimization & Best Practices Cheat Sheet

> 🎯 Goal: Maximize performance while minimizing cost in Snowflake

------------------------------------------------------------------------

## 🏗️ 1. Warehouse Optimization (Compute)

### ⚙️ Auto Suspend & Resume

-   Enable: AUTO_SUSPEND = 60--300\
    AUTO_RESUME = TRUE

-   Prevents idle compute cost

### ⏱️ Best Practice

-   Match suspend time with workload gaps
-   Avoid too low (frequent restart) or too high (idle cost)

### ⚡ Warehouse Sizing

-   Complex queries → Scale Up\
-   High concurrency → Scale Out (Multi-cluster)

------------------------------------------------------------------------

## 🗄️ 2. Table Design Best Practices

### 📊 Table Types

-   Permanent → Production\
-   Transient → Staging / Dev\
-   Temporary → Short-term

### 📦 Data Types

-   Use DATE instead of VARCHAR\
-   Use NUMBER for numeric data

### 📊 Clustering Keys

-   Use only for large tables with high scan %

------------------------------------------------------------------------

## 📊 3. Monitoring & Cost Control

### 🔍 Key Views

-   ACCOUNT_USAGE.TABLE_STORAGE_METRICS\
-   ACCOUNT_USAGE.QUERY_HISTORY\
-   ACCOUNT_USAGE.WAREHOUSE_METERING_HISTORY

### 📈 Monitor

-   Storage usage\
-   Query activity\
-   Warehouse credits

------------------------------------------------------------------------

## ⏳ 4. Time Travel & Retention

### 📊 Best Practices

-   Staging → 0 days\
-   Dev/Test → 0--1 day\
-   Production → 1--7 days

### 🚨 High-Churn Tables

-   Large + frequent updates → high storage cost\
-   Use transient tables if possible

------------------------------------------------------------------------

## 🚀 5. Bonus Tips

-   Use caching\
-   Avoid SELECT \*\
-   Use COPY INTO for bulk load

------------------------------------------------------------------------

## 🏆 Final Strategy

Optimize: - Compute\
- Storage\
- Monitoring\
- Retention

------------------------------------------------------------------------

## ✅ Checklist

-   Auto Suspend enabled\
-   Auto Resume enabled\
-   Correct warehouse size\
-   Optimized table design\
-   Monitoring setup\
-   Retention optimized

------------------------------------------------------------------------

✨ Pro Tip: Small optimizations can save huge costs at scale
