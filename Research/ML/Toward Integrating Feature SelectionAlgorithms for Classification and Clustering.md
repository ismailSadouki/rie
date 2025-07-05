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
