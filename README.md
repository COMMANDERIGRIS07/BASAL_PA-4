# BASAL\_PA4 — README
2ECE-C

## Project Title

ECE Board Exam Performance Analysis (BASAL\_PA4)

## Overview

This Jupyter Notebook (`BASAL_PA4.ipynb`) analyzes an ECE board exam dataset (`board2.xlsx`). The notebook performs data loading, basic cleaning, simple exploratory analysis (filtering and computing averages), and creates boxplots to compare average grades across Track, Gender, and Hometown. This README explains how to run the notebook and documents each part of the code cell-by-cell so you (or a grader) can understand exactly what each step does.

> **Note:** While preparing this README I noticed some cells in the notebook contain ellipses (`...`) which may indicate truncated lines inserted as placeholders. If you see `...` inside a code cell, open the notebook and replace that placeholder with the intended full line. I include guidance below to help you reconstruct any accidentally cut lines.

---

## Files in this assignment

* `BASAL_PA4.ipynb` — main Jupyter notebook with the analysis.
* `board2.xlsx` — dataset (expected Excel file) used by the notebook.
* `board2.csv` — exported CSV created by the notebook when it runs `df.to_csv(...)`.

---

## Requirements

* Python 3.8+ (3.9 or 3.10 recommended)
* Jupyter / JupyterLab (to open the notebook)
* Packages (install with pip):

```bash
pip install pandas matplotlib seaborn openpyxl
```

* `openpyxl` is required for `pandas.read_excel()` to read `.xlsx` files.

---

## How to run

1. Place `BASAL_PA4.ipynb` and `board2.xlsx` in the same directory.
2. Launch Jupyter:

```bash
jupyter notebook BASAL_PA4.ipynb
```

3. Run cells from top to bottom (Shift+Enter) or use `Run All`.
4. After running, `board2.csv` will be created in the same folder, and plot windows will be shown inline.

---

## High-level notebook flow

1. Import libraries (pandas, matplotlib, seaborn).
2. Load Excel file into a `DataFrame` called `df`.
3. Export `df` to `board2.csv` for convenience.
4. Inspect the data (`head()`, `info()`, `isnull().sum()`).
5. Create filtered DataFrames (examples: `Vis` for Visayas with Math < 70; `Instru` for Instrumentation students from Luzon with Electronics > 70).
6. Clean column names (strip whitespace).
7. Create a `grade_cols` list (columns used to compute `Average`) and compute `Average` per row.
8. Produce boxplots of `Average` grouped by `Track`, `Gender`, and `Hometown`.
9. Read and display interpretation markdowns summarizing the findings.

---

## Notebook — cell-by-cell explanation

> I refer to cells in the order they appear when reading top-to-bottom in the notebook. If your notebook editor shows cell numbers, they may differ; use the textual descriptions to find each section.

### Cell: Imports

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

**Explanation:**

* `pandas` is used for tabular data manipulation.
* `matplotlib.pyplot` and `seaborn` are used for plotting. `seaborn` builds on `matplotlib` and gives nicer default graphics.

**Why these are first:** plotting and data operations need these packages loaded before any DataFrame or plotting commands.

---

### Cell: Load dataset

```python
df = pd.read_excel("board2.xlsx")
df
```

**Explanation:**

* Reads `board2.xlsx` into a DataFrame named `df` using `pandas.read_excel()`.
* The last line `df` in a Jupyter cell causes the notebook to display the DataFrame's representation. This is useful to visually confirm data loaded correctly.

**Hints:**

* If you see an error about engines, install `openpyxl` (`pip install openpyxl`).
* If `board2.xlsx` is not in the current working directory, provide the full/relative path.

---

### Cell: Export to CSV

```python
df.to_csv("board2.csv", index=False)
```

**Explanation:**

* Saves a CSV copy of the loaded DataFrame. `index=False` prevents pandas from writing the DataFrame index as a column in the CSV.

**Why this is helpful:** CSV is easier to share and open in many tools; this line creates a quick local copy.

---

### Cell: Quick preview

```python
df.head()
```

**Explanation:**

* Shows first 5 rows. Good for confirming column names, types and a quick check of sample rows.

---

### Cell: Data diagnostics

```python
df.info()
df.isnull().sum()
```

**Explanation:**

* `df.info()` prints index dtype, column dtypes, and non-null counts — useful for spotting unexpected types or missing values.
* `df.isnull().sum()` counts missing values per column. If you see non-zero counts, consider filling or dropping missing values before analysis.

---

### Cell: Filter — Visayas students with low Math

```python
Vis = df[(df["Hometown"] == "Visayas") & (df["Math"] < 70)][["Name", "Gender", "Track", "Math"]]
Vis.head()
```

**Explanation:**

* Creates a new DataFrame `Vis` containing only rows where `Hometown` equals `"Visayas"` AND `Math` is less than 70.
* Selects only the columns `Name`, `Gender`, `Track`, and `Math` for easier inspection.
* `Vis.head()` displays the first few rows of that filtered table.

**Use-case:** Useful to list students from Visayas who performed poorly in Math.

---

### Cell: Filter — Instrumentation students from Luzon with strong Electronics

```python
Instru = df[(df["Track"] == "Instrumentation") &
            (df["Hometown"] == "Luzon") &
            (df["Electronics"] > 70)][["Name", "GEAS", "Electronics"]]
Instru.head()
```

**Explanation:**

* Filters students whose `Track` is `Instrumentation`, `Hometown` is `Luzon`, and `Electronics` score is greater than 70.
* Selects `Name`, `GEAS`, and `Electronics` columns to review.
* Shows the first rows using `head()`.

**Note:** Some notebooks may have long filter expressions that wrap lines; make sure parentheses are properly balanced.

---

### Cell: Print / clean column names

```python
print(df.columns.tolist())
df.columns = df.columns.str.strip()
```

**Explanation:**

* Prints the list of column names so you can spot leading/trailing whitespace or typos.
* `df.columns.str.strip()` removes extra whitespace from column names — a common cause of KeyError when selecting columns.

**Recommendation:** Always clean column names when reading from external files, especially Excel.

---

### Cell: Prepare grade columns and compute Average

```python
grade_cols = ['Math', 'GEAS', 'Electronics']  # example
# compute a per-row average
df['Average'] = df[grade_cols].mean(axis=1)
# optional: display first rows
display(df.head(20))
```

**Explanation:**

* `grade_cols` should list the columns used to compute the student's average.
* `df[grade_cols].mean(axis=1)` computes the mean across these columns for each row; saved to a new column `Average`.
* Use `display()` (Jupyter) to show a nicely-formatted table.

**Important:** Make sure `grade_cols` matches the exact column names in your sheet (use `print(df.columns.tolist())` to verify). If scores are strings (e.g., with commas) convert them to numeric with `pd.to_numeric(..., errors='coerce')`.

---

### Cells: Boxplots (Track, Gender, Hometown)

Example for Track:

```python
plt.figure(figsize=(8,5))
sns.boxplot(x="Track", y="Average", data=df)
plt.title("Average Grades by Track")
plt.show()
```

For Gender:

```python
plt.figure(figsize=(5,4))
sns.boxplot(x="Gender", y="Average", data=df)
plt.title("Average Grades by Gender")
plt.show()
```

For Hometown:

```python
plt.figure(figsize=(6,4))
sns.boxplot(x="Hometown", y="Average", data=df)
plt.title("Average Grades by Hometown")
plt.show()
```

**Explanation:**

* Each `sns.boxplot` draws a boxplot of the `Average` score grouped by the specified categorical column.
* Boxplots let you compare medians (center line), interquartile ranges (box), and outliers.
* Use `figsize` to adjust plot size for readability in the notebook.

**Tips:**

* If categories have many levels, rotate x-axis labels with `plt.xticks(rotation=45)`.
* If data is sparse for some categories, consider using `stripplot` or `violinplot` as alternatives.

---

### Cells: Interpretations (markdown cells)

The notebook contains final markdown observations summarizing the plots. The statements include conclusions such as:

* Track appears to affect average grade (Instrumentation vs other tracks).
* Gender medians are similar with minor variation.
* Hometown (Luzon, Visayas, Mindanao) shows only small average differences.

**Explanation:** These short paragraphs are human-readable summaries interpreting the boxplots and averages. They help the grader understand your conclusions.

---

## Suggestions and improvements

1. **Handle missing values explicitly.** Eg:

```python
df[grade_cols] = df[grade_cols].apply(pd.to_numeric, errors='coerce')
df = df.dropna(subset=grade_cols)
```

2. **Add summary statistics** per group using `groupby`:

```python
df.groupby('Track')['Average'].describe()
```

3. **Add statistical tests** (e.g. ANOVA or Kruskal–Wallis) if you want to test whether differences across groups are statistically significant.

4. **Save plots** to files for submission:

```python
plt.savefig('avg_by_track.png', bbox_inches='tight')
```

5. **Fix any placeholder `...` lines** — ensure that long expressions are fully present and not truncated.

---

## Troubleshooting

* **KeyError when selecting columns:** verify exact column names using `print(df.columns.tolist())`. Extra spaces are common — use `df.columns = df.columns.str.strip()`.
* **TypeError/ValueError when averaging:** make sure score columns are numeric:

```python
df[grade_cols] = df[grade_cols].apply(pd.to_numeric, errors='coerce')
```

* **Empty DataFrame after filtering:** check that filter values match exactly (case-sensitive). Use `df['Hometown'].unique()` to inspect actual values.

---



*End of README*
