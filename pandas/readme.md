# Pandas Learning Path

## Beginner

### 01 Series & DataFrame Basics

Topics covered:

- Reading CSV files
- Creating Series
- Creating DataFrames
- Basic analysis
- Aggregations
- Movie dataset exploration

Notebook:
- 01_series_dataframe_basics.ipynb

---

### 02 DataFrame Basics

Topics covered:

#### DataFrame Functions

- shape
- columns
- apply
- set_index
- loc
- iloc
- describe
- info

# Chapter 3 — Handling Missing Data

This chapter covers the practical ways to deal with null or invalid values in pandas.

## What this chapter covers

Missing values are common in real datasets. Before doing analysis or building models, you often need to decide whether to fill, remove, or estimate missing data.

In this chapter, we cover:

- `fillna()` / `ffill()` / `bfill()` for replacing missing values
- Why filling with `0` is often a weak default
- When mean or median is a better choice
- Forward fill and backward fill, including filling across rows and columns
- Using `limit` to control how many missing values get filled
- `interpolate()` for estimating values between known points
- `dropna()` for removing missing values when appropriate
- `any`, `all`, and `thresh` for controlling how strictly missing rows or columns are dropped

## Files in this section

- `4_handle_na_values_part_1/README.md` — concept explanation and examples
- `4_handle_na_values_part_1/notebook.ipynb` — hands-on code examples

#### Column Functions

- unique
- value_counts
- max
- min

#### Filtering

- Row filtering
- Column filtering

Notebook:
- 02_dataframe_basics.ipynb
