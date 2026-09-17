# ECE 2112: Advanced Computer Programming and Algorithms

**Made by:** Kennette L. Garcia | **Section:** 2ECE-B  
**Repository Content:** Programming Assignment No. 4 (*Experiment 4: Data Wrangling and Data Visualization*)

The primary objective of this repository is to demonstrate practical tabular data manipulation, conditional subsetting, statistical aggregation, and exploratory data visualization using the **Pandas** and **Matplotlib** libraries on an academic board exam dataset (`board2.xlsx`).

---

### Intended Learning Outcomes (Objectives)
* **Filter tabular data** using combined categorical and numerical criteria.
* **Construct focused DataFrames** through selective feature projection.
* **Summarize group-level relationships** between categorical attributes and continuous target metrics.
* **Communicate comparative distributions** effectively through clean, standardized graphical plots.

---

### A. Visayas Communication DataFrame

Load the source dataset (`board2.xlsx`) and isolate records corresponding strictly to students based in the **Visayas** region who are enrolled under the **Communication** track. The extracted subset is projected onto a specific feature order: `Name`, `Gender`, `Math`, `Electronics`, and a computed `Average` column representing their performance across the two engineering subjects.

**Logic:**
* `pd.read_excel()`: Ingests the raw spreadsheet data into a primary DataFrame without manual entry.
* **Bitwise Boolean Filtering (`&`)**: Combines categorical constraints `(board['Track'] == 'Communication') & (board['Hometown'] == 'Visayas')` to evaluate rows element-wise.
* `.loc[:, [...]]`: Slices and rearranges target features into the required presentation schema.
* `.mean(axis=1)`: Calculates horizontal (row-wise) arithmetic means across `Math` and `Electronics`.
* `.shape[0]`: Extracts the row cardinality of the filtered dataset.

```
import pandas as pd

board = pd.read_excel("board2.xlsx")
A = board.loc[(board['Track'] == 'Communication') & (board['Hometown'] == 'Visayas')]
Viscomm = A.loc[:, ['Name', 'Gender', 'Math', 'Electronics']]
Viscomm['Average'] = Viscomm[['Math', 'Electronics']].mean(axis=1)
Viscomm
Viscomm.shape[0]
```
**Observation:**

The resulting Viscomm DataFrame contains 5 records, reflecting a diverse distribution of scores with regional averages spanning from 51.50 to 78.00.

### B. Visayas Female DataFrame
Isolate female candidates originating from Visayas while retaining only their identifier, specialization track, and applied science/engineering marks (Name, Track, GEAS, Electronics, Average). A secondary threshold filter is subsequently applied to highlight students attaining an average grade of 60 or higher without mutating or overwriting the parent subset.

**Logic:**
- Multi-condition Boolean Indexing: Simultaneously isolates records where Hometown == 'Visayas' and Gender == 'Female'.
- Feature Projection: Employs .loc to discard redundant demographic data and focus exclusively on core evaluation subjects (GEAS and Electronics).
- Non-destructive Conditional Slicing: Evaluates VisFemale['Average'] >= 60 in a distinct execution step to preserve the integrity of the original VisFemale table.

```
B = board.loc[(board['Track'] == 'Communication') & (board['Hometown'] == 'Visayas') & (board['Gender'] == 'Female')]
VisFemale = B.loc[:, ['Name', 'Track', 'GEAS', 'Electronics']]
VisFemale['Average'] = VisFemale[['GEAS', 'Electronics']].mean(axis=1)
VisFemale
VisFemale.loc[(VisFemale['Average'] >= 60)]
```

**Observation:**
The baseline VisFemale table identifies 2 candidate rows (S11 and S22). Upon applying the conditional cutoff greater than or equal to 60, only student S22 qualifies with an average score of 64.00, driven significantly by a high GEAS performance of 89.C. Category-Average Visualization & Group AnalysisCompute the comprehensive four-subject overall average (Math, Electronics, GEAS, and Communication) for all records. Using group-by aggregation, compute the sample mean score across the categorical dimensions Track, Gender, and Hometown. The comparisons are visualized side-by-side on a unified multi-panel figure, followed by an automated determination of the top-performing categories.

**Logic:**
- board[['Math', ...]].mean(axis=1): Computes an unbiased horizontal aggregate score per individual across all 4 exam components.
- .groupby('<Feature>')['Average'].mean(): Splits the dataset into distinct categorical buckets and aggregates their respective overall mean performances.
- plt.subplots(1, 3, figsize=(20, 5)): Instantiates a structured $1 \times 3$ grid figure canvas for synchronized comparative review.
- .plot(kind='bar', ax=axes[i]): Renders distinct bar charts onto designated axis objects.
- .idxmax(): Programmatically queries the maximum category index label per categorical summary series.

### C. Category-Average Visualization & Group Analysis
Compute the comprehensive four-subject overall average (Math, Electronics, GEAS, and Communication) for all records. Using group-by aggregation, compute the sample mean score across the categorical dimensions Track, Gender, and Hometown. The comparisons are visualized side-by-side on a unified multi-panel figure, followed by an automated determination of the top-performing categories.

**Logic:**
- board[['Math', ...]].mean(axis=1): Computes an unbiased horizontal aggregate score per individual across all 4 exam components.
- .groupby('<Feature>')['Average'].mean(): Splits the dataset into distinct categorical buckets and aggregates their respective overall mean performances
- .plt.subplots(1, 3, figsize=(20, 5)): Instantiates a structured $1 \times 3$ grid figure canvas for synchronized comparative review.
- .plot(kind='bar', ax=axes[i]): Renders distinct bar charts onto designated axis objects.
- .idxmax(): Programmatically queries the maximum category index label per categorical summary series.
```
import matplotlib.pyplot as plt

board['Average'] = board[['Math', 'Electronics', 'GEAS', 'Communication']].mean(axis=1)

C = board.groupby('Track')['Average'].mean()
gender = board.groupby('Gender')['Average'].mean()
hometown = board.groupby('Hometown')['Average'].mean()

fig, axes = plt.subplots(1, 3, figsize=(20, 5))
C.plot(kind='bar', ax=axes[0])
gender.plot(kind='bar', ax=axes[1])
hometown.plot(kind='bar', ax=axes[2])

highest_C = C.idxmax()
highest_gender = gender.idxmax()
highest_hometown = hometown.idxmax()

print(f"{highest_C} has the highest sample mean Average among the Track categories.")
print(f"{highest_gender} has the highest sample mean Average among the Gender categories.")
print(f"{highest_hometown} has the highest sample mean Average among the Hometown categories.")
```
**Analytical Interpretation:**
- Track: Communication holds the highest sample mean average (67.98), closely followed by Microelectronics (67.50) and Instrumentation (65.23).
- Gender: Male students registered the highest sample mean average (67.18) compared to female counterparts (66.62).
- Hometown: Luzon attained the highest sample mean average (68.08), with Mindanao (66.68) and Visayas (65.75) trailing.
