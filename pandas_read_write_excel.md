---
title: "Excel Processing using Pandas"
date: 2021-06-11T00:48:23+08:00
draft: false
tags: [pandas]
categories: [Python]
---

A brief summary of how to read and write excel files using Pandas package.

<!--more-->

First we need to install pandas and pre-requisite:

```
pip install pandas
pip install openpyxl  # in order to read xlsx files
pip install xlrd
```

# Read xlsx files

Note that in order to read xlsx files, we need to install [openpyxl](https://openpyxl.readthedocs.io/en/stable/) and use it like this:

```python
import pandas as pd

df = pd.read_excel('test.xlsx', sheet_name=0, engine='openpyxl')
```

For param `sheet_name`, either the literal sheet name or the sheet index is okay.
`sheet_name=0` means to read the 1st sheet.

# Convert text in each cell to string type

When the sheet contains date and numbers, `read_excel()` will convert it to Timestamps and numbers by default,
if we would like to convert all text to strings, we can use the `dtype` parameter:

```python
df = pd.read_excel('test.xlsx', sheet_name=0, dtype=str, engine='openpyxl')
```

# Read empty cell as empty string

It seems that pandas will by default read empty cell as `NAN` values.
To read empty cell as empty string, use `keep_default_na=False` when reading excel:

```python
df = pd.read_excel('test.xlsx', sheet_name=0, dtype=str, engine='openpyxl', keep_default_na=False)
```

Or after reading the sheet, use [`fillna()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html#pandas-dataframe-fillna) to replace the nan values with empty string:

```python
df = pd.read_excel('test.xlsx', sheet_name=0, dtype=str, engine='openpyxl').fillna('')
```

# Convert panda data frame to numpy array

Use `to_numpy()`:

```python
df = pd.read_excel('test.xlsx', sheet_name=0, dtype=str, engine='openpyxl').fillna('')
data = df.to_numpy()  # data is now numpy array
```

# Convert list to excel files

To convert a list of list (each sub-list has the same number of elements) to excel files, use [`to_excel()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_excel.html):

```python
import pandas as pd

my_list = [[1, 2, 3], [4, 5, 6]]
df = pd.DataFrame(my_list)

df.to_excel('my_list.xlsx')
```

To remove row index, use `index=False`:

```python
df.to_excel('my_list.xlsx', index=False)
```

To add header for each column, we can provide a list of strings for each column using `header` parameter.

```python
import  pandas  as  pd
my_list  =  [[1,  2,  3],  [4,  5,  6]]
df = pd.DataFrame(my_list)

header = ['c1', 'c2', 'c3']
df.to_excel('my_list.xlsx', index=False, header=header)
```

# References

+ pandas can not open xlsx files: https://stackoverflow.com/q/65250207/6064933
+ pandas replace nan with blank:
    + https://stackoverflow.com/q/45148292/6064933
    + https://stackoverflow.com/q/10867028/6064933
+ pandas how to read cell value as string: https://stackoverflow.com/q/32591466/6064933
+ Remove row index: https://stackoverflow.com/q/22089317/6064933
