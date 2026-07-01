# Chapter 5: GroupBy Operations in Pandas

## Introduction

One of the most powerful features of Pandas is the ability to group data and perform aggregate calculations on different categories. Whether you're analyzing sales by region, employee performance by department, or weather data by city, the `groupby()` method allows you to summarize large datasets efficiently.

Internally, Pandas follows the **Split → Apply → Combine** strategy, where data is first divided into groups, an operation is performed on each group, and the results are then combined into a single output.

This chapter introduces the fundamentals of `groupby()`, explores commonly used aggregation methods, and demonstrates how to create custom grouping logic using user-defined functions and lambda expressions.

---

## Learning Objectives

By the end of this chapter, you will be able to:

- Understand how the `groupby()` operation works.
- Create GroupBy objects from a DataFrame.
- Retrieve individual groups using `get_group()`.
- Perform aggregate calculations such as mean, maximum, minimum, and count.
- Differentiate between `size()` and `count()`.
- Generate descriptive statistics for grouped data.
- Understand the Split → Apply → Combine paradigm.
- Apply custom grouping logic using functions and lambda expressions.

---

## Topics Covered

- Creating a GroupBy object
- `groupby()`
- `get_group()`
- `max()`
- `min()`
- `mean()`
- `describe()`
- `size()`
- `count()`
- Split → Apply → Combine
- Custom grouping
- Lambda functions
- User-defined functions

---

# Understanding `groupby()`

The `groupby()` method groups rows based on one or more columns, allowing aggregate operations to be performed on each group independently.

### Syntax

```python
df.groupby(by)
```

### Example

```python
g = df.groupby("city")
```

This creates a **GroupBy object**, which stores the grouped data but does not immediately perform any calculations.

---

# Working with GroupBy Objects

Once a GroupBy object has been created, you can perform several operations.

### Retrieve a Specific Group

```python
g.get_group("Mumbai")
```

Returns all rows that belong to the specified group.

> **Note:** The argument passed to `get_group()` should be an actual value from the grouped column (e.g., `"Mumbai"`), not the column name itself.

---

# Common Aggregation Methods

## Maximum Value

```python
g.max()
```

Returns the maximum value for each numeric column within every group.

---

## Minimum Value

```python
g.min()
```

Returns the minimum value for each numeric column within every group.

---

## Mean

```python
g.mean()
```

Calculates the average of numeric columns for each group.

If only specific columns are required:

```python
g[["temperature", "windspeed"]].mean()
```

---

## Count

```python
g.count()
```

Counts the number of **non-missing** values in each column for every group.

---

## Size

```python
g.size()
```

Returns the total number of rows in each group, including rows containing missing values.

### Difference Between `count()` and `size()`

| `count()` | `size()` |
|-----------|-----------|
| Counts non-null values | Counts all rows |
| Works column-wise | Returns total rows per group |
| Ignores missing values | Includes missing values |

---

## Describe

```python
g.describe()
```

Generates descriptive statistics for each group, including:

- Count
- Mean
- Standard Deviation
- Minimum
- Quartiles
- Maximum

---

# Split → Apply → Combine

Every GroupBy operation follows the same three-step process.

## 1. Split

The dataset is divided into groups based on one or more columns.

Example:

```
City

Mumbai
Delhi
Mumbai
Delhi
Pune
```

becomes

```
Mumbai → Rows belonging to Mumbai

Delhi → Rows belonging to Delhi

Pune → Rows belonging to Pune
```

---

## 2. Apply

An operation is performed independently on each group.

Examples include:

- Mean
- Sum
- Count
- Maximum
- Minimum
- Custom functions

---

## 3. Combine

The results from each group are combined into a single DataFrame or Series.

This entire workflow is known as the **Split → Apply → Combine** strategy and forms the foundation of grouped data analysis in Pandas.

---

# Custom Grouping Using Functions

In real-world data analysis, predefined aggregation functions are often insufficient. Analysts frequently need to categorize or transform data according to business-specific rules.

For example, instead of grouping temperatures by city, you may want to group them into categories such as:

| Temperature | Category |
|-------------|----------|
| 80–90 | Group A |
| 60–79 | Group B |
| Below 60 | Group C |

To accomplish this, you can define your own function and pass it to `groupby()`.

Example approach:

```python
def classify_temperature(temp):
    ...
```

The function can then be used with a lambda expression or directly within a grouping operation to assign each record to a custom category.

This technique is especially useful for creating business-specific classifications that are not explicitly present in the original dataset.

---

# Real-World Applications

GroupBy is one of the most frequently used operations in data analysis.

Some common applications include:

### Sales Analysis

- Revenue by region
- Monthly sales by product
- Store-wise performance

### Human Resources

- Average salary by department
- Employee count by designation
- Experience analysis

### Weather Analysis

- Average temperature by city
- Maximum rainfall by state
- Wind speed analysis

### Customer Analytics

- Customer segmentation
- Average purchase value by category
- Region-wise customer statistics

### Business Intelligence

- KPI aggregation
- Executive dashboards
- Performance reporting
- Operational summaries

---

# Notebook Contents

This notebook demonstrates:

- Creating GroupBy objects
- Accessing groups using `get_group()`
- Performing aggregation operations
- Using `count()` and `size()`
- Generating descriptive statistics
- Applying custom grouping functions
- Using lambda expressions with GroupBy

---

# Key Takeaways

- `groupby()` is one of the most powerful data analysis operations in Pandas.
- GroupBy objects follow the **Split → Apply → Combine** paradigm.
- Aggregate functions such as `mean()`, `max()`, and `count()` summarize grouped data efficiently.
- `count()` and `size()` serve different purposes and should be used appropriately.
- Custom functions enable flexible grouping based on business logic.
- GroupBy operations are extensively used in reporting, analytics, and Business Intelligence projects.

---

# Prerequisites

Before working through this chapter, you should be familiar with:

- Pandas DataFrames
- Selecting rows and columns
- Basic DataFrame operations
- Handling missing values
- Basic Python functions and lambda expressions

---

# Next Chapter

In the next chapter, you'll learn how to combine multiple datasets using **`concat()`** and **`merge()`**, two essential operations for joining and integrating data from different sources.
