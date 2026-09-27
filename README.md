# Product Feedback Intelligence

> **NLP + Classical Machine Learning system for turning Google Play customer reviews into structured product feedback signals.**

## 1. Executive Overview

**Product Feedback Intelligence** is an end-to-end customer-feedback analytics project that converts unstructured Google Play reviews into actionable sentiment signals.

The project combines:

- Exploratory Data Analysis
- Text preprocessing
- TF-IDF feature engineering
- Logistic Regression
- Linear Support Vector Machine (SVM)
- Multinomial Naive Bayes
- Confusion-matrix analysis
- Class-level precision, recall and F1 analysis
- Error analysis
- App-level sentiment analysis
- Product-opportunity prioritization

The objective is not simply to predict sentiment. The broader objective is to demonstrate how a product team can transform large volumes of customer feedback into a repeatable analytical workflow.

---

## 2. Business Problem

Mobile applications receive large volumes of customer reviews containing information about:

- product satisfaction
- usability
- reliability
- bugs and crashes
- feature experiences
- frustrations
- positive product experiences

Manually reading every review is difficult at scale.

This project asks:

> **How effectively can classical machine-learning algorithms classify customer sentiment from Google Play reviews using the same TF-IDF representation, and how do their performance and error patterns differ?**

The resulting workflow creates a bridge between:

**Customer Voice → NLP → ML → Diagnostics → Product Insight**

---

## 3. Dataset

The source dataset is a Google Play Store user-review dataset.

### Raw dataset

- **Rows:** 64,295
- **Columns:** 5
- **Unique applications:** 1,074
- **Missing review values:** 26,868
- **Missing sentiment values:** 26,863
- **Exact duplicate rows:** 33,616

### Modelling dataset

Rows were retained when both `Translated_Review` and `Sentiment` were available. Exact duplicate rows were then removed.

- **Final labelled modelling rows:** 29,692
- **Applications represented:** 865

### Sentiment classes

| Sentiment | Reviews |
|---|---:|
| Positive | 19,015 |
| Negative | 6,321 |
| Neutral | 4,356 |

---

## 4. Project Architecture

```text
Raw Google Play Reviews
        |
        v
Data Quality & EDA
        |
        v
Text Cleaning
        |
        v
Train/Test Split (80/20, stratified)
        |
        v
TF-IDF (unigrams + bigrams)
        |
        +----------------------+----------------------+
        |                      |                      |
        v                      v                      v
Logistic Regression       Linear SVM           Naive Bayes
        |                      |                      |
        +----------------------+----------------------+
                               |
                               v
                    Evaluation & Diagnostics
                               |
              +----------------+----------------+
              |                |                |
              v                v                v
        Metrics          Confusion Matrix   Error Analysis
              |                |                |
              +----------------+----------------+
                               |
                               v
                     Product-Level Analysis
                               |
                               v
                    App Sentiment / Priorities
```

---

## 5. NLP Pipeline

Reviews are transformed through:

1. Lowercasing
2. URL removal
3. Non-alphabetic character removal
4. Tokenization
5. Stop-word removal
6. Snowball stemming
7. TF-IDF vectorization

The TF-IDF configuration used in the notebook is:

```text
max_features = 20,000
ngram_range = (1, 2)
min_df = 3
max_df = 0.95
sublinear_tf = True
```

The train/test split is performed before fitting TF-IDF so that the held-out test set does not influence vocabulary/statistical fitting.

---

## 6. Experimental Design

All three models use:

- the same training set
- the same test set
- the same cleaned review representation
- the same TF-IDF representation
- the same target variable
- the same evaluation metrics

### Split

```text
80% Training
20% Testing
random_state = 42
stratified by Sentiment
```

This makes the model comparison more defensible because differences are not caused by different test samples.

---

## 7. Models

### Logistic Regression

A linear multiclass classifier used as an interpretable baseline.

### Linear SVM

A linear Support Vector Machine designed for high-dimensional sparse text features.

### Multinomial Naive Bayes

A probabilistic classifier commonly used for bag-of-words and TF-IDF text classification.

---

## 8. Model Results

| Model | Accuracy | Macro Precision | Macro Recall | Macro F1 |
|---|---:|---:|---:|---:|
| Logistic Regression | 0.8637 | 0.8568 | 0.7821 | 0.8133 |\n| Linear SVM | 0.8723 | 0.8457 | 0.8156 | 0.8294 |\n| Naive Bayes | 0.6923 | 0.8205 | 0.4175 | 0.4067 |\n
### Interpretation

The three models show materially different performance profiles on the same held-out test set.

The analysis therefore considers more than accuracy. **Macro F1 and class-level recall are especially important** because the task contains Positive, Neutral and Negative classes with different support levels.

The project intentionally avoids treating a single metric as the sole definition of model quality.

---

## 9. Class-Level Diagnostics

The class-level results show that the models behave differently across sentiment categories.

In particular, the Neutral class deserves attention because neutral language is often more ambiguous than strongly positive or negative language.

See:

- `Class_Metrics` sheet
- confusion matrices in the notebook
- `Error_Analysis` sheet

---

## 10. Error Analysis

The test-set error analysis contains **757 misclassified reviews**.

The most frequent error directions are:

- **Negative → Positive:** 262\n- **Neutral → Positive:** 151\n- **Positive → Negative:** 136\n- **Positive → Neutral:** 91\n- **Negative → Neutral:** 68\n- **Neutral → Negative:** 49\n
These errors are useful from a product perspective because they show where automated sentiment interpretation can be unreliable.

Potential sources to investigate include:

- mixed sentiment
- ambiguous wording
- short reviews
- context-dependent language
- translation artefacts
- subtle neutral statements
- product-specific terminology

These are hypotheses for investigation, not causal conclusions established by the model.

---

## 11. Product-Level Analysis

The project aggregates sentiment at the application level.

For each app, the analysis calculates:

- review volume
- positive review count
- neutral review count
- negative review count
- positive percentage
- neutral percentage
- negative percentage

This enables product teams to move from:

> "What sentiment does this review express?"

toward:

> "Which products/apps show feedback patterns that warrant deeper investigation?"

---

## 12. Product Opportunity Index

The notebook creates an analytical prioritization measure:

```text
Negative_Risk_Index
=
Negative_Review_Percentage
×
log(1 + Review_Count)
```

This is **not a causal risk score**.

It is a prioritization device that combines negative-feedback intensity with review volume so that high-volume negative-feedback patterns receive analytical attention.

Small samples should not be interpreted in isolation.

---

## 13. Repository Structure

Recommended GitHub structure:

```text
product-feedback-intelligence/
│
├── PRODUCT_FEEDBACK_INTELLIGENCE.ipynb
├── README.md
├── data/
│   └── googleplaystore_user_reviews.csv
│
├── results/
│   └── product_feedback_intelligence_results.xlsx
│
└── reports/
    └── Executive_Report.docx
```

> **Data note:** If you publish the dataset, verify that redistribution is permitted by its source/license. Otherwise, keep the raw dataset out of the public repository and provide instructions for obtaining it.

---

## 14. How to Run

### Option A — Google Colab

1. Open the notebook in Google Colab.
2. Run cells from top to bottom.
3. Use the upload widget to upload the CSV.
4. Review EDA.
5. Train the three models.
6. Compare metrics.
7. Inspect error analysis.
8. Export the Excel results.

### Option B — Local Jupyter

Install dependencies:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn nltk wordcloud openpyxl
```

Then open:

```text
PRODUCT_FEEDBACK_INTELLIGENCE.ipynb
```

---

## 15. Key Visualizations

The project produces:

1. Sentiment distribution
2. Review-length analysis
3. Sentiment polarity distribution
4. Reviews by application
5. Sentiment by application
6. Model performance comparison
7. Confusion matrices
8. Class-level F1 comparison
9. Misclassification patterns
10. Product opportunity matrix
11. Sentiment word clouds
12. Logistic Regression feature analysis

---

## 16. Product Management Interpretation

The project demonstrates a product analytics loop:

```text
VOICE OF CUSTOMER
       ↓
STRUCTURE THE FEEDBACK
       ↓
IDENTIFY PATTERNS
       ↓
MEASURE MODEL RELIABILITY
       ↓
IDENTIFY HIGH-INTEREST AREAS
       ↓
INVESTIGATE PRODUCT ISSUES
       ↓
FORMULATE HYPOTHESES
       ↓
VALIDATE WITH PRODUCT / USER DATA
```

The model should therefore be treated as a **decision-support layer**, not as an automatic product-decision engine.

For example, a high negative-review percentage can indicate an area worth investigating, but it does not by itself establish:

- the root cause
- the business impact
- the number of affected users
- whether the issue is new or persistent
- whether the issue is caused by the product

Those questions require additional product, behavioral and operational data.

---

## 17. Limitations

### Dataset limitations

- Reviews are historical snapshots.
- The dataset is not necessarily representative of all users.
- Sentiment labels may contain subjectivity.
- Translated reviews may introduce language/translation artefacts.

### Modelling limitations

- Classical TF-IDF models do not fully understand context.
- Stemming can remove linguistic nuance.
- Negation and sarcasm can be difficult.
- Product-specific terminology may be poorly represented.
- No causal inference is performed.

### Business limitations

Sentiment is a signal, not a complete product-health metric.

A production system should ideally combine sentiment with:

- review volume
- app version
- crash rate
- retention
- feature adoption
- support tickets
- user cohorts
- churn
- ratings
- release dates

---

## 18. Future Improvements

Potential next steps:

- n-gram and hyperparameter tuning
- cross-validation on the training set
- class-weight experiments
- calibrated probability estimates
- transformer-based benchmarking
- topic modelling
- aspect-based sentiment analysis
- app-version analysis
- time-series sentiment tracking
- review clustering
- automated product-issue taxonomy
- dashboard deployment
- human-in-the-loop review

---

## 19. Business Impact

A productionized version of this system could help product teams:

- monitor customer sentiment at scale
- identify emerging complaint clusters
- compare feedback patterns across apps
- prioritize qualitative review
- evaluate changes after releases
- create evidence for product discovery
- reduce manual review effort

The strongest use case is not simply **automated sentiment prediction**. It is the creation of a repeatable **Voice-of-Customer intelligence layer** that helps product teams decide what deserves deeper investigation.

---

## 20. Author

**Arunima Rout**

B.Tech Computer Science & Engineering  
Software Engineer II | Product & AI/ML Projects

---

## License

Add an appropriate license based on how you intend to distribute the code and whether the underlying dataset permits redistribution.
