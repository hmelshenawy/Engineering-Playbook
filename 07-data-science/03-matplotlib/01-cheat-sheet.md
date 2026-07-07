# Matplotlib Cheatsheet

Quick reference for the Matplotlib commands I use most often.

---

## Basic Plot

| Command                           | Hint            |
| --------------------------------- | --------------- |
| `import matplotlib.pyplot as plt` | Standard import |
| `plt.plot(x, y)`                  | Line chart      |
| `plt.scatter(x, y)`               | Scatter plot    |
| `plt.bar(x, y)`                   | Bar chart       |
| `plt.hist(x)`                     | Histogram       |
| `plt.show()`                      | Display chart   |

---

## Titles & Labels

| Command                 | Hint             |
| ----------------------- | ---------------- |
| `plt.title("Title")`    | Add chart title  |
| `plt.xlabel("X Label")` | Add x-axis label |
| `plt.ylabel("Y Label")` | Add y-axis label |
| `plt.legend()`          | Show legend      |
| `plt.grid(True)`        | Show grid        |

---

## Figure Size

| Command                       | Hint                |
| ----------------------------- | ------------------- |
| `plt.figure(figsize=(10, 5))` | Set chart size      |
| `plt.tight_layout()`          | Fix spacing         |
| `plt.savefig("chart.png")`    | Save chart as image |

---

## Multiple Lines

| Command                      | Hint        |
| ---------------------------- | ----------- |
| `plt.plot(x, y1, label="A")` | First line  |
| `plt.plot(x, y2, label="B")` | Second line |
| `plt.legend()`               | Show labels |

---

## Subplots

| Command                    | Hint                   |
| -------------------------- | ---------------------- |
| `fig, ax = plt.subplots()` | Create figure and axis |
| `ax.plot(x, y)`            | Plot using axis object |
| `ax.set_title("Title")`    | Axis title             |
| `ax.set_xlabel("X")`       | Axis x-label           |
| `ax.set_ylabel("Y")`       | Axis y-label           |

---

## Pandas Integration

| Command                        | Hint                   |
| ------------------------------ | ---------------------- |
| `df["col"].plot()`             | Quick line plot        |
| `df["col"].hist()`             | Quick histogram        |
| `df.plot(x="date", y="sales")` | Plot DataFrame columns |
| `df.plot(kind="bar")`          | Bar chart              |

---

## Daily Commands

| Command                       | Hint          |
| ----------------------------- | ------------- |
| `plt.figure(figsize=(10, 5))` | Start chart   |
| `plt.plot(x, y)`              | Line chart    |
| `plt.scatter(x, y)`           | Scatter chart |
| `plt.bar(x, y)`               | Bar chart     |
| `plt.hist(x)`                 | Distribution  |
| `plt.title()`                 | Add title     |
| `plt.xlabel()`                | Label x-axis  |
| `plt.ylabel()`                | Label y-axis  |
| `plt.tight_layout()`          | Clean layout  |
| `plt.show()`                  | Display       |
