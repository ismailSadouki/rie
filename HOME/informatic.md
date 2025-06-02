	
## 🧪 **T Test for Independent Samples**

### 🔍 **Hypotheses:**

- **H₀ (null hypothesis):** 𝜇𝐹 = 𝜇𝐻 → The **means** of `TAV` for both genres (e.g., Female and Male) are equal.
    
- **H₁ (alternative hypothesis):** 𝜇𝐹 ≠ 𝜇𝐻 → The **means** are different.
#### Conditaions
- Normality
- Homo
|Test|Use When?|
|---|---|
|`var.test`|To check variance equality before t-test|
|`t.test`|To compare means (parametric)|
|`wilcox.test`|To compare medians (non-parametric)|
|`ks.test`, `shapiro.test`|To check for normality of the variable|

```
# Load Data
BDD <- read.csv2(file.choose())  # Choose your CSV file (semicolon separator)
attach(BDD)

# Sample sizes by group
N1 <- sum(Genre == unique(Genre)[1])
N2 <- sum(Genre == unique(Genre)[2])
N_total <- N1 + N2

# Step 1: Normality Check
if (N1 >= 30 & N2 >= 30) {
  normal <- TRUE
} else if (N_total >= 25) {
  ks_result <- ks.test(TAV, "pnorm", mean=mean(TAV), sd=sd(TAV))
  normal <- (ks_result$p.value >= 0.05)
} else {
  sw_result <- shapiro.test(TAV)
  normal <- (sw_result$p.value >= 0.05)
}

# Step 2: If normal, check homogeneity of variances
if (normal) {
  var_test <- var.test(TAV ~ Genre)
  equal_var <- (var_test$p.value >= 0.05)

  
  # Perform t-test
  t_result <- t.test(TAV ~ Genre, var.equal = equal_var)
  
} else {

  w_result <- wilcox.test(TAV ~ Genre)
}
```


# Échantillon Dépendant
To compare two **paired (dependent)** samples — e.g., **TAV** (Before) and **TAP** (After) — and test if their means differ significantly.
- **H₀ (null):** μ_TAV = μ_TAP → No difference between before and after.
    
- **H₁ (alt.):** μ_TAV ≠ μ_TAP → A significant difference exists.
## Conditions

- **Normality is tested on the difference**: `Dif = TAP - TAV`
    
- The test type depends on the normality of this difference.

```
# Load data
BDD <- read.csv2(file.choose())
attach(BDD)
names(BDD)

# Difference
Dif <- TAP - TAV
N <- length(Dif)

# Test normality
if (N >= 30) {
  normal <- TRUE
} else if (N >= 25) {
  ks_result <- ks.test(Dif, "pnorm", mean=mean(Dif), sd=sd(Dif))
  normal <- ks_result$p.value >= 0.05
} else {
  sw_result <- shapiro.test(Dif)
  normal <- sw_result$p.value >= 0.05
}


if (normal) {
  result <- t.test(TAV, TAP, paired = TRUE)
} else {
  result <- wilcox.test(TAV, TAP, paired = TRUE)
}

print(result)
```

# ANOVA
You are comparing the **means of 4 groups** (Stat, Fin, Eco, DSc):

- **H₀ (null):**  
    μStat=μFin=μEco=μDSc\mu_{Stat} = \mu_{Fin} = \mu_{Eco} = \mu_{DSc}μStat​=μFin​=μEco​=μDSc​  
    (All groups have the same mean)
    
- **H₁ (alt):**  
    At least one mean is different.
## Conditions to check

1. **Normality**:  
    The distribution of the residuals (or the variable Diff) in each group should be normal.
    
2. **Homogeneity of variances**:  
    The variances across the groups should be equal.

### Test for homogeneity of variances
```
leveneTest(Diff ~ Specialite)

```


### Choose the appropriate test

#### 🔹 If normality holds:

- **If variances are equal**: Use **ANOVA** and **TukeyHSD** post-hoc test
    
- **If variances are unequal**: Use **Welch ANOVA** (`oneway.test`) and **Dunnett T3** test
    

#### 🔸 If normality **does not** hold:

- Use **Kruskal-Wallis test**
    
- If significant, do **Dunn’s post-hoc test** with Bonferroni correction


``
```
# Load data and attach
BDD <- read.csv2(file.choose())
attach(BDD)

# Calculate difference
Diff <- TAP - TAV

# Load libraries for tests
library(car)        # For leveneTest()
library(PMCMRplus)  # For dunnettT3Test()
library(FSA)        # For dunnTest()

# Sample sizes per group
N <- table(Specialite)

# Step 1: Check Normality based on sample sizes and tests
if(all(N >= 30)) {
  Normal <- TRUE
} else if(sum(N) >= 25) {
  ks_result <- ks.test(Diff, "pnorm", mean=mean(Diff), sd=sd(Diff))
  Normal <- (ks_result$p.value >= 0.05)
} else {
  sw_result <- shapiro.test(Diff)
  Normal <- (sw_result$p.value >= 0.05)
}

# Step 2: Test homogeneity of variances
levene_result <- leveneTest(Diff ~ Specialite)
Homog <- (levene_result$`Pr(>F)`[1] >= 0.05)

# Step 3: Perform ANOVA or Kruskal-Wallis test
if (Normal) {
  ANV <- oneway.test(Diff ~ Specialite, var.equal = Homog)
  p_ANV <- ANV$p.value
} else {
  ANV <- kruskal.test(Diff ~ Specialite)
  p_ANV <- ANV$p.value
}

# Step 4: Post-hoc tests if ANOVA/Kruskal-Wallis significant to find **which groups d0 iffer**:
if (p_ANV < 0.05) {
  if (Normal) {
    aov_model <- aov(Diff ~ Specialite)
    if (Homog) {
      posthoc <- TukeyHSD(aov_model)
    } else {
      posthoc <- dunnettT3Test(aov_model)
    }
  } else {
    posthoc <- dunnTest(Diff ~ Specialite, data = BDD, method = "bonferroni")
  }
} else {
  print("No significant difference found by ANOVA/Kruskal-Wallis (p >= 0.05)")
}

detach(BDD)

```

# Régression Linéaire
- **TAP**: Dependent variable (response)
    
- **TAV**: Independent variable (predictor)
    
- **α\alphaα**: Intercept
    
- **β\betaβ**: Slope (coefficient of TAV)
    
- **ε\varepsilonε**: Error term
You're testing whether the predictor **TAV** significantly explains **TAP**:

- **Null hypothesis (H₀)**: β=0 (TAV has no effect on TAP)




![](https://i.imgur.com/eLwKcUS.png)


```
If (X, y are stationary) then
    If (residuals are normal) then
        If (homoskedastic) then
            → Use Linear Regression
        Else
            → Use Ridge Regression
        EndIf
    Else
        → Use Cox Proportional Hazards Regression
    EndIf
Else
    If (X and y are integrated of the same order) then
        → Use ECM (Error Correction Model)
    Else
        → Use ARDL (AutoRegressive Distributed Lag Model)
    EndIf
EndIf

```

|Case|Method|
|---|---|
|Stationary + Normal + Homoskedastic|Linear Regression|
|Stationary + Normal + Heteroskedastic|Ridge Regression|
|Stationary + Not Normal|Cox Proportional Regression|
|Non-stationary but same integration order|ECM|
|Non-stationary and different integration order|ARDL|

```
# Load required libraries
library(tseries)      # For adf.test
library(lmtest)       # For bptest
library(car)          # For ncvTest
library(MASS)         # For ridge regression (lm.ridge)
library(urca)         # For unit root and cointegration tests

# Load data
- [ ] BDD <- read.csv2(file.choose())
attach(BDD)
names(BDD)

# Step 1: Check stationarity
adf_TAV <- adf.test(TAV)   # H0: non-stationary
adf_TAP <- adf.test(TAP)

if (adf_TAV$p.value < 0.05 && adf_TAP$p.value < 0.05) {
  
  # Step 2: Run linear regression
  reg <- lm(TAP ~ TAV)
  summary(reg)
  
  # Step 3: Check residual normality
  res <- resid(reg)
  ks <- ks.test(res, "pnorm")
  sw <- shapiro.test(res)
  
  if (ks$p.value >= 0.05 && sw$p.value >= 0.05) {
    
    # Step 4: Check homoskedasticity
    bp <- bptest(reg)      # Breusch-Pagan test
    ncv <- ncvTest(reg)    # Non-constant variance test
    
    if (bp$p.value >= 0.05 && ncv$p >= 0.05) {
      cat("→ Use Linear Regression\n")
    } else {
      cat("→ Use Ridge Regression\n")
      ridge <- lm.ridge(TAP ~ TAV, lambda = seq(0, 1, 0.1))
      print(ridge)
    }
    
  } else {
    cat("→ Use Cox Proportional Hazards Regression (if time-to-event data)\n")
    # This depends on data format; not applicable in all cases
  }
  
} else {
  # Step 5: Check integration order (assume I(1) for simplicity)
  # Use ADF on differences
  adf_diff_TAV <- adf.test(diff(TAV))
  adf_diff_TAP <- adf.test(diff(TAP))
  
  if (adf_diff_TAV$p.value < 0.05 && adf_diff_TAP$p.value < 0.05) {
    # Check cointegration
    coint <- ca.po(cbind(TAP, TAV), type = "Pu")
    if (coint@cval[1,1] < coint@teststat[1]) {
      cat("→ Use Error Correction Model (ECM)\n")
      # ECM modeling would follow
    } else {
      cat("→ Use ARDL Model\n")
      # ARDL modeling would follow
    }
  } else {
    cat("→ Use ARDL Model\n")
  }
}

```


# Automation
To test whether the means of two independent groups (e.g., Male vs. Female) differ significantly for a given variable (e.g., TAV), using the most appropriate test:

- Student's t-test
    
- Welch's t-test
    
- Wilcoxon rank-sum test (non-parametric)


|Condition|Test Used|
|---|---|
|Normal & Equal Variances|Student's t-test|
|Normal & Unequal Variances|Welch's t-test|
|Not Normal|Wilcoxon rank-sum test|


```
VARS <- all.vars(X)
```
Extracts variable names from the formula.
- `VARS[1]` will be `"TAV"` and `VARS[2]` will be `"Genre"`

```
TAV <- eval(str2expression(VARS[1]))
```
Converts the string `"TAV"` into the actual variable from your data (`TAV` vector in `BDD`).

```
N <- aggregate(X, data = BDD, length)
N1 <- N[1,2] ; N2 <- N[2,2]
```
- Counts how many observations are in each group.
    
- `N1` and `N2` are the sample sizes of the two independent groups.



```
auto.t.test.indep <- function(X) {
  VARS <- all.vars(X)
  TAV <- eval(str2expression(VARS[1]))
  N <- aggregate(X, data = BDD, length)
  N1 <- N[1, 2]
  N2 <- N[2, 2]
  
  if ((N1 >= 30) & (N2 >= 30)) {
    Normal <- TRUE
  } else if ((N1 + N2) >= 25) {
    Normal <- (ks.test(TAV, "pnorm")$p.value >= 0.05)
  } else {
    Normal <- (shapiro.test(TAV)$p.value >= 0.05)
  }
  
  if (Normal) {
    Homog <- (var.test(X)$p.value >= 0.05)
    RES <- t.test(X, var.equal = Homog)
  } else {
    RES <- wilcox.test(X)
  }
  
  return(RES)
}

```


note:
```
# Student's t-test (equal variances)
t.test(TAV ~ Genre, var.equal = TRUE)

# Welch's t-test (unequal variances)
t.test(TAV ~ Genre, var.equal = FALSE)
```
If you're unsure whether variances are equal, you use **`var.test()`** (F-test) to check:
```
var.test(TAV ~ Genre)  # Returns p-value

```


    If p-value >= 0.05: variances are equal → Student's t-test

    If p-value < 0.05: variances are unequal → Welch's t-test