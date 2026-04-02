### **Frequency Encoding**

**Definition:**  
Frequency encoding is a technique to convert **categorical variables** into **numeric values** by replacing each category with its **frequency (proportion) in the dataset**.

### **How it works**

1. Count how many times each category appears in the training set.
    
2. Divide by the total number of rows (optional; gives proportion instead of count).
    
3. Replace the categorical value with its frequency.
    

**Example:**

| Breed1 | Count | Frequency |
| ------ | ----- | --------- |
| BreedA | 500   | 0.10      |
| BreedB | 1000  | 0.20      |
| BreedC | 3500  | 0.70      |
### **When to use**

- High-cardinality categorical features (e.g., `Breed1`, `RescuerID`)
    
- Tree-based models like **XGBoost**, **LightGBM**, **CatBoost**
    
- When one-hot encoding would create **too many columns**
### **Pros**

- Reduces dimensionality compared to one-hot encoding
    
- Provides some numeric signal about category popularity
    
- Simple to implement

### **Cons / Cautions**

- Rare categories get very small numbers → may be ignored by the model
    
- Can **leak information** if applied incorrectly (e.g., using target encoding on the test set)
    
- Sometimes, for tree models, it may not improve accuracy compared to **keeping integer IDs**
```
freq = train['Breed1'].value_counts() / len(train)
train['Breed1_freq'] = train['Breed1'].map(freq)
submit['Breed1_freq'] = submit['Breed1'].map(freq)  # map using training frequencies

```

💡 **Key Tip:** Always **map using training data only** to avoid data leakage.
