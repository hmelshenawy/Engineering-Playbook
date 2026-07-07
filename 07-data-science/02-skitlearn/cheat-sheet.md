# Scikit-Learn Cheatsheet

Quick reference for the Scikit-Learn APIs I use most often.

---

## Load Datasets

| Command                      | Hint                |
| ---------------------------- | ------------------- |
| `pd.read_csv()`              | Load custom dataset |
| `load_iris()`                | Iris dataset        |
| `load_digits()`              | Digits dataset      |
| `fetch_california_housing()` | Housing dataset     |
| `train_test_split()`         | Split train/test    |

---

## Preprocessing

| Command            | Hint                    |
| ------------------ | ----------------------- |
| `StandardScaler()` | Standardize features    |
| `MinMaxScaler()`   | Scale to 0–1            |
| `OneHotEncoder()`  | Encode categorical data |
| `LabelEncoder()`   | Encode target labels    |
| `SimpleImputer()`  | Fill missing values     |

---

## Feature Selection

| Command         | Hint                          |
| --------------- | ----------------------------- |
| `SelectKBest()` | Select top K features         |
| `RFE()`         | Recursive feature elimination |
| `PCA()`         | Dimensionality reduction      |

---

## Classification Models

| Command                    | Hint                               |
| -------------------------- | ---------------------------------- |
| `LogisticRegression()`     | Binary / multiclass classification |
| `DecisionTreeClassifier()` | Decision tree                      |
| `RandomForestClassifier()` | Random forest                      |
| `SVC()`                    | Support Vector Machine             |
| `KNeighborsClassifier()`   | KNN classifier                     |
| `GaussianNB()`             | Naive Bayes                        |

---

## Regression Models

| Command                   | Hint              |
| ------------------------- | ----------------- |
| `LinearRegression()`      | Linear regression |
| `Ridge()`                 | Ridge regression  |
| `Lasso()`                 | Lasso regression  |
| `DecisionTreeRegressor()` | Decision tree     |
| `RandomForestRegressor()` | Random forest     |

---

## Clustering

| Command                     | Hint                     |
| --------------------------- | ------------------------ |
| `KMeans()`                  | K-Means clustering       |
| `DBSCAN()`                  | Density-based clustering |
| `AgglomerativeClustering()` | Hierarchical clustering  |

---

## Model Training

| Command                       | Hint                     |
| ----------------------------- | ------------------------ |
| `model.fit(X_train, y_train)` | Train model              |
| `model.predict(X_test)`       | Predict                  |
| `model.predict_proba(X_test)` | Prediction probabilities |
| `model.score(X_test, y_test)` | Quick accuracy/R²        |

---

## Evaluation

| Command                   | Hint                        |
| ------------------------- | --------------------------- |
| `accuracy_score()`        | Classification accuracy     |
| `precision_score()`       | Precision                   |
| `recall_score()`          | Recall                      |
| `f1_score()`              | F1 score                    |
| `confusion_matrix()`      | Confusion matrix            |
| `classification_report()` | Full classification metrics |
| `mean_squared_error()`    | MSE                         |
| `mean_absolute_error()`   | MAE                         |
| `r2_score()`              | R² score                    |

---

## Cross Validation

| Command             | Hint              |
| ------------------- | ----------------- |
| `cross_val_score()` | Cross-validation  |
| `KFold()`           | K-Fold splitter   |
| `StratifiedKFold()` | Stratified K-Fold |

---

## Hyperparameter Tuning

| Command                | Hint              |
| ---------------------- | ----------------- |
| `GridSearchCV()`       | Exhaustive search |
| `RandomizedSearchCV()` | Random search     |

---

## Pipelines

| Command           | Hint                        |
| ----------------- | --------------------------- |
| `Pipeline()`      | Chain preprocessing + model |
| `make_pipeline()` | Quick pipeline creation     |

---

## Model Persistence

| Command                           | Hint       |
| --------------------------------- | ---------- |
| `joblib.dump(model, "model.pkl")` | Save model |
| `joblib.load("model.pkl")`        | Load model |

---

## Daily Commands

| Command                   | Hint                      |
| ------------------------- | ------------------------- |
| `train_test_split()`      | Split dataset             |
| `StandardScaler()`        | Scale features            |
| `model.fit()`             | Train                     |
| `model.predict()`         | Predict                   |
| `accuracy_score()`        | Evaluate classifier       |
| `classification_report()` | Detailed metrics          |
| `cross_val_score()`       | Validate model            |
| `GridSearchCV()`          | Tune hyperparameters      |
| `Pipeline()`              | Production-ready workflow |
