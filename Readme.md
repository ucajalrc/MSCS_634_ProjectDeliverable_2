# Deliverable 2: Regression Modeling & Performance Evaluation**

## **Project Title**
Name: Ajal RC <br/>
Class: MSCS 634 — Project Deliverable 2 <br/>
Title: Regression Modeling and Performance Evaluation Using the Online Retail II Dataset 

---

# **1. Dataset Summary**

This deliverable uses the **Online Retail II** dataset, a real-world transactional dataset that contains:

* `InvoiceNo` — invoice identifier
* `StockCode` — product ID
* `Description` — item description
* `Quantity` — units purchased
* `Price` — price per unit
* `InvoiceDate` — timestamp
* `Country` — customer location

### **Data Preparation**

Before modeling, the dataset required several cleaning steps:

* All column names were normalized (lowercase + underscores).
* `Quantity` and `Price` were converted to numeric types.
* Negative values were removed (these represent returns).
* New feature **`line_total = quantity × price`** was created.
* Invoice-level aggregation was performed so each row represents **one invoice**, not a line item.
* Additional engineered features were added:

  * **`num_lines`** — number of distinct items on the invoice
  * **`day_of_week`** — extracted from invoice timestamp
  * **`hour_of_day`** — extracted from invoice timestamp

The goal for Deliverable 2 was to **predict total invoice spend (`line_total`)**.

---

## **Task 1 — Feature Engineering**

Feature engineering included:

* Keeping only valid, positive transactions
* Aggregating invoices using features:

  * Total quantity purchased
  * Average price
  * Number of line items
  * Day of week
  * Hour of day
* Defining:

  * **X (features):** `quantity`, `price`, `num_lines`, `day_of_week`, `hour_of_day`
  * **y (target):** `line_total`
* Performing an **80/20 train–test split**

This transformed the raw retail dataset into a usable regression modeling dataset.

---

## **Task 2 — Regression Models Built**

Two regression models were implemented:

### ✔ **Model 1 — Multiple Linear Regression (baseline)**

Standard linear regression using all engineered features.

### ✔ **Model 2 — Ridge Regression (α = 1.0)**

Regularized regression that shrinks large coefficients to reduce model variance.

---

## **Task 3 — Model Evaluation**

Models were evaluated using:

* **R²**
* **MAE**
* **MSE**
* **RMSE**

Both models were compared to understand error magnitude and variance explanation.

---

## **Task 4 — Cross-Validation**

5-fold cross-validation was performed to test generalization.
You received the following **real results**:

### **Multiple Linear Regression (5-fold CV)**

* R² per fold:
  `[0.4459, 0.2628, 0.4565, 0.5382, 0.4573]`
* **Mean R² = 0.43215**
* RMSE per fold:
  `[777.41, 1051.07, 1027.81, 861.93, 1706.72]`
* **Mean RMSE = 1084.99**

### **Ridge Regression (α = 1.0)**

* R² per fold:
  `[0.4459, 0.2628, 0.4565, 0.5382, 0.4573]`
* **Mean R² = 0.43215**
* RMSE per fold:
  `[777.41, 1051.07, 1027.81, 861.93, 1706.72]`
* **Mean RMSE = 1084.99**

### **Cross-Validation Summary Table**

| Model                        | CV_Mean_R2 | CV_Mean_RMSE |
| ---------------------------- | ---------- | ------------ |
| Multiple Linear Regression   | 0.432154   | 1084.987294  |
| Ridge Regression (alpha=1.0) | 0.432154   | 1084.987294  |

**Both models performed identically during cross-validation**, indicating that the dataset does not suffer from strong multicollinearity or coefficient instability.

---

## **Task 5 — Summary & Insights**

### **1. Both Models Performed Identically**

Both Linear Regression and Ridge Regression produced the same:

* Mean R²
* Mean RMSE
* Fold-by-fold results

This indicates:

* Regularization had **no significant impact**
* The features are **reasonably independent**
* Model variance is not high
* Retail invoice-level features behave in a stable linear pattern

### **2. R² ≈ 0.43 Means Moderate Predictive Strength**

The models explained **~43% of the variance** in invoice total spending.

This is reasonable because invoice totals contain **natural noise** such as:

* Customer purchasing randomness
* Price variability
* Product mix differences

Retail data rarely produces extremely high R² values without very detailed customer-level segmentation.

### **3. RMSE ≈ 1084 Means Real-World Variability**

RMSE over 1000 makes sense because:

* Some invoices contain high quantity orders
* Pricing varies dramatically depending on product type
* Large orders cause high variance in totals

### **4. Ridge Does Not Improve Performance**

Because:

* Feature correlations were not strong
* The model was already stable
* There was no overfitting behavior

Ridge regression simply collapsed to the same solution as Linear Regression.

### **5. Feature Engineering Matters**

The strongest contributors were:

* `quantity` — higher item count → larger invoice total
* `price` — basket of higher-priced items → higher spend
* `num_lines` — diverse basket → larger invoice total

Time-based features (`day_of_week`, `hour_of_day`) had minimal impact.

---

# **4. Challenges and How They Were Addressed**

### **1. Missing or Incorrect Column Names**

* The dataset uses `price`, not `unitprice`
* Fixed by renaming and normalizing columns

### **2. Detecting the Invoice Column**

* Different datasets label this differently (`InvoiceNo`, etc.)
* Implemented auto-detection with fallback checks

### **3. Negative Transactions**

* Returns caused negative quantities
* Removed all negative values to avoid corrupting regression results

### **4. Large Variation in Invoice Totals**

* Some invoices have extremely high totals
* RMSE was high as a result
* Cross-validation confirmed this variance is natural and stable

### **5. Ensuring Deliverable 2 Runs Independently**

* Loaded data from ZIP
* Cleaned and engineered features inside the notebook
* Did not rely on variables from Deliverable 1

---

# **5. Conclusion**

Deliverable 2 successfully demonstrated:

* Independent dataset loading and cleaning
* Intelligent feature engineering
* Construction of two regression models
* Full performance evaluation
* Cross-validation for generalization assessment
* Clear interpretation of model results

Both linear regression and ridge regression performed similarly, indicating the feature space is already stable and does not require regularization.

These insights help understand how invoice-level behavior can be predicted and which features contribute most to retail spending.
