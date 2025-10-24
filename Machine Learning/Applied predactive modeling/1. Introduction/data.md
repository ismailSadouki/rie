# Music Genre
This data set was published as a contest data set on the TunedIT web
site (http://tunedit.org/challenge/music-retrieval/genres). In this competi-
tion, the objective was to develop a predictive model for classifying music
into six categories. In total, there were 12,495 music samples for which 191
characteristics were determined. The response categories were not balanced
(Fig. 1.1), with the smallest segment coming from the heavy metal category
(7 %) and the largest coming from the classical category (28 %). All predic-
tors were continuous; many were highly correlated and the predictors spanned
diﬀerent scales of measurement. This data collection was created using 60 per-
formers from which 15–20 pieces of music were selected for each performer.
Then 20 segments of each piece were parameterized in order to create the
ﬁnal data set. Hence, the samples are inherently not independent of each
other.
![](https://i.imgur.com/jt1tNdo.png)
# Grant Applications
This data set was also published for a competition on the Kaggle web site
(http://www.kaggle.com). For this competition, the objective was to develop
a predictive model for the probability of success of a grant application. The
historical database consisted of 8,707 University of Melbourne grant appli-
cations from 2009 and 2010 with 249 predictors. Grant status (either “un-
successful” or “successful”) was the response and was fairly balanced (46 %
successful). The web site notes that current Australian grant success rates
are less than 25 %. Hence the historical database rates are not representative
of Australian rates. Predictors include measurements and categories such as
Sponsor ID, Grant Category, Grant Value Range, Research Field, and De-
partment and were continuous, count, and categorical. Another notable char-
acteristic of this data set is that many predictor values were missing (83 %).
Furthermore, the samples were not independent since the same grant writers
occurred multiple times throughout the data. These data are used throughout
the text to demonstrate diﬀerent classiﬁcation modeling techniques.

# Hepatic Injury
A data set from the pharmaceutical industry was used to develop a model
for predicting compounds’ probability of causing hepatic injury (i.e., liver
damage). This data set consisted of 281 unique compounds; 376 predictors
were measured or computed for each. The response was categorical (either
“does not cause injury,”“mild injury,” or “severe injury”) and was highly un-
balanced (Fig. 1.2). This variety of response often occurs in pharmaceutical
data because companies steer away from creating molecules that have unde-
sirable safety characteristics. Therefore, well-behaved molecules often greatly
outnumber undesirable molecules. The predictors consisted of measurements
from 184 biological screens and 192 chemical feature predictors. The biolog-
ical predictors represent activity for each screen and take values between 0
and 10 with a mode of 4. The chemical feature predictors represent counts of
important substructures as well as measures of physical properties that are
thought to be associated with hepatic injury. A more extensive description
of these types of predictors is given in Chap. 5.
![](https://i.imgur.com/UkyMgts.png)


# Permeability
This pharmaceutical data set was used to develop a model for predicting com-
pounds’ permeability. In short, permeability is the measure of a molecule’s
ability to cross a membrane. The body, for example, has notable membranes
between the body and brain, known as the blood–brain barrier, and between
the gut and body in the intestines. These membranes help the body guard
critical regions from receiving undesirable or detrimental substances. For an
orally taken drug to be eﬀective in the brain, it ﬁrst must pass through the
intestinal wall and then must pass through the blood–brain barrier in order
to be present for the desired neurological target. Therefore, a compound’s
ability to permeate relevant biological membranes is critically important to
understand early in the drug discovery process. Compounds that appear to
be eﬀective for a particular disease in research screening experiments but
appear to be poorly permeable may need to be altered in order to improve
permeability and thus the compound’s ability to reach the desired target.
Identifying permeability problems can help guide chemists towards better
molecules.
Permeability assays such as PAMPA and Caco-2 have been developed to
help measure compounds’ permeability (Kansy et al. 1998). These screens are
eﬀective at quantifying a compound’s permeability, but the assay is expen-
sive labor intensive. Given a suﬃcient number of compounds that have been
screened, we could develop a predictive model for permeability in an attempt
to potentially reduce the need for the assay. In this project there were 165
unique compounds; 1,107 molecular ﬁngerprints were determined for each.
A molecular ﬁngerprint is a binary sequence of numbers that represents the
presence or absence of a speciﬁc molecular substructure. The response is
highly skewed (Fig. 1.3), the predictors are sparse (15.5 % are present), and
many predictors are strongly associated.

![](https://i.imgur.com/bkH6XZx.png)
# Chemical Manufacturing Process
This data set contains information about a chemical manufacturing process,
in which the goal is to understand the relationship between the process and
the resulting ﬁnal product yield. Raw material in this process is put through
a sequence of 27 steps to make the ﬁnal pharmaceutical product. The start-
ing material is generated from a biological unit and has a range of quality
and characteristics. The objective in this project was to develop a model to
predict percent yield of the manufacturing process. The data set consisted of
177 samples of biological material for which 57 characteristics were measured.
Of the 57 characteristics, there were 12 measurements of the biological start-
ing material and 45 measurements of the manufacturing process. The process
variables included measurements such as temperature, drying time, washing
time, and concentrations of by-products at various steps. Some of the pro-
cess measurements can be controlled, while others are observed. Predictors
are continuous, count, categorical; some are correlated, and some contain
missing values. Samples are not independent because sets of samples come
from the same batch of biological starting material.

# Fraudulent Financial Statements
Fanning and Cogger (1998) describe a data set used to predict management
fraud for publicly traded companies. Using public data sources, such as U.S.
Securities and Exchange Commission documents, the authors were able to
identify 102 fraudulent ﬁnancial statements. Given that a small percentage
of statements are fraudulent, they chose to sample an equivalent number1
of non-fraudulent companies, which were sampled to control for important
factors (e.g., company size and industry type). Of these data, 150 data points
were used to train models and the remaining 54 were used to evaluate them.
The authors started the analysis with an unidentiﬁed number of predictors
derived from key areas, such as executive turnover rates, litigation, and debt
structure. In the end, they used 20 predictors in their models. Examples
include the ratio of accounts receivable to sales, the ratio of inventory to
sales, and changes in the gross margins between years. Many of the predictor
variables of ratios share common denominators (e.g., the ratio of accounts re-
ceivable to sales and the ratio of inventory to sales). Although the actual data
points were not published, there is likely to be strong correlations between
predictors.

From a modeling perspective, this example is interesting for several reasons.
First, because of the large class imbalance, the frequencies of the two classes
in the data sets were very diﬀerent from the population that will be predicted
with severe imbalances. This is a common strategy to minimize the conse-
quences of such an imbalance and is sometimes referred to as “down-sampling”
the data. Second, the number of possible predictors was large compared to
the number of samples. In this situation, the selection of predictors for the
models is delicate as there are only a small number of samples for selecting
predictors, building models, and evaluating their performance. Later chapters
discuss the problem of over-ﬁtting, where trends in the training data are not
found in other samples of the population. With a large number of predictors
and a small number of data points, there is a risk that a relevant predictor
found in this data set will not be reproducible.
![](https://i.imgur.com/SgMyKtM.png)
