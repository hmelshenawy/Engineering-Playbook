# Pandas Cheatsheet

Quick reference for the Pandas operations I use most often.

---

## Read & Write

| Command                                | Hint       |
| -------------------------------------- | ---------- |
| `pd.read_csv("file.csv")`              | Read CSV   |
| `pd.read_excel("file.xlsx")`           | Read Excel |
| `df.to_csv("out.csv", index=False)`    | Save CSV   |
| `df.to_excel("out.xlsx", index=False)` | Save Excel |

---

## Inspect Data

| Command         | Hint           |
| --------------- | -------------- |
| `df.head()`     | First 5 rows   |
| `df.tail()`     | Last 5 rows    |
| `df.sample(5)`  | Random rows    |
| `df.shape`      | Rows & columns |
| `df.columns`    | Column names   |
| `df.info()`     | Data types     |
| `df.describe()` | Statistics     |

---

## Select Data

| Command                  | Hint                    |
| ------------------------ | ----------------------- |
| `df["col"]`              | Select one column       |
| `df[["a","b"]]`          | Select multiple columns |
| `df.loc[5]`              | Row by label            |
| `df.iloc[5]`             | Row by position         |
| `df.loc[df["age"] > 30]` | Filter rows             |

---

## Modify Data

| Command                            | Hint             |
| ---------------------------------- | ---------------- |
| `df["new"] = value`                | Create column    |
| `df.drop(columns=["col"])`         | Remove column    |
| `df.rename(columns={"old":"new"})` | Rename column    |
| `df.astype({"age":"int"})`         | Change data type |

---

## Missing Data

| Command           | Hint                   |
| ----------------- | ---------------------- |
| `df.isna().sum()` | Count missing values   |
| `df.dropna()`     | Remove missing rows    |
| `df.fillna(0)`    | Replace missing values |

---

## Sorting & Duplicates

| Command                                    | Hint              |
| ------------------------------------------ | ----------------- |
| `df.sort_values("price")`                  | Sort ascending    |
| `df.sort_values("price", ascending=False)` | Sort descending   |
| `df.drop_duplicates()`                     | Remove duplicates |

---

## Grouping

| Command                                                   | Hint           |
| --------------------------------------------------------- | -------------- |
| `df.groupby("brand").size()`                              | Count by group |
| `df.groupby("brand")["price"].mean()`                     | Average        |
| `df.groupby("brand").agg({"price":["min","max","mean"]})` | Multiple stats |

---

## Merge & Combine

| Command                   | Hint             |
| ------------------------- | ---------------- |
| `pd.concat([df1, df2])`   | Stack DataFrames |
| `df1.merge(df2, on="id")` | Join DataFrames  |

---

## Useful Operations

| Command                    | Hint                    |
| -------------------------- | ----------------------- |
| `df.value_counts()`        | Count unique values     |
| `df["col"].unique()`       | Unique values           |
| `df["col"].nunique()`      | Number of unique values |
| `df["col"].apply(func)`    | Apply function          |
| `df.query("price > 1000")` | SQL-like filtering      |

---

## Daily Commands

| Command           | Hint            |
| ----------------- | --------------- |
| `df.head()`       | Preview data    |
| `df.info()`       | Check structure |
| `df.describe()`   | Summary stats   |
| `df.isna().sum()` | Missing values  |
| `df.groupby()`    | Aggregate       |
| `df.merge()`      | Join data       |
| `df.to_csv()`     | Export results  |
