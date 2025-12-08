![](https://i.imgur.com/3gzQ9jj.png)
![](https://i.imgur.com/IdoVjp8.png)
Here’s a **clear and intuitive explanation** of what it means to use _standardized variables_ in PCA and why it makes interpretation easier.

---

# ✅ **Why Standardize Variables in PCA?**

PCA works by examining the **covariance matrix** of the data.  
But covariance depends on the **scale** of variables.

Example:

- Income measured in dollars (0–100,000)
    
- Age measured in years (0–80)
    

Income has a variance MUCH larger than age → PCA will think income is more “important,” but that may be just because of scale.

### ✔ Standardization fixes this.

You transform each variable:

[  
x_{ij}^{\text{std}} = \frac{x_{ij} - \bar{x}_j}{s_j}  
]

After this:

- Each variable has **mean 0**
    
- Each variable has **variance 1**
    

Now no variable dominates just because of units.

---

# 🌟 **What happens after standardization?**

When you standardize all variables, the PCA is effectively done on the **correlation matrix** instead of the covariance matrix.

Why?  
Because the correlation matrix is just the covariance matrix of standardized variables.

---

# 📌 **Important Consequences**

### **1. Total variance becomes equal to the number of variables (P)**

Before standardization:

- Total variance = sum of variances of all variables
    
- This could be any value (e.g., 1300.7)
    

After standardization:

- Each variable has variance = 1  
    → Total variance = (P)
    

If you have 7 variables:

- Total variance = 7
    

This is why PCA on standardized data is easier to interpret.

---

### **2. Proportion of variance explained (PVE) becomes simple**

Let λ₁, λ₂, …, λₚ be eigenvalues of **correlation matrix**.

These eigenvalues each represent variance captured by a principal component.

Since total variance = P:

[  
\text{PVE of PC}_k = \frac{\lambda_k}{P}  
]

This is much cleaner than dividing by an arbitrary number.

---

# 🎯 **Why it makes interpretation easier**

## ✔ Equal footing for all variables

All variables are treated equally because they all have variance 1.

## ✔ Direct, simple PVE interpretation

If P = 10 and λ₁ = 4.1 → first PC explains 41% of the variation.

## ✔ Loadings (coefficients) reflect correlations

Because each variable is standardized, PCA loadings show how strongly each variable correlates with the component.

---

# 🧠 **When should you standardize?**

✔ When variables are measured in _different units_  
✔ When variables have _very different variances_

❌ You should NOT standardize when:

- All variables are on the same scale and variance differences are meaningful (e.g., measurements all in cm)
    

---

# 🔥 **Summary**

|Step|Meaning|
|---|---|
|Standardize variables|Each variable variance = 1|
|PCA uses correlation matrix|Equivalent to PCA on standardized data|
|Total variance = P|Simplifies interpretation|
|PVE = λᵢ / P|Very intuitive|

---

If you want, I can also illustrate with a small numerical example or code in Python/R.








---
---
---
---

Here is a **clean, simple summary** of the text:

---

### **Summary**

In PCA (Principal Component Analysis), we start with a data table where rows represent individuals and columns represent quantitative variables.

We aim to:

1. **Describe individuals** by reducing the dimension of the data (e.g., from 6 variables to 2–3 dimensions) so we can better visualize similarities between individuals.
    
2. **Describe variables** by finding linear relationships between them.
    

The data matrix (X) can be viewed in two ways:

- as a cloud of points representing individuals,
    
- or as a cloud of points representing variables.
    

PCA finds the **principal axes** that best capture the variability (inertia) of these clouds.  
The process consists of:

- finding the axis through the origin that minimizes the squared distances to the data points;
    
- this axis corresponds to the **eigenvector** associated with the **largest eigenvalue** of (X'X).
    

The first eigenvector explains the most variation (inertia).  
Additional eigenvectors explain decreasing amounts of variation.  
The total variation explained by a reduced subspace equals the **sum of its eigenvalues**.

---

If you want, I can also make a super short “2–3 line summary” or a beginner-friendly explanation.
![](https://i.imgur.com/U89x22f.png)
The individuals must be **homogeneous** (for example: individuals, cars, companies — but not a mix of very different types).
![](https://i.imgur.com/s3c42ot.png)
**1st Step: Analysis of the Cloud (nuage) of Individual Points in the Space ${R}^P$**
- **Compute the correlation matrix** between the variables.  
The matrix CCC is symmetric, positive, and definite.
$$C=X^tX$$
- **Compute the eigenvalues** $λ_i$​ and sort them in decreasing order.
$$|C-λI|=0$$
- **Compute the eigenvectors** $U_i$​ associated with the eigenvalues, knowing that each eigenvector represents a **principal axis**.
![](https://i.imgur.com/Biwagi4.png)
- **Compute the principal components** (des composantes principales):
![](https://i.imgur.com/PvRvoq1.png)
![](https://i.imgur.com/kl17Cpm.png)
so **Coord(i, α)** = The value of individual i on principal component α, It tells you where the individual lies in the _reduced-dimensional space_


- **Calculation of Absolute Contributions**
(These identify the individuals that provide the most information about a given axis.)
This tells you **how much each individual influences the construction of the principal axis**.   Individuals far from the center contribute a lot.
![](https://i.imgur.com/RgUDZEp.png)
- **Calculation of Relative Contributions**
![](https://i.imgur.com/ShdisyH.png)
![](https://i.imgur.com/t9uzpay.png)
This tells you:  
➡️ _Of all the information carried by individual i, what percentage is captured by axis α?_

- If CTR is high → the individual is well represented by this axis
    
- If CTR is low → the individual does not align strongly with this axis




**2nd Step: Analysis of the Cloud of Variable Points in the Space ${R}^n$

Step 2 focuses on how variables relate to principal components.

We compute the **factor coordinates of the variables**.  
Instead of diagonalizing the matrix $X^tX$ again, we use the following **transition formula**:
![](https://i.imgur.com/QMCLEI6.png)
This tells you:

- how strongly variable **j** is correlated with principal component **α**
    
- if positive → variable moves in same direction as the axis
    
- if negative → variable moves in opposite direction

We compute the **relative contributions**:
![](https://i.imgur.com/dpomGVi.png)
This tells you:

- how much **variable j participates in building axis α**
    
- high CTR → important variable for that principal component
    
- low CTR → variable not related to that axis

Finally, we represent the variables graphically on the **correlation circle**.

