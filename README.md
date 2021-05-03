# Nutrition Intervention Project

In order for these results to be reproducible, you need to run 
1. Teacher Clean .rmd
2. Student Clean. rmd 
3. Full Merge. rmd 
4. Imputation.rmd
5. OLR analysis .rmd



## Section 1.1 NIFA Program Background Information
Healthy Schoolhouse 2.0 is a 5 year long study that is currently in its 3rd year of activity. It’s Nutrition Intervention Program is a multicomponent nutrition education program designed to empower teachers to improve nutrition literacy and prevent obesity in elementary school students in Washington, DC. The inten- tion of this program is to study the intervention effects of PD Sessions and classroom implemented nutrition lessons on student knowledge of, and attitude towards, nutrition related concepts.

## Section 1.2 Preliminary Analysis
We began our exploratory analysis as we looked at Teacher Level and Student Level data that was collected during the program. Our preliminary data started out in 2 separate SPSS files, one for student data and one for teacher data. The Student dataset contained 330 variables and 1324 observations. The teacher dataset contained 447 variables and 206 observations. Teacher level data contained teacher demographic data, along with health and health education questionnaires grouped by Teacher ID, and spanning each year that they participated in the program. Student level data was structured by a unique student code which contained student information for each year they participated within the program.
The nested and hierarchical structure of the data calls for the utilization of multilevel modeling techniques. While SPSS requires multi-level data to be in wide format to analyze, R requires data to be structured in long format. We therefore melted the data, and merged the two datasets together.The merging of the data was student centered, meaning it contained all student observations from years 1:3, and attached Teacher level data to student information based on TeacherIDs for pre and post tests. It is important to note that while some teachers attended PD Sessions, their students did not participate in the knowledge testing during the program. Those teachers will exist in our teacher_all dataset but will not exist within the full_data/ student and teacher merged dataset. The dataset contains student knowledge and attitude scores and has variables for Program Year[Which Year of the NIFA Program is it?], Student Year [How long has the student participated in the program] and Test Exposure [How many times has the student seen the test prior to this?]

## Section 1.3 Ordinal Logistic Regression
As discussed, the data collected by the NIFA project is mostly discrete numbers. This is due to the nature of the pre and post tests given to student and teachers during the program. The Teacher Health Survey consists of 38 questions while the Level 1 student questionnaire consists of a 10 questions, and the Level 2 student questionnaire consists of 18 questions. This defined nature of the test resulted in a discrete number of potential test scores or percentages as outcomes, and therefore violated assumptions of normality necessary to conduct our Multilevel Mixed Linear Model.
We determined that the best possible route to proceed with modeling would be through Ordinal Logistic Regression. An ordinal variable is defined as a variable " with a categorical data scale which describes order, and where the distinct levels of “of such a variable differ in degree of dissimilarity more than in quality (Agresti, 2010).” Our main variable of interest and our dependent variable is this analysis is the Knowledge Percent scores of the students, which by the above definition should be considered an ordinal variable. While some utilize percentages of grade outcomes as continuous or nominal variables, there are several disadvantages that should be considered during modeling (Hoffman & Franke, 1986). Agresti (2010) discusses the disadvantages of using typical regression methods on ordinal data. Some of these disadvantages include results being sensitive to the scores assigned, methods do not allow for the measurement that accounts for the error of replacing ordinal responses with continuous responses, and the models can make predicted values outside of the range of possible values (ie scoring above 100%). Lastly, if typical regression models are applied to ordinal data, the model can lead to misleading results due to “floor and ceiling effects on the dependent variable” (Agresti, 2010, section 1.3.1).


Ordinal logistic regression (OLR) is used to determine the relationship between a set of predictors and an ordered factor dependent variable. This is especially useful when you have test scores data, or proportional data. OLR is more appropriate to use than linear mixed effects models in this case because although these
observations at first seem numeric, these values are inherently categorical. For example, a 3/10 on an exam will always give you the same percentage 33.33% The most common form of an ordinal logistic regression is the “proportional odds model”.
To complete our analysis, we utilize the clmm function of the ordinal package package in R. CLMM stands for Cumulative Link Mixed Models. The coefficients for OLS are given in ordered logits, or ordered log odds ratios.
It is important to note the difference between probability and odds when utilizing these models.Probabilities are considered proportions or percentages. You can get probabilities by dividing the occurrences of an event by the total number of observations. Odds are the ratio of the probability of one event to the probability of another event, or simplifying as the ratio of the frequency of X to the frequency of Y. For probabilities, if the chances of two events are equal, the probability of either outcome is 0.5, or 50%. Probability ranges from 0 to 1 (0% to 100%). If the odds equal 1, the probabilities of the outcomes are equal. If the odds are lower than 1, the probability of the second event is greater than the first. If odds is higher than 1, the probability of the first event is greater than the second event.


Log odds are logarithmically transformed odds. Log odds are also called logits. Note that logarithmically transformed here means the natural log, not base-10 log. An odds ratio is the ratio of two odds. It tells you if the odds for a particular event is more or less likely in a particular scenario over another. A log odds ratio is the log of the odds ratio. If a log odds ratio is positive, the specified level boosts the chances of a selected outcome. If a log odds ratio is negative, the specified level decreases the chances of a selected outcome. Log odds are centered around 0 (because ln(1) = 0, so when odds are equal, ln(odds) = 0.
The format of the OLS proportional odds model is as follows. This interpretations will become important as we move into the modeling.

logit[P(Y ≤j)]=αj −βx,j=1...J−1

As discussed by R. Christensen in Cumulative Link Models for Ordinal Regression with the R Package ordinal, "We interpret this to say: The log odds of the probability of getting a rating less than or equal to J is equal to the equation αj − βx, where αj is the threshold coefficient corresponding to the particular rating, β is the variable coefficient corresponding to a change in a predictor variable, and x is the value of the predictor variable. Note, β is the value given to each coefficient corresponding to a variable, which is similar to a coefficient in a linear model. However, while we have in a linear model the coefficient for a variable in the original units of the response variable, this model gives us the change in log odds. The α value can be considered an intercept of sorts (if comparing to a linear model) - it is the intercept for getting a rating of J or below, again in log odds.
Since we have defined the relationship between probability and log odds as P(X) = exp(X)/(1 + exp(X)) (where X is the log odds ratio), we can extend our definition of the proportional odds model to be:

P(Y ≤j)= exp(αj−βx) ,j=1...J−1." 1+exp(αj −βx)

We hypothesize our null: There is no statistical difference between Knowledge Scores of students who received the intervention (3 lessons or more) and those who did not receive the intervention (2 or less) across the length of the program. Therefore, our alternative hypothesis is: There is a statistical difference between Knowledge Scores of stu- dents who received the intervention (3 lessons or more) and those who did not receive the intervention (2 or less) across the length of the program.

## Section 2: Data Analysis and Modeling Section 

2.1: Variables of Interest

As we begin our clmm modeling, we take note of specific variables of interest. In our model notation, KP_Bucket (Knowledge Percent as Ordinal Grade Not Integer) is the dependent variable. We have identified several predictor variables that we will explore as we build and test our models. These variables include:


Response Variable
KP_brac : This variable was coded by assigning a letter grade based on the percent of correct test answers achieved by the students. It is scaled A-F with A being high, and F being low. Cut off points were: A >= 90, B >= 80, C >= 70, D >= 60, F < 60.


Fixed Effects: (Random Intercepts in the model)
Program_Year: This variable describes the NIFA Program Year Student_Year: This variable describes student participation in the program. lessons_1: This variable is dummy coded with - 1= Student received intervention of: 3 nutrition lessons and - 0= Student received 2 lessons or less. pd_brac (PD Sessions Attended): This variable is coded for “Yes” if the teacher attended PD sessions and “No” if the teacher did not attend pd sessions. test_exp / (Test Exposure): This variable describes how many times the student has seen the test prior to the current attempt. Test: This variable denotes whether the test was a “Pre” test or a “Post” test. Gender_1 : This variable is a dummy coded student gender variable with 1= “Girl” and 0 = “Boy”.

Random Effects: 

(Random Effects allow for random Slopes to be added into the model to ac- count for unobserved heterogenity among groups. As example, allowing our model to account for differences in students within classrooms, or classrooms within schools)

Code: Student ID TeacherID: Teacher ID - in effect setting a classroom for random slope School School_Type: Experimental or Control School Program_Year: Which Year of the program Student_Year: Which year of student participation in the program Level: This variable differentiates between the two levels of the test


Model Assumptions (which will be checked on a couple of the models throughout the code)
The assumptions of the Ordinal Logistic Regression are as follow and should be tested in order: 1. The dependent variable are ordered. 2. One or more of the independent variables are either continuous, categorical or ordinal. 3. No multi-collinearity. 4. Proportional odds















