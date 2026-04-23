# 🐍 Python Data Analytics — Quick Revision Guide

> A concise cheat-sheet covering **NumPy**, **Pandas**, **Matplotlib**, **Seaborn**, and **Scikit-learn**.

---

## 📦 1. NumPy — Numerical Computing

### Array Creation
```python
import numpy as np

np.array([1, 2, 3])           # From list
np.zeros((3, 4))              # 3x4 array of zeros
np.ones((2, 3))               # 2x3 array of ones
np.eye(3)                     # 3x3 identity matrix
np.arange(0, 10, 2)          # [0, 2, 4, 6, 8]
np.linspace(0, 1, 5)         # 5 evenly spaced values between 0 and 1
np.random.rand(3, 3)         # Random float array
np.random.randint(0, 10, (3,3))  # Random int array
```

### Array Operations
```python
a = np.array([[1, 2], [3, 4]])

a.shape        # (2, 2)
a.dtype        # int64
a.ndim         # 2
a.size         # 4

a.reshape(4, 1)      # Reshape
a.flatten()          # 1D array
a.T                  # Transpose

np.dot(a, a)         # Matrix multiplication
a * 2                # Element-wise multiply
a + a                # Element-wise addition
```

### Aggregation & Math
```python
np.sum(a)            # Sum of all elements
np.mean(a)           # Mean
np.std(a)            # Standard deviation
np.min(a), np.max(a) # Min / Max
np.argmin(a), np.argmax(a)  # Index of min/max
np.sort(a, axis=0)  # Sort along axis
np.unique(a)         # Unique values
```

### Indexing & Slicing
```python
a[0, 1]              # Element at row 0, col 1
a[:, 1]              # All rows, column 1
a[0:2, :]            # Rows 0 and 1
a[a > 2]             # Boolean indexing
```

---

## 🐼 2. Pandas — Data Manipulation

### Creating DataFrames
```python
import pandas as pd

# From dict
df = pd.DataFrame({'Name': ['Alice', 'Bob'], 'Age': [25, 30]})

# From CSV
df = pd.read_csv('file.csv')

# From Excel
df = pd.read_excel('file.xlsx')
```

### Exploring Data
```python
df.head()            # First 5 rows
df.tail()            # Last 5 rows
df.shape             # (rows, cols)
df.info()            # Column types & nulls
df.describe()        # Statistical summary
df.columns           # Column names
df.dtypes            # Data types
df.isnull().sum()    # Count nulls per column
df.value_counts()    # Frequency count
```

### Selecting Data
```python
df['Age']                     # Single column (Series)
df[['Name', 'Age']]          # Multiple columns
df.iloc[0]                    # Row by index position
df.loc[0]                     # Row by label
df.loc[df['Age'] > 25]       # Filter rows
df.iloc[0:3, 1:3]            # Slice by position
```

### Data Cleaning
```python
df.dropna()                   # Drop rows with NaN
df.fillna(0)                  # Fill NaN with 0
df.rename(columns={'Age': 'age'})   # Rename column
df.drop('Age', axis=1)        # Drop column
df.drop_duplicates()          # Remove duplicates
df['Age'] = df['Age'].astype(float)  # Change dtype
df.replace({'yes': 1, 'no': 0})     # Replace values
```

### Data Transformation
```python
df.sort_values('Age', ascending=False)   # Sort
df.groupby('City').mean()                # GroupBy + aggregate
df.pivot_table(values='Sales', index='Month', columns='Region', aggfunc='sum')

df['double_age'] = df['Age'] * 2        # New column
df.apply(lambda x: x * 2)              # Apply function
df.merge(df2, on='ID', how='inner')     # Join DataFrames
pd.concat([df1, df2], axis=0)          # Concatenate
```

### Export
```python
df.to_csv('output.csv', index=False)
df.to_excel('output.xlsx', index=False)
```

---

## 📊 3. Matplotlib — Plotting

### Basic Plots
```python
import matplotlib.pyplot as plt

# Line Plot
plt.plot([1, 2, 3], [4, 5, 6], color='blue', linestyle='--', marker='o')
plt.title('Line Plot')
plt.xlabel('X')
plt.ylabel('Y')
plt.legend(['Series 1'])
plt.grid(True)
plt.show()

# Bar Chart
plt.bar(['A', 'B', 'C'], [10, 20, 15], color='steelblue')

# Horizontal Bar
plt.barh(['A', 'B', 'C'], [10, 20, 15])

# Scatter Plot
plt.scatter(x, y, c='red', s=50, alpha=0.7)

# Histogram
plt.hist(data, bins=20, edgecolor='black')

# Pie Chart
plt.pie([30, 40, 30], labels=['A', 'B', 'C'], autopct='%1.1f%%')
```

### Subplots
```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))
axes[0].plot(x, y)
axes[0].set_title('Plot 1')
axes[1].scatter(x, y)
axes[1].set_title('Plot 2')
plt.tight_layout()
plt.show()
```

### Styling
```python
plt.style.use('ggplot')       # 'seaborn', 'dark_background', etc.
plt.figure(figsize=(10, 6))   # Set figure size
plt.savefig('plot.png', dpi=300, bbox_inches='tight')
```

---

## 🎨 4. Seaborn — Statistical Visualization

```python
import seaborn as sns

# Distribution
sns.histplot(df['Age'], kde=True)
sns.boxplot(x='Category', y='Value', data=df)
sns.violinplot(x='Category', y='Value', data=df)

# Relationships
sns.scatterplot(x='Age', y='Salary', hue='Gender', data=df)
sns.lineplot(x='Month', y='Sales', data=df)
sns.pairplot(df)                  # All variable pairs
sns.heatmap(df.corr(), annot=True, cmap='coolwarm')  # Correlation matrix

# Categorical
sns.barplot(x='City', y='Sales', data=df)
sns.countplot(x='Category', data=df)

# Regression
sns.regplot(x='Age', y='Income', data=df)
sns.lmplot(x='Age', y='Income', hue='Gender', data=df)

# Styling
sns.set_theme(style='whitegrid', palette='muted')
```

---

## 🤖 5. Scikit-learn — Machine Learning

### Workflow Overview
```
Load Data → Preprocess → Split → Train Model → Evaluate → Tune
```

### Preprocessing
```python
from sklearn.preprocessing import (
    StandardScaler, MinMaxScaler, LabelEncoder, OneHotEncoder
)
from sklearn.impute import SimpleImputer

# Scale features
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Normalize [0, 1]
minmax = MinMaxScaler()
X_norm = minmax.fit_transform(X)

# Encode categorical
le = LabelEncoder()
y_encoded = le.fit_transform(y)

ohe = OneHotEncoder(sparse=False)
X_encoded = ohe.fit_transform(X[['category_col']])

# Fill missing values
imputer = SimpleImputer(strategy='mean')  # 'median', 'most_frequent'
X_clean = imputer.fit_transform(X)
```

### Train-Test Split
```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)
```

### Supervised Learning — Classification
```python
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.svm import SVC
from sklearn.neighbors import KNeighborsClassifier

# Example with Logistic Regression
model = LogisticRegression(max_iter=200)
model.fit(X_train, y_train)
y_pred = model.predict(X_test)
y_prob = model.predict_proba(X_test)  # Probabilities
```

### Supervised Learning — Regression
```python
from sklearn.linear_model import LinearRegression, Ridge, Lasso
from sklearn.ensemble import RandomForestRegressor
from sklearn.tree import DecisionTreeRegressor

model = LinearRegression()
model.fit(X_train, y_train)
y_pred = model.predict(X_test)

print(model.coef_)      # Coefficients
print(model.intercept_) # Intercept
```

### Unsupervised Learning
```python
from sklearn.cluster import KMeans, DBSCAN, AgglomerativeClustering
from sklearn.decomposition import PCA

# K-Means Clustering
kmeans = KMeans(n_clusters=3, random_state=42)
kmeans.fit(X)
labels = kmeans.labels_
centers = kmeans.cluster_centers_

# PCA — Dimensionality Reduction
pca = PCA(n_components=2)
X_reduced = pca.fit_transform(X)
print(pca.explained_variance_ratio_)  # Variance explained per component
```

### Evaluation Metrics
```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    confusion_matrix, classification_report,
    mean_squared_error, mean_absolute_error, r2_score,
    roc_auc_score, roc_curve
)

# Classification
print(accuracy_score(y_test, y_pred))
print(classification_report(y_test, y_pred))  # Full report
cm = confusion_matrix(y_test, y_pred)

# Regression
mse = mean_squared_error(y_test, y_pred)
rmse = mse ** 0.5
r2 = r2_score(y_test, y_pred)
mae = mean_absolute_error(y_test, y_pred)

# ROC-AUC
auc = roc_auc_score(y_test, y_prob[:, 1])
```

### Cross-Validation
```python
from sklearn.model_selection import cross_val_score, KFold, StratifiedKFold

scores = cross_val_score(model, X, y, cv=5, scoring='accuracy')
print(f"Mean: {scores.mean():.3f}, Std: {scores.std():.3f}")
```

### Hyperparameter Tuning
```python
from sklearn.model_selection import GridSearchCV, RandomizedSearchCV

param_grid = {'n_estimators': [50, 100, 200], 'max_depth': [3, 5, None]}

grid = GridSearchCV(RandomForestClassifier(), param_grid, cv=5, scoring='accuracy')
grid.fit(X_train, y_train)

print(grid.best_params_)
print(grid.best_score_)
best_model = grid.best_estimator_
```

### Pipelines
```python
from sklearn.pipeline import Pipeline

pipe = Pipeline([
    ('imputer', SimpleImputer(strategy='mean')),
    ('scaler', StandardScaler()),
    ('model', RandomForestClassifier(n_estimators=100))
])

pipe.fit(X_train, y_train)
y_pred = pipe.predict(X_test)
```

### Feature Importance
```python
import pandas as pd

# For tree-based models
importances = pd.Series(model.feature_importances_, index=feature_names)
importances.sort_values(ascending=False).plot(kind='bar')
```

---

## ⚡ Quick Tips

| Task | Best Library |
|---|---|
| Numerical arrays & math | NumPy |
| Data wrangling & cleaning | Pandas |
| Basic plots | Matplotlib |
| Statistical / pretty plots | Seaborn |
| Machine learning | Scikit-learn |

### Common Pattern — Full ML Pipeline
```python
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report

# 1. Load
df = pd.read_csv('data.csv')

# 2. Prepare
X = df.drop('target', axis=1)
y = df['target']

# 3. Split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 4. Scale
scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)
X_test  = scaler.transform(X_test)

# 5. Train
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# 6. Evaluate
y_pred = model.predict(X_test)
print(classification_report(y_test, y_pred))
```

---

> 📌 **Remember**: Always `fit` on training data only, then `transform` both train and test. Never leak test data into your preprocessing!
