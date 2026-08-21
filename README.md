# Churn-Analysis-Practice-Project

## 💼 Business Problem

Customer churn is a major business challenge because losing customers can lead to reduced revenue, increased customer acquisition costs, and lower long-term customer value.

The business needs to understand why customers are leaving, which types of customers are more likely to churn, and which factors are associated with customer churn.

The key business problem is to:

* Identify customers and customer segments with a higher risk of churn.
* Understand how **plan type, subscription type, tenure, complaints, escalations, and other customer attributes** relate to churn.
* Identify the amount of **revenue at risk due to customer cancellations**.
* Detect churn patterns and trends that can help the business take action before customers leave.
* Develop **targeted retention strategies** instead of applying the same approach to every customer.

### 🎯 Business Objective

The objective of this analysis is to turn customer data into **actionable insights that can help the business identify high-risk customers, understand the reasons and patterns behind churn, and take proactive measures to improve customer retention and protect revenue**.

---------------

## 📌 Project Overview

This project focuses on analyzing **customer churn** using Python, Pandas, NumPy, Matplotlib, Seaborn, and SQLite.

The analysis combines customer, subscription, and customer-support data to identify churn patterns, calculate important business metrics, engineer useful features, and visualize factors associated with customer churn.

---

# ⭐ STAR Method

## 🟢 S — Situation

Customer churn is an important business problem because losing customers can directly affect revenue and long-term customer relationships.

The available data was distributed across multiple tables containing information about:

* Customer details
* Subscription information
* Customer support interactions
* Complaints and escalations
* Churn scores
* Monthly charges
* Customer tenure

The raw data also contained issues such as:

* Unnecessary columns
* Inconsistent categorical values
* Missing country information
* Incorrect/inconsistent date data types
* Duplicate customer-support records

Therefore, the data needed to be cleaned, combined, and analyzed before meaningful churn insights could be extracted.

---

## 🟡 T — Task

The objective of this project was to build an end-to-end **Customer Churn Analysis** workflow using Python.

The main goals were to:

1. Import data from a SQLite database.
2. Inspect the available tables and columns.
3. Clean and standardize the datasets.
4. Handle missing values and duplicate records.
5. Combine customer, subscription, and support data.
6. Create features required for churn analysis.
7. Calculate key customer and business metrics.
8. Analyze churn across different customer segments.
9. Examine the relationship between escalations and churn.
10. Create visualizations to identify churn patterns and trends.
11. Export the final cleaned dataset for further analysis.

---

## 🔵 A — Action

### 1. Data Import & Database Exploration

Connected Python to a SQLite database using `sqlite3` and dynamically identified the available tables.

Each database table was loaded into a separate Pandas DataFrame for analysis.

```python
conn = sqlite3.connect('customer_churn.db')
```

I also inspected table structures and column names using SQL and SQLite's `PRAGMA table_info()`.

---

### 2. Data Cleaning

Performed several data-cleaning operations on the customer, subscription, and support datasets.

#### Customer Data

* Renamed `name` to `customer_name`
* Removed unnecessary columns such as `pincode` and `interests`
* Converted `dob` into a datetime format
* Standardized gender values:

  * `Women → Female`
  * `Men → Male`
* Filled missing country values using a **state → country mapping**

#### Subscription Data

Converted subscription-related date columns into datetime format:

* `subscription_start_date`
* `renewal_date`
* `cancellation_date`

#### Customer Support Data

Removed unnecessary columns such as:

* `col_1`
* `comment`

The support data was also examined for duplicate customer records.

---

### 3. Data Integration

Merged the three major datasets using `customerid`:

```python
df = (
    df_db_subscription
    .merge(df_db_customer, on='customerid', how='left')
    .merge(df_db_support, on='customerid', how='left')
)
```

Duplicate customer-support records were handled by:

1. Calculating the complaint count per customer.
2. Sorting records by complaint date.
3. Keeping the latest support record for each customer.
4. Re-merging the cleaned support data with the other datasets.

---

### 4. Feature Engineering

Created several analytical features to support churn analysis.

#### Churn Flag

A binary churn indicator was created based on whether a customer had a cancellation date.

```python
df['churn_flag'] = np.where(
    df['cancellation_date'].notna(),
    1,
    0
)
```

* `1` → Customer churned
* `0` → Customer did not churn

#### Customer Tenure

Calculated customer tenure in days.

For churned customers, tenure was calculated from:

**Cancellation Date − Subscription Start Date**

For active customers:

**Current Date − Subscription Start Date**

#### Churn Risk

Created three churn-risk categories using the existing `churn_score`:

| Churn Score | Risk Level |
| ----------- | ---------- |
| `< 50`      | Low        |
| `50–69`     | Medium     |
| `≥ 70`      | High       |

---

### 5. Business Metrics

Calculated several key metrics to understand customer behavior and business performance.

### 📈 Churn Rate

Calculated the percentage of customers who churned.

### 📉 Retention Rate

Calculated as:

```text
Retention Rate = 100 − Churn Rate
```

### 💰 ARPU

Calculated **Average Revenue Per User (ARPU)** using average monthly charges.

### ⏳ Average Customer Tenure

Calculated the average number of days customers remained subscribed.

### 💸 Revenue at Risk

Calculated the total monthly charges associated with churned customers.

### 📞 Escalation Rate

Calculated the percentage of records containing an escalation.

### 🗣️ Average Complaints Per User

Calculated the average number of complaints per customer.

### 🔗 Escalation vs Churn Correlation

Converted escalation values from categorical values (`Y/N`) into numeric values and calculated their correlation with the churn flag.

---

### 6. Churn Analysis

Analyzed churn across multiple dimensions, including:

* Plan type
* State
* Subscription type

For state and subscription type, the analysis included:

* Churn rate
* Total revenue
* User count

This helped compare customer churn behavior across different segments.

---

### 7. Data Visualization

Created multiple visualizations using **Matplotlib and Seaborn**.

#### 📅 Monthly Churn Trend

Created a monthly trend showing the number of churned customers over time.

#### 📊 Churn by Plan Type

Compared churn rates across different plans.

#### 🗺️ Churn by State

Visualized churn rates across states.

#### 🔥 Correlation Heatmap

Created a correlation matrix involving variables such as:

* Plan type
* Contract type
* Churn score
* Churn flag
* Churn risk
* Escalations

Categorical variables were encoded using predefined category orders before calculating correlations.

#### 🔎 Pairplot

Used a pairplot to examine pairwise relationships between selected variables.

#### 📊 Catplot

Compared monthly charges across:

* Plan type
* Gender
* Churn-risk category

#### 📋 Pivot Table

Created a multi-metric pivot table containing:

* Total monthly charges
* Unique customers
* Average churn flag

---

### 8. Exporting the Dataset

After cleaning and feature engineering, the final dataset was exported as:

```text
exported_churn_data.csv
```

This makes the processed data available for further analysis or visualization.

---

## 🔴 R — Result

### 🔍 Key Findings

The analysis helped identify several factors and customer segments associated with churn:

* **Churn is not evenly distributed across customers**, with differences observed across plan types, subscription types, and geographical segments.
* **Customers with higher churn-risk scores** can be identified and prioritized for early intervention.
* **Customer support activity**, including complaints and escalations, was analyzed to understand its relationship with customer churn.
* **Customer tenure** was analyzed to identify how long customers typically remain subscribed before cancellation.
* **Revenue at risk** was calculated to understand the potential monthly revenue impact of customer churn.
* **Monthly churn trends** were analyzed to identify periods where customer cancellations increased.
* The analysis also examined relationships between **churn, customer behavior, support interactions, and other customer attributes**.

### 💡 Recommended Actions to Reduce Churn

Based on these findings, the business can take a proactive approach to customer retention:

#### 1. 🎯 Target High-Risk Customers

Use the churn-risk categories to identify customers who are more likely to churn.

**Action:**

* Prioritize high-risk customers for retention campaigns.
* Offer personalized support or incentives before cancellation occurs.
* Monitor high-risk customers regularly.

#### 2. 📞 Improve Customer Support

Customers with frequent complaints or escalations should receive additional attention.

**Action:**

* Identify customers with repeated complaints.
* Resolve escalated issues quickly.
* Track unresolved complaints and follow up with affected customers.
* Analyze recurring complaint reasons to identify service problems.

#### 3. 💰 Focus on Revenue at Risk

Instead of looking only at the number of customers likely to churn, prioritize customers who represent significant revenue.

**Action:**

* Identify high-value customers at risk of churn.
* Provide personalized retention offers.
* Assign dedicated support to valuable at-risk customers.

#### 4. 📊 Monitor High-Churn Segments

The analysis of plan type, subscription type, and state can help identify customer segments with comparatively higher churn.

**Action:**

* Investigate why certain segments experience higher churn.
* Review pricing, service quality, and customer experience in those segments.
* Design targeted retention strategies instead of using the same approach for every customer.

#### 5. 📅 Monitor Churn Trends

Monthly churn analysis can help identify periods when cancellations increase.

**Action:**

* Track churn monthly through a dashboard.
* Investigate sudden increases in cancellations.
* Identify events, pricing changes, service issues, or seasonal factors that may contribute to increased churn.

### 🎯 Business Impact

The analysis transforms raw customer and support data into **actionable retention insights**.

Instead of waiting for customers to cancel, the business can:

**Identify → Prioritize → Intervene → Monitor**

This allows the company to focus its retention efforts on customers who are most likely to churn and have the greatest potential impact on revenue.

---

# 🛠️ Technologies Used

| Technology           | Purpose                                      |
| -------------------- | -------------------------------------------- |
| **Python**           | Data analysis and processing                 |
| **Pandas**           | Data cleaning, transformation and analysis   |
| **NumPy**            | Numerical operations and feature engineering |
| **Matplotlib**       | Data visualization                           |
| **Seaborn**          | Statistical visualization                    |
| **SQLite**           | Database storage and querying                |
| **Jupyter Notebook** | Development and analysis environment         |

---

# Author 
*Riya Bhatt*

# linkedIn
*https://www.linkedin.com/in/riyabhattz/*




