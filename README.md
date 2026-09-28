# Iris Dataset Analysis

**Which measurements actually tell the three Iris species apart?**

An exploratory analysis of the classic Iris dataset: 150 flowers from three species (*Iris setosa*, *Iris versicolor*, *Iris virginica*), each described by four measurements (sepal length/width and petal length/width, in cm).

The notebook first **rebuilds the full dataset from five separate source files** and answers a few targeted lookup questions. It then goes further with an extended analysis of distributions, variability, correlations, outliers, and feature separation, including a Simpson's paradox check and a test of whether an engineered feature (petal area) separates the hardest pair of species better than the raw measurements.

---

## Table of Contents

- [Questions Explored](#questions-explored)
- [Dataset](#dataset)
- [Methods and Skills Demonstrated](#methods-and-skills-demonstrated)
- [Key Findings](#key-findings)
- [Getting Started](#getting-started)
- [Repository Structure](#repository-structure)
- [Limitations](#limitations)

---

## Questions Explored

**Part 1: Data assembly and lookups**

- Is the combined dataset complete, correctly typed, and balanced across species?
- Average sepal length of Virginica vs average petal length of Setosa
- The complete dataset, combined from all five files and shuffled
- Minimum and maximum sepal and petal width for Setosa and Versicolor

**Part 2: Deeper analysis**

1. How do the three species compare on each measurement (mean, median, standard deviation)?
2. How are the measurements distributed, and where do the species overlap?
3. Which measurements vary most within a species (coefficient of variation)?
4. Which measurements move together (correlation)?
5. Do those correlations hold *within* each species, or are they misleading when pooled?
6. Are there unusual measurements within a species (1.5 × IQR rule)?
7. Which single measurement separates the species best?
8. Can a combined measurement separate Versicolor and Virginica better?

## Dataset

The dataset is split across five published Google Sheets (CSV format), which the notebook downloads and merges on `Id` and `Unnamed: 0`:

| Source | Contents |
|---|---|
| Sepal All | `Id`, `SepalLengthCm`, `SepalWidthCm` |
| Petal All | `Id`, `PetalLengthCm`, `PetalWidthCm` |
| Iris Setosa | `Id`, `Species` |
| Iris Versicolor | `Id`, `Species` |
| Iris Virginica | `Id`, `Species` |

`Unnamed: 0` is an unused index column created when the sub-datasets were generated; it is only used as a merge key. The merged result has 150 rows and these columns:

| Column | Meaning |
|---|---|
| `Id` | Record ID |
| `Species` | `Iris-setosa`, `Iris-versicolor` or `Iris-virginica` |
| `SepalLengthCm` / `SepalWidthCm` | Sepal length / width (cm) |
| `PetalLengthCm` / `PetalWidthCm` | Petal length / width (cm) |

**Data quality:** 150 rows, no missing values, all measurements numeric, every `Id` unique, and exactly 50 flowers per species. Five rows repeat another flower's measurements within the same species; they are genuine separate observations and are kept.

> **Note:** the data is loaded directly from URLs, so an internet connection is required to run the notebook.

## Methods and Skills Demonstrated

- Merging and reshaping multiple data sources (`merge`, `concat`, `melt`)
- Data quality checks (types, duplicates, class balance)
- Group-wise summary statistics with `groupby` / `agg`
- Distribution analysis (KDE plots) and box plots
- Coefficient of variation to compare spread across different scales
- Correlation analysis, pooled vs within-group (**Simpson's paradox**)
- IQR-based outlier detection
- Feature engineering and a feature-separation comparison
- Visualisation with `matplotlib` and `seaborn`

## Key Findings

1. **The petals identify the species; the sepals barely do.** Petal length and width completely separate Setosa, and leave only 37-38 of the 100 Versicolor/Virginica flowers in a shared range, versus 88-96 for the sepal measurements. Sepal width is nearly uninformative on its own.
2. **Species explain petal variation, not sepal variation.** The coefficient of variation of petal length/width is about 47-64% across all flowers but only about 10-15% inside Versicolor and Virginica. For sepals the drop is much smaller (about 14% overall vs 7-11% within species).
3. **Setosa is distinct but not simply "smaller".** It has by far the smallest petals (mean length 1.46 cm vs 4.26 and 5.55) yet the widest sepals (mean width 3.42 cm), so species do not rank consistently across features.
4. **Pooled correlations can mislead (Simpson's paradox).** Sepal length and width are essentially uncorrelated overall (-0.11) but positively correlated inside every species (0.46-0.75). Petal length and width correlate at 0.96 overall but only about 0.3 within Setosa and Virginica.
5. **Versicolor vs Virginica is the hard pair, and combining features helps.** Petal area (length × width) cuts the number of ambiguous flowers from 37-38 down to about 10 out of 100. Other engineered features (sepal area, petal-to-sepal length ratio) add nothing.
6. **Outliers are mild.** 11 of 600 measurements fall just outside the 1.5 × IQR limits, mostly in Setosa petals, and none looks like a data-entry error.

**Species averages (cm)**

| Species | Sepal length | Sepal width | Petal length | Petal width |
|---|---|---|---|---|
| Setosa | 5.01 | 3.42 | 1.46 | 0.24 |
| Versicolor | 5.94 | 2.77 | 4.26 | 1.33 |
| Virginica | 6.59 | 2.97 | 5.55 | 2.03 |

**Versicolor/Virginica flowers in the shared range (of 100; lower means better separation)**

| Feature | Flowers in shared range |
|---|---|
| Petal area (length × width) | 10 |
| Petal length | 37 |
| Petal width | 38 |
| Petal-to-sepal length ratio | 41 |
| Sepal length | 88 |
| Sepal area | 91 |
| Sepal width | 96 |

## Getting Started

### Prerequisites

- Python 3.9+
- Jupyter Notebook or JupyterLab
- `pandas`, `numpy`, `matplotlib`, `seaborn`
- Internet connection (the data is read from Google Sheets URLs)

### Installation

```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
pip install pandas numpy matplotlib seaborn jupyter
```

### Run

```bash
jupyter notebook Iris_Dataset_Analysis.ipynb
```

Then run all cells (**Kernel → Restart & Run All**).

## Repository Structure

```
.
├── Iris_Dataset_Analysis.ipynb
└── README.md
```

## Limitations

- **Small sample.** With only 150 observations (50 per species), the range-based overlap counts are sensitive to a few extreme flowers.
- **Descriptive only.** The analysis shows what separates the species in this sample, not how well a predictive model would generalise. A natural next step is a cross-validated classifier (for example logistic regression or k-nearest neighbours) using petal area and the raw petal measurements.
- **Coefficient of variation caveat.** CV is inflated when the mean is close to zero, as with Setosa petal width (0.1-0.6 cm).

## Data Source

The Iris dataset was introduced by Ronald Fisher (1936) and is widely available, for example from the [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/53/iris). This notebook uses a copy split across five published Google Sheets.
