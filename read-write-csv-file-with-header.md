---
title: "How to Read and Write CSV Files in Python"
date: 2018-05-13 21:24:42 +0800
tags: [pandas]
categories: [Python]
---

In this post, I will summarize the most convenient way to read and write CSV files (with or w/o headers) in Python.

<!-- more -->

# Write CSV files

## Use the builtin CSV writer

Python has a built-in [CSV module](https://docs.python.org/3/library/csv.html#module-csv) that deals with CSV files.
In order to write to files in CSV format, we build a CSV writer, then write to a file using this writer.
Here is a simple example:

```python
import csv

lines = [['Bob', 'male', '27'],
['Smith', 'male', '26'],
['Alice', 'female', '26']]

header = ['name', 'gender', 'age']

with open("test.csv", "w", newline='') as f:
    writer = csv.writer(f, delimiter=',')
    writer.writerow(header) # write the header
    # write the actual content line by line
    for l in lines:
        writer.writerow(l)
    # or we can write in a whole
    # writer.writerows(lines)
```

In the above code snippet, [the `newline` parameter inside the `open` method is important](https://docs.python.org/3/library/csv.html#csv.writer).
If you do not use `newline=''`, there will an extra blank line after each line on Windows platform.
The parameter `delimiter` is used to denote the delimiter between different columns.

## Use pandas

Another way to generate csv files is to use [to_csv](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_csv.html) from pandas, which is easier to use.
Here is an example using pandas:

```python
import pandas as pd

lines = [['Bob', 'male', '27'],
['Smith', 'male', '26'],
['Alice', 'female', '26']]

header = ['name', 'gender', 'age']
new_df = pd.DataFrame(data=lines, columns=header)

new_fname = "test.csv"
new_df.to_csv(new_fname, sep=",", index=False)
```

The parameter `index=False` disable adding row index to each row, which is often desired.
`sep` is used to change the separation character between columns.

# Read CSV files

## Use the CSV reader

CSV module provides [a CSV reader](https://docs.python.org/3/library/csv.html#reader-objects), which we can use to read the CSV files.
The CSV reader is an iterable object. We can use the following snippet to read CSV files:

```python
import csv

with open("test.csv", "r", newline='') as f:
    reader = csv.reader(f, delimiter=',')
    for l in reader:
        print(l) # l will be a Python list
```

## Use Pandas

The famous data processing library [Pandas](https://pandas.pydata.org/) also provides a method [`read_csv()`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html) to read CSV files.

For example, in order to read the above `test.csv` file, we can use the following code:

```python
import pandas as pd

df = pd.read_csv('test.csv', delimiter=',') # df is Pandas dataframe
```

The `df` in the above code will be Pandas `dataframe` object.
If the csv file you have does not have header, you should use `header=None` when reading this file:

```python
df = pd.read_csv("test.csv", delimiter=',', header=None)
```

To show the number of rows in the dataframe, use `len(df.index)` or `df.shape[0]`.
To show the number of columns, use `len(df.columns)` or `df.shape[1]`.

To get a certain column, we use the column name as key:

```python
col0 = df['name'] # col0 is Pandas Series object
print(col0.tolist()) # use tolist() method to make a list
```

The [`tolist()`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.tolist.html#pandas-series-tolist) method in the above code convert Pandas Series to plain Python list.
If the csv file does not have a header, you can also use column index to access a certain column.
For example, to get column 0, you can use:

+ `df[0].values` (this is numpy array)
+ `df[0].tolist()` (plain Python list)

To access a certain row, we can use the [`loc`](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.loc.html#pandas.DataFrame.loc) method with the row number.

```python
row0 = df.loc[0] # row0 is Pandas Series object
df.loc[0].values # a numpy array
print(row0.tolist()) # use tolist() method to make a list
```

To get the whole dataframe as a numpy ndarray, you can use `df.values`.

# References

+ [Indexing and selecting data in Pandas](https://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-and-selecting-data)
+ [Get the number of rows or columns in dataframe](https://stackoverflow.com/questions/15943769/how-do-i-get-the-row-count-of-a-pandas-dataframe)
