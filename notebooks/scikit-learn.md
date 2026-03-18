# Here is a template for a scikit-learn regression project
This should help in understanding the workflow of a scikit-learn regression project and how to use the scikit-learn library.
It is intended to be a guide for someone who is new to scikit-learn and go through the steps of the regression exercise in this notebook, but it can be used as a template for any regression project should you need to do one in the future.


# ── 1. Define your features ───────────────────────────────────────────────────
CAT_FEATS = [...]   # your categorical columns
NUM_FEATS = [...]   # your numerical columns

X = ...
y = ...

# ── 2. Split BEFORE encoding (think about why this matters!) ──────────────────
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(???)

# ── 3. Set up your encoder for categorical features ───────────────────────────
from sklearn.preprocessing import OneHotEncoder
# Hint: look up the handle_unknown parameter
ohe = OneHotEncoder(???)

# ── 4. Use a ColumnTransformer to apply different treatment to different cols ──
from sklearn.compose import ColumnTransformer
preprocessor = ColumnTransformer(transformers=[
    ("ohe", ohe, CAT_FEATS),
    ("num", ???, NUM_FEATS)     # what should happen to numeric columns?
])

# ── 5. Chain preprocessor + model into a Pipeline ────────────────────────────
from sklearn.pipeline import Pipeline
from sklearn.linear_model import LinearRegression

pipeline = Pipeline(steps=[
    (???, ???),
    (???, ???)
])

# ── 6. Fit and predict — just two lines! ─────────────────────────────────────
pipeline.???(X_train, y_train)
y_pred = pipeline.???(X_test)

# ── 7. Evaluate ───────────────────────────────────────────────────────────────
from sklearn.metrics import mean_squared_error, r2_score
# compute RMSE and R² here...

# ── 8. Scatter plot: actual vs. predicted ─────────────────────────────────────
# plt. ???