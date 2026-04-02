Let xik be the value taken by individual i for variable k, where i varies from 1 to I and k from 1 to K.
![](https://i.imgur.com/XpsFZXY.png)
# Studying Individuals
the distance between two individuals i and l is expressed as
![](https://i.imgur.com/YWFwEII.png)
The operation of reduction (also referred to as standardising), which con-
sists of considering (xik − x̄k )/sk rather than xik , modifies the shape of the
cloud by harmonising its variability in all the directions of the original vectors
(i.e., the K variables). Geometrically, it means choosing standard deviationsk as a unit of measurement in direction k. This operation is essential if the
variables are not expressed in the same units. Even when the units of mea-
surement do not differ, this operation is generally preferable as it attaches
the same importance to each variable. Therefore, we will assume this to be
the case from here on in. Standardised PCA occurs when the variables are
centred and reduced, and unstandardised PCA when the variables are only
centred. When not otherwise specified, it may be assumed that we are using
standardised PCA.

![](https://i.imgur.com/uIY0LMa.png)

### **Breaking it down**

1. **Mechanical analogy**
    
    - PCA often uses a **geometric/mechanical interpretation**.
        
    - (O) = center of gravity (origin after centering the data)
        
    - (OHi) = vector from the center of gravity to an individual (i) (a point in the cloud (N_I))
        
    - “Inertia” = total variance of the points around the center
        
2. **Inertia of the cloud**
    
    - In mechanics, **inertia** measures how mass is distributed relative to an axis.
        
    - In PCA, **variance plays the same role as mass distribution**.
        
    - So the “inertia of the projection of (N_I)” = variance of the points after projecting them onto a direction.
        
3. **Criterion of PCA**
    
    - PCA looks for the **direction that maximizes the projected variance**.
        
    - Geometrically: find the line (axis) along which points are most spread out.
        
    - This direction is the **first principal component**.
        
    - Maximizing the variance (or inertia) ensures the principal components capture as much information as possible about the dataset’s variability.
        
![](https://i.imgur.com/FpHiiDF.png)
meaning:
If individuals have different weights pip_ipi​ (e.g., some samples are more important or represent more population units), then the **inertia becomes weighted**.
- PCA now finds the direction that **maximizes the weighted variance**.


**Best Axial Representation of the Cloud $N_I$**

**Focusing on individuals only**

- Usually, PCA considers both **individuals (rows)** and **variables (columns)**.
    
- In some cases, we might want to **analyze only the cloud of individuals**, ignoring variables for now.

**Finding the best axis**

- The “best axis” u1u_1u1​ is the **direction along which the projected points spread the most**.
    
- Mathematically: maximize
    
![](https://i.imgur.com/KaNht78.png)

    
- This is exactly the **first principal component** of the individuals.

**Nested representations**

- Once the first component $u_1$​ is found, we can find a **plane P** that contains $u_1$​ and maximizes the variance of projected points in that plane.
    
- In other words:
    
    - The **“best” plane** contains the **“best” axis**.
        
    - This property is called **nested representations**: each higher-dimensional representation contains the previous lower-dimensional components.


In short:

> PCA finds axes one by one, each axis maximizing variance. Higher-dimensional subspaces (planes) always contain the axes found in lower dimensions — that’s what “nested” means.



An illustration of this property is presented in
Figure 1.6. Planets, which are in a three-dimensional space, are traditionally
represented on a component. This component determines their positions as
well as possible in terms of their distances from one other (in terms of inertia
of the projected cloud). We can also represent planets on a plane according
to the same principle: to maximise the inertia of the projected scatterplot
(on the plane). This best plane representation also contains the best axial
representation.We define plane P by two nonlinear vectors chosen as follows: vector u1 ,
which defines the best axis (and which is included in P ), and vector u2 of
the plane P orthogonal to u1 . Vector u2 corresponds to the vector which
expresses the greatest variability of NI once that which is expressed by u1 isremoved. In other words, the variability expressed by u2 is the best coupling
and is independent of that expressed by u1 .
![](https://i.imgur.com/4MhwF7P.png)


### Sequence of Axes for Representing NI
![](https://i.imgur.com/3vcVtQb.png)
Here’s a clear explanation of this passage in the context of PCA:

---

### **Nested Subspaces in PCA**

1. **Objective**
    
    - We want to find a series of **subspaces of increasing dimension** (s = 1, 2, ..., S).
        
    - Each subspace should **maximize the inertia (variance)** of the projected points for its dimension.
        
2. **Inertia in a subspace**
    
    - For a subspace of dimension (s), let (H_i) be the **projection of individual (i)** onto this subspace.
        
    - The **criterion to maximize** is:  
        [  
        \sum_{i=1}^I OHi^2  
        ]  
        where (OHi) is the vector from the center of gravity to the projected point (H_i).
        
    - In simpler words: choose the subspace so that the points spread out **as much as possible** in that subspace.
        
3. **Nested subspaces**
    
    - The subspaces are **nested**, meaning:
        
        - The subspace of dimension (s) **contains all smaller subspaces** of dimension (t < s).
            
        - This ensures that the first principal component (1D) is contained in the first 2D plane, which is contained in the first 3D space, and so on.
            
4. **Orthogonal components**
    
    - Each new vector (u_s) that defines the subspace of dimension (s) is chosen to be **orthogonal to all previous vectors** (u_t) ((1 \le t < s)).
        
    - This guarantees that each principal component captures **new, uncorrelated variance**.
        

---

### **Intuition**

- Start with 1D: find the line where points spread the most → first PC.
    
- Then 2D: find a plane that maximizes spread, but it **must contain the first line** → second PC is orthogonal to the first.
    
- Repeat for higher dimensions: each dimension **adds a new direction of maximal variance**, orthogonal to all previous ones.
    

---

### **In short**

> PCA builds **nested subspaces** step by step, each capturing as much variance as possible, with each new axis orthogonal to the previous ones.

---
---

# How Are the Components Obtained?
![](https://i.imgur.com/LxPL822.png)
<mark>Remark
When variables are centred but not standardised, the matrix to be diago-
nalised is the variance–covariance matrix.</mark>

Here’s a clear explanation of this passage in the context of PCA:

---

### **PCA and Diagonalisation of the Correlation Matrix**

1. **Correlation (or covariance) matrix**
    
    - PCA starts with the **correlation matrix** (or covariance matrix) of the dataset.
        
    - This matrix describes **how variables vary together**.
        
2. **Eigenvectors and eigenvalues**
    
    - **Eigenvectors** → directions in space (vectors (u_s)) along which the variance is maximized.
        
    - **Eigenvalues** → magnitude of variance captured along each eigenvector.
        
        - Denoted (\lambda_s), ranked **in descending order**: (\lambda_1 \ge \lambda_2 \ge \dots \ge \lambda_K).
            
3. **Interpretation of eigenvalues**
    
    - (\lambda_s) = **inertia (variance) of the cloud (N_I) projected onto component (s)**.
        
    - Also called **explained variance** for that component.
        
    - Example: (\lambda_1 = 50%) → first principal component explains 50% of total variance.
        
4. **Full PCA (all components)**
    
    - If you calculate **all eigenvectors** ((S = K)), you get a **new basis** for the K-dimensional space (\mathbb{R}^K).
        
    - PCA = **change of basis**:
        
        - First eigenvectors → capture most variance → most important for understanding data.
            
        - Last eigenvectors → capture very little variance → often discarded in dimensionality reduction.
            

---

### **Intuition**

- Imagine a cloud of points in 3D:
    
    1. First eigenvector → line along which points are **most stretched**.
        
    2. Second eigenvector → direction **orthogonal to the first**, capturing next largest spread.
        
    3. Third eigenvector → orthogonal to both, smallest spread.
        
- Together, they **recreate the original space**, just in a **more meaningful order**.
    

---

In short:

> PCA diagonalizes the correlation matrix → eigenvectors = principal axes, eigenvalues = variance explained along each axis → first axes matter most, later axes less.

---




In the context of PCA, **“projecting inertia”** can sound abstract at first, but it’s actually very intuitive once you connect it to variance. Let’s break it down carefully.

---

### **1️⃣ Inertia = Variance**

- In PCA, **inertia** is just another word for **variance** of the cloud of points (N_I).
    
- Geometrically: it measures **how spread out the points are around the center of gravity**.
    
- Mathematically:  
    [  
    \text{Inertia} = \sum_{i=1}^I | OHi |^2  
    ]  
    where (OHi) is the vector from the center (O) to individual (i).
    

---

### **2️⃣ Projection onto a component**

- When we pick a direction (a vector (u_s)) — a principal component — we can **project each point onto that line**.
    
- Let (H_i) be the projection of point (i) on (u_s).
    

---

### **3️⃣ Projected inertia**

- The **projected inertia** is the variance of the points **after projection**:  
    [  
    \sum_i | H_i |^2  
    ]
    
- Interpretation: how much of the total variance (inertia) is **captured along this direction**.
    
- In other words, it measures **how much the cloud “spreads out” along that component**.
    

---

### **4️⃣ Why it matters**

- PCA finds the direction (u_1) that **maximizes projected inertia**.
    
- The first component captures the **largest possible variance**.
    
- Subsequent components capture the **largest remaining variance**, orthogonal to previous components.
    

---

### **Analogy**

- Imagine a shadow of a 3D cloud of points on a line:
    
    - The **length of the shadow** = projected inertia.
        
    - PCA picks the line along which the shadow is **longest** → first principal component.
        

---

**In short:**

> **Projecting inertia** = measuring how much variance (spread) the data have along a chosen direction. PCA finds directions where this projected variance is maximized.

---

---
---



# Representation of the Variables as an Aid for Interpreting the Cloud of Individuals



