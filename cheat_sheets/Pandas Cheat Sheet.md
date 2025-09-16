## 📦 Importing Pandas

```python
import pandas as pd
import numpy as np
```

---

## 📁 Creating DataFrames

```python
# From dict
data = {'Name': ['Alice', 'Bob'], 'Age': [25, 30]}
df = pd.DataFrame(data)

# From CSV
df = pd.read_csv('file.csv')

# From Excel
df = pd.read_excel('file.xlsx')

# From NumPy array
df = pd.DataFrame(np.random.randn(5, 3), columns=['A', 'B', 'C'])
```

---

## 📋 Basic Operations

```python
df.head()          # First 5 rows
df.tail(10)        # Last 10 rows
df.shape           # (rows, columns)
df.info()          # Summary
df.describe()      # Descriptive stats
df.columns         # Column names
df.index           # Row indices
df.dtypes          # Data types
```

---

## 🔍 Selecting and Querying Data

### ✅ Column(s)

```python
df['col']                   # Single column (Series)
df[['col1', 'col2']]        # Multiple columns (DataFrame)
```

### ✅ Row(s)

```python
df.loc[0]                   # By label/index
df.iloc[0]                  # By integer position
df.loc[0:3]                 # Slice by label (inclusive)
df.iloc[0:3]                # Slice by position (exclusive)
```

### ✅ Conditional Selection

```python
df[df['Age'] > 25]
df[(df['Age'] > 25) & (df['Gender'] == 'F')]
```

### ✅ Query API

```python
df.query('Age > 25 and Gender == "F"')
```

---

## 🧹 Cleaning Data

```python
df.dropna()                         # Drop rows with NaN
df.fillna(0)                        # Fill NaNs with 0
df.fillna(method='ffill')          # Forward fill
df.isna()                           # Boolean mask of NaNs
df.drop_duplicates()               # Remove duplicate rows
df.rename(columns={'A': 'Alpha'})  # Rename columns
df.replace({'old': 'new'})         # Replace values
df.astype({'col': 'int'})          # Change type
```

---

## 🧮 Aggregations

```python
df['Age'].mean()
df['Score'].sum()
df.groupby('Gender').mean()
df.groupby(['Gender', 'Dept']).agg({'Score': 'max', 'Age': 'mean'})
df.pivot_table(values='Score', index='Gender', columns='Dept', aggfunc='mean')
```

---

## 🧵 String Operations

```python
df['Name'].str.lower()
df['Name'].str.upper()
df['Name'].str.contains('Al')
df['Name'].str.replace('a', '@', case=False)
df['Name'].str.strip()         # Remove whitespace
df['Email'].str.split('@', expand=True)
```

---

## 📅 Date/Time Operations

```python
df['Date'] = pd.to_datetime(df['Date'])
df['Date'].dt.year
df['Date'].dt.month
df['Date'].dt.day
df['Date'].dt.weekday
df['Date'].dt.strftime('%Y-%m-%d')     # Format date
df.set_index('Date', inplace=True)
df.resample('M').mean()                # Resample monthly
```

---

## 🔗 Merge / Join

```python
# Inner join
pd.merge(df1, df2, on='id', how='inner')

# Left / Right / Outer join
pd.merge(df1, df2, on='id', how='left')
pd.merge(df1, df2, on='id', how='right')
pd.merge(df1, df2, on='id', how='outer')

# Merge on multiple keys
pd.merge(df1, df2, on=['id', 'date'])
```

---

## 🔗 Concatenate DataFrames

```python
# Row-wise
pd.concat([df1, df2], axis=0)

# Column-wise
pd.concat([df1, df2], axis=1)

# Ignore index
pd.concat([df1, df2], ignore_index=True)
```

---

## 🔄 Sorting

```python
df.sort_values('Age')
df.sort_values(['Age', 'Name'], ascending=[True, False])
df.sort_index()
```

---

## 🔄 Apply and Map

```python
df['new'] = df['old'].apply(lambda x: x * 2)
df['col'].map({'A': 1, 'B': 2})         # Map values
df.apply(np.mean, axis=0)              # Column-wise
df.apply(np.mean, axis=1)              # Row-wise
```

---

## 📌 Useful One-Liners

```python
df.sample(n=5)                         # Random sample
df.nlargest(3, 'Score')               # Top 3 scores
df.nsmallest(3, 'Score')              # Bottom 3 scores
df.memory_usage(deep=True)           # Memory usage
df.corr()                             # Correlation matrix
```

---
## 🧭 `.loc[]` — Label-based Selection

### 🔹 Syntax

```python
df.loc[row_label, column_label]
```

### ✅ 1. **Selecting Rows by Label**

```python
df.loc[5]                # Row with index label 5
df.loc[[2, 5, 7]]        # Rows with labels 2, 5, 7
df.loc[2:5]              # Rows from label 2 to 5 (inclusive)
```

### ✅ 2. **Selecting Rows and Columns**

```python
df.loc[2, 'Name']                         # Single value
df.loc[2:4, 'Name']                       # Column for rows 2 to 4
df.loc[2:4, ['Name', 'Age']]              # Multiple columns
df.loc[:, ['Name', 'Age']]               # All rows, specific columns
```

### ✅ 3. **Using Boolean Conditions with `.loc[]`**

```python
df.loc[df['Age'] > 30]                          # Filter rows
df.loc[df['Age'] > 30, ['Name', 'Age']]         # Filter + select columns
```

### ✅ 4. **Setting Values with `.loc[]`**

```python
df.loc[3, 'Age'] = 35                           # Set value
df.loc[df['Name'] == 'Alice', 'Age'] = 28       # Conditional update
```

### ✅ 5. **Using Callable Functions**

```python
df.loc[lambda df: df['Score'] > 90]
```

---

## 🧪 Example DataFrame

```python
data = {
    'Name': ['Alice', 'Bob', 'Charlie', 'David'],
    'Age': [25, 30, 35, 40],
    'Score': [85, 90, 95, 100]
}
df = pd.DataFrame(data, index=['a', 'b', 'c', 'd'])
```

```python
df.loc['b']                     # Row with index 'b'
df.loc[['a', 'c'], 'Score']    # Score of 'a' and 'c'
df.loc['a':'c', ['Name', 'Age']]  # Slice by label
```

---

