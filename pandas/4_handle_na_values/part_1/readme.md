# 4. Handle NA Values — Part 1

## Introduction

One of the biggest differences between toy datasets and real-world datasets is the presence of missing data.

In an ideal world, every customer record would be complete, every sensor would report perfectly, and every spreadsheet would be filled correctly. In reality, datasets often contain missing values because of:

* Human errors during data entry
* System failures
* Incomplete surveys
* Data migration issues
* Missing measurements from sensors or devices
* Data collected from multiple sources

In pandas, missing values are usually represented as:

```python
import numpy as np

np.nan
```

or

```python
pd.NA
```

As a data analyst or data scientist, handling missing values correctly is an important skill because the strategy you choose can directly impact the quality of your analysis and the performance of your machine learning models.

In this chapter, we will cover three important approaches for dealing with missing data:

1. `fillna()`
2. `dropna()`
3. `interpolate()`

---

# Understanding Missing Values

Let's start with a simple DataFrame.

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "Temperature": [32, np.nan, 28, 30],
    "Humidity": [70, 75, np.nan, 80]
})

df
```

Output:

| Temperature | Humidity |
| ----------- | -------- |
| 32          | 70       |
| NaN         | 75       |
| 28          | NaN      |
| 30          | 80       |

Here, `NaN` stands for "Not a Number" and is pandas' default representation for missing numerical values.

---

# Method 1: Filling Missing Values using `fillna()`

The most common way to deal with missing values is to replace them with some other value.

Pandas provides the `fillna()` method for this purpose.

## Filling with a Constant Value

```python
df.fillna(0)
```

Output:

| Temperature | Humidity |
| ----------- | -------- |
| 32          | 70       |
| 0           | 75       |
| 28          | 0        |
| 30          | 80       |

This is probably the most commonly used approach by beginners.

However, it is not always the best approach.

---

# Why Filling with 0 is Often a Bad Idea

Many beginners automatically write:

```python
df.fillna(0)
```

because it is simple and convenient.

The problem is that **0 may carry a completely different meaning than a missing value**.

Consider the following examples:

### Example 1: Salary Data

| Salary |
| ------ |
| 50000  |
| NaN    |
| 60000  |

If we replace the missing value with 0:

| Salary |
| ------ |
| 50000  |
| 0      |
| 60000  |

Now it appears that one employee earns no salary at all, which is likely incorrect.

---

### Example 2: Temperature Data

| Temperature |
| ----------- |
| 35          |
| NaN         |
| 30          |

Replacing the missing value with 0 suggests that the temperature suddenly dropped to freezing point.

Again, this may be completely unrealistic.

---

### Example 3: Customer Age

| Age |
| --- |
| 25  |
| NaN |
| 30  |

Replacing the missing value with 0 would suggest a customer is newborn.

Clearly incorrect.

---

# Why Mean or Median is Often Better

Instead of introducing unrealistic values, we often use a representative value from the existing data.

## Using Mean

```python
df["Temperature"].fillna(df["Temperature"].mean())
```

The mean represents the average value of the column.

This approach works well when:

* Data is relatively symmetric
* There are no extreme outliers
* Average values make sense for the business problem

Example:

| Temperature |
| ----------- |
| 32          |
| NaN         |
| 28          |
| 30          |

Mean:

```python
(32 + 28 + 30) / 3 = 30
```

Result:

| Temperature |
| ----------- |
| 32          |
| 30          |
| 28          |
| 30          |

---

## Using Median

```python
df["Salary"].fillna(df["Salary"].median())
```

Median is often preferred when the dataset contains outliers.

Example:

| Salary  |
| ------- |
| 50000   |
| 55000   |
| 60000   |
| 1000000 |
| NaN     |

Mean:

```python
291250
```

Median:

```python
57500
```

The median better represents the typical salary because it is less affected by extreme values.

### Rule of Thumb

Use:

* **Mean** when data is fairly symmetric
* **Median** when data contains outliers or is skewed

---

# Forward Fill (`ffill`)

Sometimes the previous valid value can be used to fill missing values.

This technique is called **Forward Fill**.

```python
df.ffill()
```

or

```python
df.fillna(method="ffill")
```

*(Older syntax still seen in many tutorials.)*

---

## Example

| Sales |
| ----- |
| 100   |
| NaN   |
| NaN   |
| 120   |

After forward fill:

| Sales |
| ----- |
| 100   |
| 100   |
| 100   |
| 120   |

The previous valid value is carried forward.

---

## When to Use Forward Fill

Forward fill is useful when:

* Time-series data
* Stock prices
* Sensor readings
* Sequential observations

Basically, when the previous value remains valid until a new value appears.

---

# Backward Fill (`bfill`)

Backward fill works in the opposite direction.

```python
df.bfill()
```

or

```python
df.fillna(method="bfill")
```

---

## Example

| Sales |
| ----- |
| 100   |
| NaN   |
| NaN   |
| 120   |

After backward fill:

| Sales |
| ----- |
| 100   |
| 120   |
| 120   |
| 120   |

The next valid value is copied backward.

---

## When to Use Backward Fill

Backward fill is useful when:

* Future values are more reliable
* Data was recorded late
* Missing observations should inherit the next available value

---

# Filling Across Columns

Most people use forward fill and backward fill along rows.

However, pandas can also fill horizontally across columns.

Consider:

| A  | B   | C  |
| -- | --- | -- |
| 10 | NaN | 30 |

Now execute:

```python
df.bfill(axis="columns")
```

Output:

| A  | B  | C  |
| -- | -- | -- |
| 10 | 30 | 30 |

What happened?

Pandas moved horizontally from left to right and filled the missing value in column B using the next available value in column C.

---

# Using the `limit` Parameter

Sometimes you do not want pandas to fill every missing value.

Suppose:

| Sales |
| ----- |
| 100   |
| NaN   |
| NaN   |
| NaN   |
| 120   |

Now run:

```python
df.ffill(limit=1)
```

Output:

| Sales |
| ----- |
| 100   |
| 100   |
| NaN   |
| NaN   |
| 120   |

Only the first missing value is filled.

The remaining missing values stay untouched.

---

## Why Use `limit`?

Without a limit, a value may be propagated too far.

Imagine:

* A sensor stopped working for 3 days
* You don't want the first reading to fill the entire outage period

In such cases, `limit` provides better control.

Example:

```python
df.bfill(limit=2)
```

Only the first two missing values will be filled.

---

# Method 2: Interpolation

Sometimes neither forward fill nor backward fill is appropriate.

Instead, we want to estimate what the missing value should have been.

This is where interpolation becomes useful.

---

# What is Interpolation?

Interpolation estimates missing values using nearby observations.

Think of it as drawing a line between known points and estimating values along that line.

---

## Weather Example

Suppose:

| Day       | Temperature |
| --------- | ----------- |
| Monday    | 32          |
| Tuesday   | NaN         |
| Wednesday | 28          |

A reasonable estimate for Tuesday might be:

```python
30
```

because the temperature appears to be gradually decreasing.

This is exactly what linear interpolation does.

---

## Using `interpolate()`

```python
df.interpolate()
```

Example:

| Temperature |
| ----------- |
| 32          |
| NaN         |
| 28          |

Output:

| Temperature |
| ----------- |
| 32          |
| 30          |
| 28          |

---

# When Should You Use Interpolation?

Interpolation is useful when:

* Data is numeric
* Data has an order
* Missing values occur between valid observations
* Smooth transitions are expected

Examples:

* Weather data
* Sensor measurements
* Time-series data
* Stock market indicators
* Traffic measurements

---

# When Should You Avoid Interpolation?

Avoid interpolation for:

* Names
* Categories
* Product IDs
* Customer IDs
* Text data

Example:

| Product Category |
| ---------------- |
| Electronics      |
| NaN              |
| Furniture        |

There is no meaningful way to interpolate between categories.

Interpolation only makes sense for numerical data.

---

# Method 3: Dropping Missing Values

Sometimes the simplest solution is to remove missing data completely.

Pandas provides:

```python
df.dropna()
```

---

## Example

Before:

| Name  | Age |
| ----- | --- |
| John  | 25  |
| Sarah | NaN |
| Mike  | 30  |

After:

```python
df.dropna()
```

Result:

| Name | Age |
| ---- | --- |
| John | 25  |
| Mike | 30  |

Rows containing missing values are removed.

---

# When Should You Drop Missing Values?

Dropping rows is appropriate when:

* Missing values are rare
* Dataset is large
* Missing rows are not important
* Missing information cannot be reliably estimated

For example:

A dataset contains 1 million records.

Only 100 rows contain missing values.

Dropping those rows is usually acceptable.

---

# The `how` Parameter

`dropna()` allows us to control the conditions under which rows are removed.

---

## `how="any"`

```python
df.dropna(how="any")
```

Drop the row if **any column contains a missing value**.

Example:

| A   | B   |
| --- | --- |
| 1   | 2   |
| NaN | 3   |
| 4   | NaN |

Result:

| A | B |
| - | - |
| 1 | 2 |

Since every other row contains at least one missing value, they are removed.

---

## `how="all"`

```python
df.dropna(how="all")
```

Drop rows only if **every value is missing**.

Example:

| A   | B   |
| --- | --- |
| NaN | NaN |
| 1   | NaN |
| 2   | 3   |

Result:

| A | B   |
| - | --- |
| 1 | NaN |
| 2 | 3   |

Only the completely empty row is removed.

---

# The `thresh` Parameter

Sometimes we want to keep rows that contain enough valid information.

For this purpose, pandas provides the `thresh` parameter.

```python
df.dropna(thresh=3)
```

This means:

> Keep rows having at least 3 non-null values.

---

## Example

| A | B | C   | D   |
| - | - | --- | --- |
| 1 | 2 | NaN | NaN |
| 1 | 2 | 3   | NaN |
| 1 | 2 | 3   | 4   |

Using:

```python
df.dropna(thresh=3)
```

Result:

| A | B | C | D   |
| - | - | - | --- |
| 1 | 2 | 3 | NaN |
| 1 | 2 | 3 | 4   |

The first row is removed because it contains only two valid values.

---

# Summary

Missing values are unavoidable in real-world datasets, and there is no single strategy that works for every situation.

### Use `fillna()`

When you know a reasonable replacement value.

### Use Mean

When the data is fairly symmetric.

### Use Median

When the data contains outliers.

### Use Forward Fill (`ffill`)

When previous observations remain valid.

### Use Backward Fill (`bfill`)

When future observations provide a better estimate.

### Use `limit`

When you want to control how many missing values get filled.

### Use `interpolate()`

When numerical values change gradually and you want to estimate values between known observations.

### Use `dropna()`

When removing incomplete records is preferable to making assumptions.

### Use `how` and `thresh`

When you need more control over which rows or columns should be removed.

There is no universally correct approach for handling missing values. The best strategy depends on the business problem, the nature of the data, and the impact that missing values may have on downstream analysis.
