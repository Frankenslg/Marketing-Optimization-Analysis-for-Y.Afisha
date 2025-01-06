# 🚀 **Marketing Optimization Analysis for Y.Afisha**

## 📝 **Introduction**

In this project, we analyze the data of Y.Afisha, a company aiming to optimize its marketing expenses. The focus is on understanding user interactions with the platform, identifying revenue contributions, and evaluating when revenues surpass customer acquisition costs. The analysis is based on data related to website visits, orders, and marketing expenses spanning from January 2017 to December 2018.

---

## 📚 **Libraries Used**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy import stats as st
```

---

## 📥 **Data Loading**
```python
# Loading datasets
visits = pd.read_csv('/datasets/visits_log_us.csv')
orders = pd.read_csv('/datasets/orders_log_us.csv')
costs = pd.read_csv('/datasets/costs_us.csv')
```

---

## 🛠️ **Data Preparation**

### 🧹 **Data Cleaning**
- **Datetime Conversion:**
```python
visits['Start Ts'] = pd.to_datetime(visits['Start Ts'])
visits['End Ts'] = pd.to_datetime(visits['End Ts'])
orders['Buy Ts'] = pd.to_datetime(orders['Buy Ts'])
costs['dt'] = pd.to_datetime(costs['dt'])
```

- **Duplicates Removal:**
```python
visits = visits.drop_duplicates()
orders = orders.drop_duplicates()
costs = costs.drop_duplicates()
```

- **Null Values Handling:**
```python
# Checking for null values
print(visits.isnull().sum())
print(orders.isnull().sum())
print(costs.isnull().sum())

# Dropping null values
visits = visits.dropna()
orders = orders.dropna()
costs = costs.dropna()
```

- **Descriptive Statistics:**
```python
print(visits.describe(include='all', datetime_is_numeric=True))
print(orders.describe(include='all', datetime_is_numeric=True))
print(costs.describe(include='all', datetime_is_numeric=True))
```

### 🔍 **Observations**
1. The datasets were free from duplicates and null values post-cleaning.
2. A general understanding of key metrics (e.g., unique users, order revenues, marketing costs) was established for further analysis.

---

## 📊 **Analysis and Metrics Calculation**

### 1️⃣ **Visits Analysis**

#### **Daily, Weekly, and Monthly Visits:**
```python
visits['date'] = visits['Start Ts'].dt.date
# Aggregating visits
daily_visits = visits.groupby('date')['Uid'].nunique().reset_index()
weekly_visits = visits.resample('W-Mon', on='Start Ts')['Uid'].nunique().reset_index()
monthly_visits = visits.resample('M', on='Start Ts')['Uid'].nunique().reset_index()
```

#### **Session Metrics:**
```python
visits['session_duration'] = (visits['End Ts'] - visits['Start Ts']).dt.seconds
```

#### **Visualizations:**
```python
plt.figure(figsize=(14, 7))
# Daily Visits
plt.subplot(2, 2, 1)
plt.plot(daily_visits['date'], daily_visits['Uid'])
plt.title('Daily Unique Visits')
plt.xlabel('Date')
plt.ylabel('Users')
# Session Durations
plt.subplot(2, 2, 2)
plt.hist(visits['session_duration'], bins=50)
plt.title('Session Durations')
plt.xlabel('Seconds')
plt.ylabel('Frequency')
plt.tight_layout()
plt.show()
```

#### **Key Insights:**
1. **Fluctuations in Unique Visits:** Peaks observed during specific dates.
2. **Session Durations:** Short sessions dominate, indicating quick user actions.

### 2️⃣ **Order Analysis**

#### **Order Metrics:**
```python
orders['order_date'] = orders['Buy Ts'].dt.date
# Aggregating orders
orders_per_day = orders.groupby('order_date')['Uid'].count().reset_index()
```

#### **Visualizations:**
```python
plt.figure(figsize=(14, 7))
# Daily Orders
plt.subplot(2, 2, 1)
plt.plot(orders_per_day['order_date'], orders_per_day['Uid'])
plt.title('Daily Orders')
plt.xlabel('Date')
plt.ylabel('Number of Orders')
plt.tight_layout()
plt.show()
```

#### **Key Insights:**
1. Significant daily variations in the number of orders.
2. Revenue spikes correlate with specific user activities.

### 3️⃣ **Marketing Analysis**

#### **Calculating CAC (Cost per Acquisition):**
```python
orders_with_source = pd.merge(orders, visits[['Uid', 'Source Id']], on='Uid', how='left')
orders_with_source.rename(columns={'Source Id': 'source_id'}, inplace=True)

# Costs per source
costs_per_source = costs.groupby('source_id')['costs'].sum().reset_index()
# User count per source
user_counts = orders_with_source.groupby('source_id')['Uid'].nunique().reset_index()
# Calculating CAC
cac_per_source = pd.merge(costs_per_source, user_counts, on='source_id', how='left')
cac_per_source['cac'] = cac_per_source['costs'] / cac_per_source['Uid']
```

#### **Visualizations:**
```python
plt.figure(figsize=(14, 7))
# CAC by Source
plt.bar(cac_per_source['source_id'], cac_per_source['cac'])
plt.title('Cost per Acquisition (CAC) by Source')
plt.xlabel('Source ID')
plt.ylabel('CAC')
plt.tight_layout()
plt.show()
```

#### **Key Insights:**
1. Certain sources have disproportionately high CAC.
2. Budget allocation strategies need optimization.

---

## 🏁 **Conclusion**

Through this project, we:
1. Cleaned and prepared the datasets for analysis.
2. Explored user behavior patterns through visits, orders, and session metrics.
3. Calculated marketing efficiency metrics such as CAC.

### **Recommendations:**
- Optimize marketing budgets by focusing on sources with low CAC and high ROI.
- Enhance user engagement to increase session durations and order frequency.
- Continuously monitor and adjust marketing strategies based on real-time data.

---

© 2025 | Francisco SLG | TripleTen

