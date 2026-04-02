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
we now introduce a categorizing framework that groups many existing feature selection algorithms into distinct categories, and summarize individual algorithms based on this framework.

### 3.1 A Categorizing Framework
In order to better understand the inner
instrument of each algorithm and the commonalities and
differences among them, we develop a three-dimensional
categorizing framework (shown in Table 1) based on the
previous discussions.
![](https://i.imgur.com/ozpLXD8.png)
The categorizing framework serves three
roles. First, it reveals relationships among different algo-
rithms: Algorithms in the same block (category) are most
similar to each other (i.e., designed with similar search
strategies and evaluation criteria, and for the same type of
data mining tasks). Second, it enables us to focus our
selection of feature selection algorithms for a given task on a
relatively small number of algorithms out of the whole
body. 

For example, knowing that feature selection is
performed for classification, predicative accuracy of a
classifier is suitable to be the evaluation criterion, and
complete search is not suitable for the limited time allowed,
we can conveniently limit our choices to two groups of
algorithms in Table 1: one is defined by Classification,
Wrapper, and Sequential; the other is by Classification,
Wrapper, and Random. Both groups have more than one
algorithm available. 

Third, the framework also reveals
what are missing in the current collection of feature
selection algorithms.


## 3.2 Filter Algorithm
![](https://i.imgur.com/EMbeG9c.png)
## 3.3 Wrapper Algorithm
A generalized wrapper algorithm (shown in Table 3) is very
similar to the generalized filter algorithm except that it
utilizes a predefined mining algorithm A instead of an
independent measure M for subset evaluation.
![](https://i.imgur.com/XbKDUwq.png)
different
mining algorithms will produce different feature selection
results. Varying the search strategies via the function
generate(D) and mining algorithms (A) can result in
different wrapper algorithms. Since mining algorithms are
used to control the selection of feature subsets, the wrapper
model tends to give superior performance as feature subsets
found are better suited to the predetermined mining
algorithm
## 3.4 Hybrid Algorithm
![](https://i.imgur.com/WQ8YawE.png)
the hybrid model is recently proposed to handle large data sets  A typical hybrid algorithm (shown in Table 4) makes
use of both an independent measure and a mining
algorithm to evaluate feature subsetss: It uses the
independent measure to decide the best subsets for a
given cardinality and uses the mining algorithm to select
the final best subset among the best subsets across different cardinalities.

# 4 TOWARD AN I NTEGRATED S YSTEM FOR I NTELLIGENT FEATURE S ELECTION
In this section, we present an integrated approach to intelligent feature
selection:
## 4.1 A Unifying Platform
At the top, knowledge and data about feature selection are
two key determining factors. Currently, the knowledge
factor covers Purpose of feature selection, Time concern,
expected Output Type, and M/N Ratio—the ratio between the
expected number of selected features M and the total
number of original features N. The data factor covers Class
Information, Feature Type, Quality  of data, and N/I Ratio—the
ratio between the number of features N and the number of
instances I. Each dimension is discussed below.

For general purpose of redundancy and/or irrele-
vancy removal, algorithms in the filter model are good
choices as they are unbiased and fast. To enhance the
mining performance, algorithms in the wrapper model
should be preferred than those in the filter model as they
are better suited to the mining algorithms, Sometimes, algorithms in the hybrid model are needed to serve more complicated purposes.

 When time is not a critical issue, algorithms with
complete search are recommended to achieve higher
optimality of results; otherwise, algorithms with sequential
search or random search should be selected for fast results.

The output type of feature selection can sometimes be
known a priori. This aspect divides feature selection
algorithms into two groups: ranked list and minimum
subset. The real difference between the two is about the
order among the selected features. There is no order among
the features in a selected subset. One cannot easily remove
any more feature from the subset, but one can do so for a
ranked list by removing the least important one. Back to the
previous example, among WSFG, WSBG, and LVW, if we
expect to get a ranked list as the result, LVW returning a
minimum subset will be eliminated from the final choice.

The M=N ratio is also very useful in determining a
proper search strategy. If the number of relevant features
(M) is expected to be small, a forward complete search
strategy can be afforded; if the number of irrelevant features
(N  M) is small, a backward complete search strategy can
be adopted even in time critical situations. If we have the
prior knowledge that the number of irrelevant features is
significantly larger than the number of relevant ones, WSFG
using sequential forward search is considered a better
choice than WSBG using sequential backward search.

The quality of data is about whether data contains missing
values or noisy data. Different feature selection algorithms
require different levels of data quality to perform well. Some
applications require more preprocessing such as value
discretization and missing value treatment, while
others are less stringent in this regard.
![](https://i.imgur.com/2zDs8D3.png)
## 4.2 Toward an Integrated System
![](https://i.imgur.com/u2v3jDp.png)
We focus on feature selection algorithms using the
consistency evaluation criterion. The four representative
algorithms employ different search strategies: Focus—for-
ward exhaustive search, ABB—backward complete search,
QBB—random search plus ABB, and SetCover—sequential
search. Both theoretical analysis and experimental results
suggest that each algorithm has its own strengthes and
weaknesses concerning speed and optimality of results.
To guide the selection of a suitable algorithm among the
four, the number of relevant features M is estimated as M' or M''.
where M' is an estimate of M by
SetCover, and M'' an estimate of M by QBB. With this
system, Focus or ABB is recommended when either M0 or
N - M' is small because they guarantee the optimal
selected subset. However, the two could take an impratical
long time to converge when the two conditions are not true.

Therefore, either SetCover or QBB will be used based on the
comparison of M' and M''. The two algorithms do not
guarantee optimal subsets, but they are efficient in generat-
ing near optimal subsets.


# 5 REAL-WORLD APPLICATIONS OF FEATURE SELECTION
