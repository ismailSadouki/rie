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

$$
\text{PVE of PC}_k = \frac{\lambda_k}{P}  
$$

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




# Data Adequacy
Before performing PCA, it is essential to ensure that the data form a **coherent set** and that the variables are **correlated** with each other. Two commonly used tests are the **Bartlett’s test** and the **Kaiser-Meyer-Olkin (KMO) test**.

#### Bartlett’s Test
This test examines the correlation matrix and provides the **probability of the null hypothesis**, which assumes **no correlation** between variables.
### **KMO Test**

The KMO test measures **the proportion to which the variables form a coherent set** and **adequately represent the results**.  
It indicates whether the data are suitable for PCA.
![](https://i.imgur.com/xcctCQ2.png)



**Notion of Distance Between Two Statistical Units**
Two individuals are **similar** if their values are **close for all variables**.  
The standard measure is the **squared Euclidean distance** between individuals i and k:

![](https://i.imgur.com/bjO2v6b.png)
![](https://i.imgur.com/Xka95k3.png)
![](https://i.imgur.com/UA3eVMw.png)



**a) Notion de l’inertie**
![](https://i.imgur.com/E9bQ8Dt.png)
### **What is the “centre de gravité” (center of gravity) of a data cloud?**

In multivariate statistics (like PCA), you have a dataset with **n individuals** (rows) and **p variables** (columns). You can imagine these individuals as points in a **p-dimensional space**.

The **centre de gravité** of this cloud is simply the point whose coordinates are the **means of each variable**.

![](https://i.imgur.com/GOz6JU0.png)

Intuition: The center of gravity is where the cloud "balances".
![](https://i.imgur.com/u2saV0e.png)

**b. Définition de l’inertie totale** :
![](https://i.imgur.com/SckYy5m.png)
![](https://i.imgur.com/Jllepmk.png)
![](https://i.imgur.com/261DcFK.png)
![](https://i.imgur.com/MSIyw3n.png)


# 2.2.4. Ajustement du nuage des individus
(Adjustment (Approximation) of the Cloud of Individuals in PCA)
![](https://i.imgur.com/PcS00Hp.png)


To find the _best approximating image_ of the cloud of individuals, PCA follows these steps:
1. Find the axis that distorts the cloud as little as possible
![](https://i.imgur.com/ygjO79S.png)
![](https://i.imgur.com/NJnpTej.png)
![](https://i.imgur.com/RLySU5v.png)
![](https://i.imgur.com/RoqTrjc.png)
![](https://i.imgur.com/caQ1O6h.png)
# 🔍 **Intuition**

PCA finds axes that give the **best possible approximation** of the data cloud by:

- **maximizing the variance** (inertia) along each axis,
    
- **minimizing the reconstruction error** when projecting points.
    

The first axis captures the **largest direction of variability**,  
the second captures the next largest (under the constraint of orthogonality),  
and so on.


# 2.2.5. Matrice à diagonaliser
##### **Analysis of the Cloud of Individuals and the Matrix Used in PCA**

When analyzing the cloud of individuals in Rp\mathbb{R}^pRp, PCA begins by applying two transformations:

1. **Move the origin to the center of gravity** of the cloud  
    → this corresponds to **centering the variables**.
    
2. **(In the case of standardized PCA)**  
    Change the scales of the axes  
    → this corresponds to **dividing each variable by its standard deviation** (normalizing).
    

After centering and possibly standardizing the data, we obtain a transformed data matrix XXX.

![](https://i.imgur.com/AJVi0Hc.png)
![](https://i.imgur.com/YVy2g7K.png)
<mark>The matrix we must diagonalize in standardized PCA is simply the **correlation matrix**.</mark>

# 2.2.6. Axes factoriels
![](https://i.imgur.com/hqEXE2H.png)
![](https://i.imgur.com/lxU8Ism.png)
![](https://i.imgur.com/7sZ1fo1.png)
![](https://i.imgur.com/xcXBfMa.png)
![](https://i.imgur.com/E9zdjoh.png)
![](https://i.imgur.com/WMeTN8D.png)
![](https://i.imgur.com/IWEjUuf.png)



# 2.3. Etude des variables
 
 **Distance of a Variable from the Origin in Standardized PCA**

In **standardized PCA (ACP normée)**:

- Each **variable** is considered as a **point in an n-dimensional space** (with n individuals).
    
- We want to measure its distance from the **origin**.

![](https://i.imgur.com/wpPzHYj.png)
![](https://i.imgur.com/jpHIeju.png)
![](https://i.imgur.com/px3Lx0Z.png)


## 2.3.2. Distance entre deux points variables j et jˊ
![](https://i.imgur.com/XRDLg8d.png)
![](https://i.imgur.com/8ASyL8V.png)
![](https://i.imgur.com/CzE6sLf.png)
![](https://i.imgur.com/L2Rw8cD.png)
![](https://i.imgur.com/E7iIk8L.png)
![](https://i.imgur.com/35WmzKz.png)
![](https://i.imgur.com/RawmaNR.png)
![](https://i.imgur.com/Ud1Inm0.png)
Alors, la distance entre deux variables s’interprète en termes de corrélation

# 2.3.3. Axe factoriels ou composantes principales
![](https://i.imgur.com/rPhTpgF.png)
![](https://i.imgur.com/FpU8Ek2.png)
![](https://i.imgur.com/MSyHmdo.png)
![](https://i.imgur.com/dpoWWvc.png)
![](https://i.imgur.com/0Iy1tmT.png)
![](https://i.imgur.com/PzE20Uh.png)
![](https://i.imgur.com/LSDefGI.png)
![](https://i.imgur.com/DBCoHLJ.png)
![](https://i.imgur.com/YC5vJae.png)
