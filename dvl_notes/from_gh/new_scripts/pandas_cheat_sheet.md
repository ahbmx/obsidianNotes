# Pandas Python Cheat Sheet

## Importing Pandas
```python
import pandas as pd
```

## Creating Data Structures

### Series (1D)
```python
s = pd.Series(data, index=index)  # Create Series
s.values  # Get values as numpy array
s.index   # Get index
```

### DataFrame (2D)
```python
df = pd.DataFrame(data, columns=columns, index=index)  # Create DataFrame
df.columns  # Get column names
df.index    # Get index
df.values   # Get values as numpy array
```

## Reading/Writing Data

```python
# Reading
pd.read_csv('file.csv')           # From CSV
pd.read_excel('file.xlsx')        # From Excel
pd.read_json('file.json')         # From JSON
pd.read_sql(query, connection)    # From SQL

# Writing
df.to_csv('file.csv')             # To CSV
df.to_excel('file.xlsx')          # To Excel
df.to_json('file.json')           # To JSON
df.to_sql('table', connection)    # To SQL
```

## Viewing/Inspecting Data

```python
df.head(n)        # First n rows
df.tail(n)        # Last n rows
df.shape          # (rows, columns)
df.info()         # Data types, memory info
df.describe()     # Summary statistics
df.dtypes         # Data types per column
df.isnull()       # Boolean mask of null values
df.notnull()      # Opposite of isnull()
df['col'].unique()  # Unique values in column
df['col'].value_counts()  # Counts of unique values
```

## Selecting Data

### By Label
```python
df.loc[row_label, col_label]    # Select by label
df.loc[row_labels]              # Select rows
df.loc[:, col_labels]           # Select columns
```

### By Position
```python
df.iloc[row_pos, col_pos]       # Select by position
df.iloc[row_positions]          # Select rows
df.iloc[:, col_positions]       # Select columns
```

### Boolean Indexing
```python
df[df['col'] > 0.5]             # Rows where condition is met
df[df['col'].isin(values)]      # Rows where value is in list
df[(condition1) & (condition2)] # Multiple conditions
```

### Column Selection
```python
df['col']        # Get column as Series
df[['col1', 'col2']]  # Get multiple columns as DataFrame
```

## Data Cleaning

```python
df.fillna(value)            # Replace NaN with value
df.dropna()                # Drop rows with NaN
df.dropna(axis=1)          # Drop columns with NaN
df.drop_duplicates()       # Drop duplicate rows
df.replace(a, b)           # Replace a with b
df.rename(columns={'a':'b'})  # Rename columns
df.rename(index={'a':'b'})    # Rename index
df.set_index('col')        # Set column as index
df.reset_index()           # Reset index to integers
```

## Filtering, Sorting, Grouping

```python
df.sort_values('col')              # Sort by column
df.sort_values('col', ascending=False)  # Sort descending
df.nlargest(n, 'col')              # Top n values
df.nsmallest(n, 'col')             # Bottom n values
df.groupby('col')                  # Group by column
df.groupby('col').mean()           # Group and aggregate
df.groupby(['col1', 'col2']).sum() # Multiple group columns
```

## Applying Functions

```python
df['col'].apply(func)      # Apply function to column
df.apply(func, axis=0)     # Apply to each column (default)
df.apply(func, axis=1)     # Apply to each row
df.applymap(func)          # Apply to each element
```

## Combining DataFrames

```python
pd.concat([df1, df2])                # Concatenate vertically
pd.concat([df1, df2], axis=1)        # Concatenate horizontally
df1.append(df2)                      # Append rows (deprecated)
pd.merge(df1, df2, on='col')         # Merge on column
pd.merge(df1, df2, left_on='col1', right_on='col2')  # Merge on different columns
df1.join(df2, on='col')              # Join on index/column
```

## Reshaping Data

```python
df.pivot(index='col1', columns='col2', values='col3')  # Pivot table
df.melt(id_vars=['col1'], value_vars=['col2'])        # Unpivot
pd.crosstab(df['col1'], df['col2'])                   # Cross-tabulation
```

## Time Series

```python
pd.to_datetime(df['col'])            # Convert to datetime
df.set_index('datetime_col')         # Set datetime index
df.resample('D').mean()              # Resample daily
df.rolling(window=3).mean()          # Rolling average
```

## Plotting (requires matplotlib)

```python
df.plot()                  # Line plot
df.plot.bar()              # Bar plot
df.plot.hist()             # Histogram
df.plot.box()              # Box plot
df.plot.scatter(x='col1', y='col2')  # Scatter plot
df.plot.pie(y='col')       # Pie chart
```



# Pandas String Manipulation Cheat Sheet

Pandas provides powerful vectorized string operations through the `str` accessor. Here's a comprehensive guide to string manipulation in Pandas:

## Basic String Operations

```python
import pandas as pd

# Create a sample Series
s = pd.Series(['apple', 'banana', 'cherry', 'date'])

# Case conversion
s.str.lower()       # Convert to lowercase
s.str.upper()       # Convert to uppercase
s.str.title()       # Convert to title case
s.str.capitalize()  # Capitalize first letter

# Length of strings
s.str.len()         # Get length of each string

# Count occurrences
s.str.count('a')    # Count 'a's in each string

# Check content
s.str.contains('a')     # Boolean if contains 'a'
s.str.startswith('a')   # Boolean if starts with 'a'
s.str.endswith('e')     # Boolean if ends with 'e'
```

## String Slicing and Splitting

```python
# Slicing
s.str[0:3]          # Get first 3 characters
s.str[-3:]          # Get last 3 characters

# Splitting
s.str.split('a')    # Split on 'a'
s.str.split('a', expand=True)  # Split into multiple columns
s.str.split('a', n=1)  # Split with max splits

# Partitioning
s.str.partition('a')  # Split into 3 parts (before, sep, after)
s.str.rpartition('a') # Split from right
```

## String Replacement

```python
# Simple replacement
s.str.replace('a', 'X')      # Replace 'a' with 'X'
s.str.replace('[aeiou]', '', regex=True)  # Remove vowels

# Advanced regex replacement
s.str.replace(r'^(\w{3})', r'\1-', regex=True)  # Add hyphen after first 3 chars

# Multiple replacements
replacements = {'a': 'X', 'e': 'Y'}
s.str.replace('|'.join(replacements.keys()), 
              lambda m: replacements[m.group(0)], regex=True)
```

## String Joining and Concatenation

```python
# Join elements with separator
s.str.cat(sep=', ')  # 'apple, banana, cherry, date'

# Concatenate Series with separator
s1 = pd.Series(['a', 'b', 'c'])
s2 = pd.Series(['1', '2', '3'])
s1.str.cat(s2, sep='-')  # 'a-1', 'b-2', 'c-3'

# Concatenate columns in DataFrame
df = pd.DataFrame({'A': ['a', 'b'], 'B': ['1', '2']})
df['A'].str.cat(df['B'], sep='-')  # 'a-1', 'b-2'
```

## String Padding and Alignment

```python
# Padding
s.str.pad(width=10)          # Pad to width 10 (right align)
s.str.pad(width=10, side='both')  # Pad both sides
s.str.center(10)             # Center align

# Z-fill (zero padding)
pd.Series(['1', '22', '333']).str.zfill(3)  # '001', '022', '333'

# Strip whitespace
s = pd.Series(['  apple  ', 'banana\t', '\ncherry'])
s.str.strip()   # Remove leading/trailing whitespace
s.str.lstrip()  # Remove leading whitespace
s.str.rstrip()  # Remove trailing whitespace
```

## String Testing and Extraction

```python
# Testing
s.str.isalpha()     # All alphabetic characters?
s.str.isnumeric()   # All numeric characters?
s.str.isalnum()     # Alphanumeric?
s.str.isdigit()     # All digits?
s.str.isspace()     # All whitespace?
s.str.islower()     # All lowercase?
s.str.isupper()     # All uppercase?

# Pattern extraction
s = pd.Series(['apple123', 'banana456', 'cherry789'])
s.str.extract('([a-z]+)(\d+)')  # Extract letters and numbers
s.str.extract('(?P<letters>[a-z]+)(?P<numbers>\d+)')  # Named groups

# Find positions
s.str.find('a')     # First position of 'a' (-1 if not found)
s.str.rfind('a')    # Last position of 'a'
```

## Advanced String Operations

```python
# Get dummy variables from strings
s = pd.Series(['a|b', 'b|c', 'a|c'])
s.str.get_dummies(sep='|')  # Creates dummy columns for each value

# Repeat strings
s.str.repeat(3)  # Repeat each string 3 times

# String slicing with get
s.str.get(0)     # First character of each string
s.str[0]         # Same as above

# Normalize strings
s.str.normalize('NFKD')  # Unicode normalization
```

## Working with Missing Values

```python
s = pd.Series(['apple', None, 'banana', np.nan])

# String operations handle NA by default
s.str.upper()  # Returns NA for missing values

# Replace missing values before operations
s.fillna('').str.upper()  # Convert empty strings instead of NA
```

## Performance Tip

For very large datasets, consider converting to categorical type if you have many repeated strings:

```python
s = s.astype('category')  # More memory efficient for repeated strings
```



# Pandas Date/Time Cheat Sheet

## Importing and Setup
```python
import pandas as pd
import numpy as np
```

## Creating Timestamps and Ranges

### Basic Creation
```python
pd.Timestamp('2023-01-01')        # Single timestamp
pd.Timestamp('2023-01-01 12:00')  # With time
pd.Timestamp(2023, 1, 1)          # From components

pd.to_datetime('2023-01-01')      # Convert string to datetime
pd.to_datetime(['2023-01-01', '2023-01-02'])  # Convert list to DatetimeIndex
```

### Date Ranges
```python
pd.date_range('2023-01-01', periods=5)              # 5 days
pd.date_range('2023-01-01', '2023-01-05')           # Explicit end
pd.date_range('2023-01-01', periods=5, freq='H')    # Hourly
pd.date_range('2023-01-01', periods=5, freq='2D')   # Every 2 days
pd.bdate_range('2023-01-01', periods=5)             # Business days only
```

### Common Frequencies
```python
'H'   # Hourly
'D'   # Daily
'B'   # Business day
'W'   # Weekly
'M'   # Month end
'MS'  # Month start
'Q'   # Quarter end
'QS'  # Quarter start
'A'   # Year end
'AS'  # Year start
```

## Working with DataFrames

### Converting Columns
```python
df['date'] = pd.to_datetime(df['date_col'])  # Convert column to datetime
df = df.set_index('date')                   # Set as index
```

### Accessing Components
```python
df.index.year           # Year component
df.index.month          # Month (1-12)
df.index.day            # Day of month
df.index.hour           # Hour (0-23)
df.index.minute         # Minute (0-59)
df.index.second         # Second (0-59)
df.index.dayofweek      # Day of week (0-6, Monday=0)
df.index.dayofyear      # Day of year
df.index.weekofyear     # Week of year
df.index.quarter        # Quarter
df.index.is_month_start # Boolean if first day of month
df.index.is_month_end   # Boolean if last day of month
```

## Time Delta Operations

### Creating Time Deltas
```python
pd.Timedelta('1 days')      # 1 day
pd.Timedelta('1 days 2 hours')  # 1 day and 2 hours
pd.Timedelta(hours=3)       # 3 hours
pd.Timedelta(weeks=2)       # 2 weeks
```

### Arithmetic Operations
```python
df.index + pd.Timedelta('1D')     # Add 1 day
df.index - pd.Timedelta('3H')     # Subtract 3 hours
df['end'] - df['start']           # Calculate time differences
```

## Resampling and Frequency Conversion

### Downsampling
```python
df.resample('D').mean()       # Daily mean
df.resample('W').sum()        # Weekly sum
df.resample('M').first()      # First value each month
df.resample('Q').last()       # Last value each quarter
```

### Upsampling
```python
df.resample('H').asfreq()     # Hourly frequency (no interpolation)
df.resample('H').ffill()      # Forward fill
df.resample('H').bfill()      # Backward fill
df.resample('H').interpolate()# Linear interpolation
```

## Window Operations

### Rolling Windows
```python
df.rolling(window=3).mean()       # 3-period rolling mean
df.rolling(window='7D').sum()     # 7-day rolling sum
df.rolling(window=5, min_periods=1).mean()  # Partial windows
```

### Expanding Windows
```python
df.expanding().sum()              # Cumulative sum
df.expanding().mean()             # Cumulative mean
```

## Time Zone Handling

### Time Zone Conversion
```python
df.index.tz_localize('UTC')               # Set timezone
df.index.tz_convert('US/Eastern')         # Convert timezone
pd.Timestamp('2023-01-01').tz_localize('US/Pacific')  # Localize
```

### Common Time Zones
```python
'UTC'
'US/Eastern'
'US/Central'
'US/Pacific'
'Europe/London'
'Asia/Tokyo'
```

## Periods and Period Arithmetic

### Working with Periods
```python
pd.Period('2023-01', freq='M')    # January 2023
pd.period_range('2023-01', periods=12, freq='M')  # Monthly periods

# Convert datetime to period
df.index.to_period('M')           # Convert to monthly periods
```

### Period Arithmetic
```python
p = pd.Period('2023-01', freq='M')
p + 1                            # February 2023
p - 2                            # November 2022
```

## Time Series Selection

### Partial String Indexing
```python
df['2023']                # All of 2023
df['2023-01':'2023-03']   # Jan-Mar 2023
df['2023-01-15':'2023-01-20']  # Specific date range
```

### First/Last Occurrences
```python
df.at_time('09:30')       # All rows at 9:30 AM
df.between_time('09:00', '16:00')  # Between times
df.first('5D')            # First 5 days
df.last('2W')             # Last 2 weeks
```

## Time Series Visualization

```python
import matplotlib.pyplot as plt

df.plot()                 # Basic line plot
df['column'].plot()       # Plot single column
plt.show()
```

## Performance Tips

```python
# Faster parsing with format
pd.to_datetime(df['date'], format='%Y-%m-%d')  # Specify format

# Cache converted dates
df['date'] = pd.to_datetime(df['date'], cache=True)
```
