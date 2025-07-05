link: https://www.researchgate.net/publication/243122973_Yu_L_Toward_Integrating_Feature_Selection_Algorithm_for_Classification_and_Clustering_IEEE_Transaction_on_Knowledge_and_Data_Engineering_174_491-502?enrichId=rgreq-ad39296ba172dd838993496d325fd152-XXX&enrichSource=Y292ZXJQYWdlOzI0MzEyMjk3MztBUzoxMDEyMjA2MzUxMTk2MjhAMTQwMTE0NDI4ODUzMw%3D%3D&el=1_x_3&_esc=publicationCoverPdf


**Abstract**—This paper introduces concepts and algorithms of feature selection, surveys existing feature selection algorithms for
classification and clustering, groups and compares different algorithms with a categorizing framework based on search strategies,
evaluation criteria, and data mining tasks, reveals unattempted combinations, and provides guidelines in selecting feature selection
algorithms. With the categorizing framework, we continue our efforts toward building an integrated system for intelligent feature
selection. A unifying platform is proposed as an intermediate step. An illustrative example is presented to show how existing feature
selection algorithms can be integrated into a meta algorithm that can take advantage of individual algorithms. An added advantage of
doing so is to help a user employ a suitable algorithm without knowing details of each algorithm. Some real-world applications are
included to demonstrate the use of feature selection in data mining. We conclude this work by identifying trends and challenges of
feature selection research and development



# Introduction
many problems related to feature selection have been
shown to be NP-hard.
A typical feature selection process consists of four basic steps (shown in Fig. 1), namely, subset generation, subset evaluation, stopping criterion, and result validation.

![[Pasted image 20250705164245.png]]

Feature selection algorithms designed with different
evaluation criteria broadly fall into three categories: the
filter model , the wrapper model, and the hybrid model.
The filter model relies on general characteristics of the data to
evaluate and select feature subsets without involving any
mining algorithm. The wrapper model requires one pre-
determined mining algorithm and uses its performance as
the evaluation criterion. It searches for features better suited
to the mining algorithm aiming to improve mining
performance, but it also tends to be more computationally
expensive than the filter model, The hybrid model attempts to take advantage of the two models by exploiting their different evaluation criteria in different search stages.

# 2 GENERAL P ROCEDURE OF FEATURE S ELECTION

### 2.1 Subset Generation
**Complete Search**. It guarantees to find the optimal result
according to the evaluation criterion used. Hence, although the
order of the search space is O(2^N), a smaller number of
subsets are evaluated. Some examples are branch and bound, and beam search .
**Sequential Search**. It gives up completeness and thus
risks losing optimal subsets. There are many variations to
the greedy hill-climbing approach, such as sequential forward
selection, sequential backward elimination, and bidirectional
selection. Algorithms with sequential search are simple to implement and fast in producing results as the order of the search space is usually O(N^2) or less.

**Random Search**. It starts with a randomly selected
subset and proceeds in two different ways. One is to follow
sequential search, which injects randomness into the above
classical sequential approaches. Examples are random-start
hill-climbing and simulated annealing . The other is to
generate the next subset in a completely random manner
(i.e., a current subset does not grow or shrink from any
previous subset following a deterministic rule), also known
as the Las Vegas algorithm. For all these approaches, the
use of randomness helps to escape local optima in the
search space, and optimality of the selected subset depends
on the resources available.


### 2.2 Subset Evaluation
an optimal subset selected using one criterion may not be optimal according to another criterion.
##### 2.2.1 Independent Criteria
an independent criterion is used in algorithms of the filter model.
These criteria are **independent** because they don’t care how well a specific classifier (like SVM or Random Forest) would perform with the features.

**Distance measures** are also known as separability,
divergence, or discrimination measures. For a two-class
problem, a feature X is preferred to another feature Y if X
induces a greater difference between the two-class condi-
tional probabilities than Y.
Imagine two histograms (or probability density functions):

- One for feature X values when the class is **0**
    
- One for feature X values when the class is **1**
    

If those two distributions **overlap heavily**, feature X is not helpful — it can't distinguish the classes.

If the distributions are **far apart**, then feature X is very helpful — it separates the classes well.

**Information measures** typically determine the informa-
tion gain from a feature. The information gain from a
feature X is defined as the difference between the prior
uncertainty and expected posterior uncertainty using X.
Feature X is preferred to feature Y if the information gain
from X is greater than that from Y.

**Dependency measures** are also known as correlation
measures or similarity measures. 

**Consistency measures** are characteristically different
from the above measures because of their heavy reliance
on the class information and the use of the Min-Features
bias in selecting a subset of features. These measures
attempt to find a minimum number of features that separate
classes as consistently as the full set of features can. An
inconsistency is defined as two instances having the same
feature values but different class labels
Example

| Feature 1 | Feature 2 | Class |
| --------- | --------- | ----- |
| A         | B         | 0     |
| A         | B         | 1     |
Same features → Different class → ❌ **Inconsistent**
- **Consistency**: No two instances with same feature values should have different class labels.
    
- **Inconsistency** = violation of this rule.
    
- Goal = **select minimal feature subset** with minimal inconsistency.
    
- Strongly depends on **class labels** and enforces **simplicity** (few features).

##### 2.2.2 Dependent Criteria
A dependent criterion used in the wrapper model requires a
predetermined mining algorithm in feature selection and
uses the performance of the mining algorithm applied on
the selected subset to determine which features are selected.

### 2.3 Stopping Criteria
Some frequently used stopping criteria are:
1. The search completes.
2. Some given bound is reached, where a bound can be
a specified number (minimum number of features or
maximum number of iterations).
3. Subsequent addition (or deletion) of any feature
does not produce a better subset.
4. A sufficiently good subset is selected (e.g., a subset
may be sufficiently good if its classification error rate
is less than the allowable error rate for a given task).

## 2.4 Result Validation

# 3 A CATEGORIZING FRAMEWORK FOR F EATURES ELECTION ALGORITHMS
