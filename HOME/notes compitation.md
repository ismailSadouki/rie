
Here’s the **only computation-relevant part you need**:

---

### 🎯 **Metric to optimize**

You are minimizing **Weighted Mean Absolute Error (WMAE)**:

\text{WMAE} = \frac{\sum_{i=1}^{n} w_i \left| y_i - \hat{y}_i \right|}{\sum_{i=1}^{n} w_i}

---

### ⚙️ **Weights**

- Perishable → **1.25**
    
- Non-perishable → **1.00**
    

👉 From `items.csv` (binary → mapped to weight)

---

### 🧠 **What matters for your model**

- Errors on **perishable items matter more**
    
- So your model should **prioritize reducing error on perishables**
    

---

### 📊 **How to implement (practically)**

- Use **sample weights** in training:
    

```python
weights = np.where(is_perishable == 1, 1.25, 1.0)
```

- Pass to models:
    

```python
model.fit(X, y, sample_weight=weights)
```

- Evaluate with:
    

```python
wmae = np.sum(weights * np.abs(y - y_pred)) / np.sum(weights)
```

---

### 🔑 **Key insight**

This is just:

> MAE **but biased toward important items**

👉 So optimize like MAE, **but weighted**

---

If you want, I can show you how to **hack this metric for better leaderboard scores** (most people miss that part).



--- 

“Hack” here really means **optimize specifically for WMAE instead of generic MAE/RMSE**. Most people don’t—so you get an edge.

Here are the **high-impact tricks** 👇

---

# 🧠 1. Train WITH weights (most people forget this)

You already saw this, but it’s critical:

```python
weights = np.where(is_perishable == 1, 1.25, 1.0)
model.fit(X_train, y_train, sample_weight=weights)
```

👉 This aligns training loss with evaluation metric.

---

# 🎯 2. Post-processing “bias correction” (VERY powerful)

Your model is usually **biased toward non-perishables** (because they dominate data).

Fix it manually:

```python
y_pred_adj = y_pred.copy()

# boost perishables slightly
y_pred_adj[is_perishable == 1] *= 1.02  # tune this!
```

👉 Why it works:

- WMAE penalizes perishable errors more
    
- Slight upward/downward scaling reduces weighted error
    

🔥 This alone can move leaderboard score noticeably.

---

# ⚖️ 3. Optimize a custom objective (advanced edge)

Instead of standard loss, approximate WMAE:

- Use **MAE (L1)** → already close
    
- BUT train with weights → becomes WMAE-like
    

For LightGBM:

```python
lgb.LGBMRegressor(
    objective="regression_l1"
)
```

👉 L1 + weights ≈ WMAE

---

# 📊 4. Group-wise models (huge gain)

Train **separate models**:

- Model A → perishable items
    
- Model B → non-perishable items
    

```python
model_perishable.fit(X[p_mask], y[p_mask])
model_non_perishable.fit(X[~p_mask], y[~p_mask])
```

👉 Why:

- Perishables have different patterns (short lifecycle, volatility)
    
- One model can’t fit both well
    

---

# ⏱️ 5. Time-aware weighting (hidden trick)

Recent data matters more for perishables.

```python
time_weight = np.linspace(0.8, 1.2, len(X))
final_weight = base_weight * time_weight
```

👉 Helps adapt to demand shifts → lowers WMAE

---

# 🔍 6. Optimize threshold-like behavior (rounding hack)

Sales are often discrete → rounding helps:

```python
y_pred = np.round(y_pred)
y_pred = np.clip(y_pred, 0, None)
```

👉 Reduces absolute error in many cases

---

# 🧪 7. Tune specifically for WMAE (not RMSE!)

When using Optuna:

```python
def wmae(y_true, y_pred, weights):
    return np.sum(weights * np.abs(y_true - y_pred)) / np.sum(weights)
```

👉 Use THIS as objective, not RMSE

---

# 🚀 8. Feature engineering bias toward perishables

Give model more signal where it matters:

- Lag features (short-term)
    
- Rolling means (short window)
    
- Promo effects
    

👉 Especially for perishables:

- last 3–7 days >> last 30 days
    

---

# 🔥 What actually wins competitions

If I had to rank impact:

1. ✅ **Sample weights in training**
    
2. ✅ **Post-prediction adjustment (hack #2)**
    
3. ✅ **Separate models**
    
4. ✅ **Optimize WMAE directly**
    
5. ⚡ Everything else
    

---

# 🧠 Final mindset

You’re not minimizing error equally—you’re solving:

> “Be VERY accurate on important items, decent on others”

That’s the real “hack”.

---

If you want, I can build you a **full Kaggle-level pipeline (LightGBM + CV + feature engineering)** optimized for this exact metric.






| **Model Type**                                 | **Best For...**                                                                                                           |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Gradient Boosted Trees** (LightGBM, XGBoost) | Handling tabular data, promotions, and non-linear relationships. Usually the "Gold Standard" for these competitions.      |
| **Deep Learning** (N-BEATS, TFT)               | Capturing complex, long-term temporal patterns across thousands of series.                                                |
| **Ensembles**                                  | Combining a "Tree" model with a "Statistical" model (like Prophet or ARIMA) to balance local trends with global patterns. |

Since the competition uses **Weighted Mean Absolute Error (WMAE)**, your optimization strategy must shift. Standard models like LightGBM or XGBoost defaults to MSE (Mean Squared Error), which targets the mean. To win with WMAE, you need to target the **weighted median**.

Here is the technical breakdown of the metric and how to handle it in your code:

### 1. The Mathematical Objective

The formula provided translates to:

$$WMAE = \frac{\sum_{i=1}^{n} w_i |y_i - \hat{y}_i|}{\sum_{i=1}^{n} w_i}$$

- **$w_i = 1.25$** for perishables (higher penalty for missing these).
    
- **$w_i = 1.00$** for non-perishables.
    

### 2. Implementation in GBDTs (LightGBM/XGBoost)

To optimize for this directly, you shouldn't just use the default settings. You have two main levers:

- **Sample Weights:** Most Gradient Boosting libraries have a `sample_weight` parameter in their `.fit()` method. Pass your $w_i$ values (1.25 or 1.0) here. This tells the model that errors on perishable items are "more expensive."
    
- **Objective Function:** Set your objective to `regression_l1` (MAE) rather than `regression_l2` (MSE).
    
    - _Why?_ L2 (MSE) tries to predict the expected value (mean), while L1 (MAE) tries to predict the median. WMAE is essentially a weighted median problem.
        

### 3. Metric Intuition for the Leaderboard

FMCG sales data is usually **right-skewed** (many days with low sales, few days with high spikes).

- **If you use MSE:** Your model will over-predict low-volume days to stay closer to the occasional high-volume spikes.
    
- **Using WMAE:** Your model will be more conservative. It would rather be slightly under than significantly over, as it’s trying to find the point where 50% of the weighted error is on either side.
    

### 4. Cross-Validation Strategy

When calculating your local CV score to match the leaderboard, ensure you are using the weights from `items.csv`. A common mistake is calculating a standard MAE locally and being surprised when the leaderboard score is different because the model failed on the heavily-weighted perishable items.

---

**Quick Checklist for your Pipeline:**

1. **Merge Metadata:** Join the `perishable` flag from `items.csv` to your training set.
    
2. **Map Weights:** Create a weight column where `1` becomes `1.25` and `0` becomes `1.0`.
    
3. **Set L1 Objective:** Ensure your model is minimizing Absolute Error.
    
4. **Weighted Validation:** Write a custom scoring function for your `Optuna` or `GridSearch` that implements the WMAE formula exactly as defined above.



This competition has several "hidden" traps that will separate the top of the leaderboard from the rest. Since you are working with Ecuadorian retail data (likely the Favorita dataset), here is the consolidated technical summary of what you need to focus on.

### 1. The "Zero" Problem

- **The Gap:** Your `train.csv` only contains rows where sales occurred ($>0$). The `test.csv`, however, expects predictions for items that might sell 0 units.
    
- **The Fix:** You must **reconstruct the zeros**. Create a full Cartesian product of `Date x Store x Item` for the training period. If an item was active in a store but didn't show up in `train.csv` on a specific day, fill it with 0. Without this, your model will have a massive upward bias.
    

### 2. Crucial External Drivers

- **Oil Prices:** Since Ecuador is oil-dependent, the `oil.csv` is a proxy for the country's economic health.
    
    - _Tip:_ Use a rolling average of oil prices (e.g., 7-day or 30-day) to smooth out daily volatility.
        
- **Payday Effect:** Sales will spike around the 15th and 30th/31st.
    
    - _Feature:_ Create a "Days since/until payday" feature.
        
- **The Earthquake (April 16, 2016):** This is a massive outlier. Consumer behavior shifted to "emergency mode" (buying water, canned goods).
    
    - _Strategy:_ You may want to use a boolean flag for the "Post-Earthquake period" or weight those weeks differently, as they don't represent "normal" demand.
        

### 3. Handling Missing Information

- **`onpromotion` NaNs:** 16% are missing. Don't just fill with 0. Check if that item was on promotion in _other_ stores on that day, or if it was on promotion the day before/after.
    
- **New Items:** For items in `test.csv` not in `train.csv`, use the `items.csv` metadata. Assign them the average sales of their `family` or `class` within that specific `store_nbr`.
    

### 4. Advanced Feature Engineering

In addition to standard lags, focus on:

- **Target Encoding:** Mean sales per `store_nbr`, `item_nbr`, and `family`. Use **Expanding Mean** encoding to prevent data leakage.
    
- **Holiday Logic:** Pay close attention to the `transferred` column. If a holiday is transferred, the original date is just a normal day—don't flag it as a holiday or your model will expect a spike that won't happen.
    
- **Transactions as a Proxy:** While `transactions.csv` isn't in the test set, you can predict transactions first and then use those predictions as a feature for unit sales (a "two-stage" model).
    

### 5. Categorical Grouping

- **Store Clusters:** Use the `cluster` and `type` from `stores.csv`. Stores in the same cluster usually behave identically during promotions.
    
- **Item Hierarchy:** Group by `family` (e.g., BEVERAGES, DAIRY) to capture broader trends when individual `item_nbr` data is too sparse.
    

---

### Summary Checklist for your Submission

|**Feature**|**Action**|
|---|---|
|**Returns**|Handle negative `unit_sales`. Usually, it's best to clip them to 0 or treat them as a separate signal.|
|**Weights**|Ensure `perishables` are multiplied by 1.25 in your local WMAE calculation.|
|**Log Transform**|Many top solutions use $\log(1 + \text{unit\_sales})$ for training to handle the skew, then transform back.|
|**Zipping**|Per the instructions, zip your CSV to save time on the upload.|

Do you want to see a specific Python snippet for reconstructing the missing zeros or handling the transferred holidays?### Evaluation

Submissions will be evaluated using the Weighted Mean Absolute Error (WMAE), which measures the average absolute difference between predicted and actual sales while assigning different importance to each item.

WMAE is defined as:

### Where:

- y_i = actual sales
- ŷ_i = predicted sales
- w_i = weight assigned to each item
- n = total number of samples

### ⚖️ Item Weights

Items are weighted based on their importance:

Perishable items: 1.25 Non-perishable items: 1.00

The weights are derived from the items.csv file, which contains item-level metadata. This file provides a binary indicator for each product (0/1), representing whether the item is considered perishable or not. During evaluation, this indicator is internally mapped to the corresponding weight value used in the WMAE calculation.

_Metric Intuition_

WMAE represents the average prediction error in sales units while giving more importance to critical products. It is easy to interpret and aligns well with real-world inventory and supply chain decision-making.## train.csv

Training data includes the target **unit_sales** by `date`, `store_nbr`, and `item_nbr`, along with a unique `id` for each row.

- The target `unit_sales` can be:
    
    - Integer (e.g., a bag of chips)
    - Float (e.g., 1.5 kg of cheese)
- Negative values in `unit_sales` represent returns of a product.
    
- The `onpromotion` column indicates whether a given `item_nbr` was on promotion for a specific `date` and `store_nbr`.
    
- Approximately 16% of values in `onpromotion` are missing (NaN)