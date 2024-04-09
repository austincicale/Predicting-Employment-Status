# Predicting Employment Status

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)
- [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)
- [Data Analysis](#data-analysis)
- [Results/Findings](#resultsfindings)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [References](#references)

### Project Overview

This research project was conducted during a Machine Learning and Econometrics course (ECON 573) at The University of North Carolina at Chapel Hill throughout the Fall semester of 2023. While the research paper was a collaborative assignment, I was responsible for all programming aspects of my team's research. This project aims to predict individual employment status using machine learning techniques to identify significant variables. Understanding these predictors can aid individuals in navigating the job market, help organizations optimize recruitment strategies, and inform policy decisions focused on reducing unemployment and promoting societal equality.

### Data Sources

This project utilizes U.S. census microdata from 2021, sourced from [IPUMS USA](https://usa.ipums.org/usa/). The data is cross-sectional, focusing on individuals residing within the United States throughout 2021. Each observation within the data set represents an individual respondent.

### Tools

#### Programming Language
- **[R](https://www.r-project.org/about.html)**:
  - Data preprocessing
  - Exploratory Data Analysis (EDA)
  - Statistical analysis
  - Data visualization
  - Machine learning model development
  - Reporting and documentation using R Markdown

#### Libraries and Packages

| Package       | Uses                                                                          |
|---------------|-------------------------------------------------------------------------------|
| [dplyr](https://cran.r-project.org/web/packages/dplyr/index.html)         | Data manipulation and analysis                                               |
| [ggplot2](https://cran.r-project.org/web/packages/ggplot2/index.html)       | Data visualization and plot creation                                         |
| [ROSE](https://cran.r-project.org/web/packages/ROSE/index.html)          | Oversampling of training data for balanced classes                             |
| [MASS](https://cran.r-project.org/web/packages/MASS/index.html)         | Linear Discriminant Analysis (LDA) and Quadratic Discriminant Analysis (QDA)   |
| [part](https://cran.r-project.org/web/packages/rpart/index.html)         | Classification trees                                                          |
| [randomForest](https://cran.r-project.org/web/packages/randomForest/index.html)  | Random forest modeling                                                        |
| [gbm](https://cran.r-project.org/web/packages/gbm/index.html)           | Boosting                                                                      |
| [e1071](https://cran.r-project.org/web/packages/e1071/index.html)         | Support Vector Machine (SVM) classification                                   |
| [caret](https://cran.r-project.org/web/packages/caret/index.html)         | Various machine learning tasks                                                |
| [class](https://cran.r-project.org/web/packages/class/index.html)         | k-Nearest Neighbors (kNN) classification                                      |

### Data Cleaning/Preparation

In the initial data preparation phase, the following tasks were executed:
1. Removed all NA values and individuals not in the labor force, reducing the data from 3,252,599 observations to 261,202 observations.
2. Removed unnecessary and redundant variables.
3. Manipulated certain variables to simplify the data, such as merging similar education levels rather than having levels for each grade.
4. Converted the data types for certain variables to better reflect the data and enhance analysis.

### Exploratory Data Analysis (EDA)

Before creating predictive models, EDA involved exploring the 14 predictors related to employment status to gain deeper insights into which variables might hold the greatest significance in predicting whether someone is employed. This analysis involved comparing the characteristics of employed and unemployed individuals for each variable, which was organized into *Table 1. Descriptive Statistics of Data*.

#### *Table 1. Descriptive Statistics of Data*
<img width="426" alt="Screenshot 2024-04-05 at 2 40 54 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/852c6a7e-303c-4a32-b037-a68ef6ab6680">
<img width="426" alt="Screenshot 2024-04-05 at 2 41 28 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/44a7041c-a8ee-4530-838e-6f0f0cd32b70">

### Data Analysis

To comprehend the basic relationship between parameters and employment status, a logistic regression model was created to predict employment status using all variables and observations within the cleaned data set. This basic model portrayed all predictors as statistically significant, except FAMSIZE, NCHILD, and VETSTAT. The statistical summary for this model is shown in *Table 2. Basic Logistic Regression Model*. 

```r
# Creating a basic logistic regression model using all cleaned data
glm.emp1 <- glm(EMPSTAT ~., family = "binomial", data = data)
summary(glm.emp1)
```
#### *Table 2. Basic Logistic Regression Model*
<img width="566" alt="Screenshot 2024-04-08 at 11 17 26 AM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/bd57dfbe-3f01-4eac-8cdc-511d997424ab">

Future models were created using the validation set approach, where 70% of the data was randomly assigned to the training set and the remaining 30% was assigned to the test or validation set. However, an issue surfaced concerning the basic logistic regression model's predictive performance. With a classification threshold of 0.5, the model predicted all observations to be "employed." This predictive behavior signifies a bias towards the majority class (employed individuals). The ROSE package in R was utilized to address the imbalance of class observations. Through sampling techniques and a smoothed bootstrap approach, the ROSE package artificially generated a new training set, mirroring its original size while mitigating the disparity in class distribution. Notably, the original training set exhibited over 93% “employed” observations, whereas the augmented training set achieved a more balanced representation, with approximately 50% “employed” observations. By training the models on data with improved class balance, we aim to enhance the discernment of variables impacting employment. Subsequently, the models constructed using the refined training set will be utilized to predict observations within the unaltered test set, allowing assessment of prediction accuracy.

```r
# Splitting data into training and test set
set.seed(1)
# Generating random indices for train-test split
indices1 <- sample(1:nrow(data), size = round(0.7 * nrow(data)), replace = FALSE)
# Creating training and testing sets
train1 <- data[indices1, ]
test1 <- data[-indices1, ]
```
```r
# Perform oversampling on the training data to have more balanced classes
library(ROSE)
train2 <- ROSE(EMPSTAT ~., data = train1, seed = 123)$data
```

In addition to logistic regression, complementary modeling methods used throughout data analysis include LDA, QDA, classification trees, random forests, boosting, SVM, and kNN. Among all modeling techniques, the most accurate models with balanced predictive power for both employed and unemployed individuals were developed using classification trees and boosting. The predictive accuracy of these models can be observed through their respective confusion matrices. Furthermore, the output of the classification tree model is depicted in the figure titled *Classification Tree Predicting Employment Status*, while the most significant variables according to the boosting model are illustrated in its relative influence plot.

```r
# Train and test model using Classification Trees
library(rpart)
set.seed(1)
tree_model <- rpart(EMPSTAT ~., data = train2, method = "class")
predictions_tree <- predict(tree_model, newdata = test1, type = "class")

# Create classification tree confusion matrix
table(predictions_tree, test1$EMPSTAT)
```
#### *Classification Tree Confusion Matrix*
<img width="545" alt="Screenshot 2024-04-08 at 12 44 35 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/ff45d322-44d6-421a-bae0-d29d5710727d">

```r
# Create visual representation of classification tree
library(rpart.plot)
printcp(tree_model)
prp(tree_model, extra = 1, branch = 1, shadow.col = "gray", box.col = "lightblue", branch.lty = 3, tweak = 1.2)
```
#### *Classification Tree Predicting Employment Status*
<img width="537" alt="Screenshot 2024-04-08 at 12 46 53 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/060f3820-13b1-4938-ab71-59f4072885ab">

```r
# Train and test model using Boosting
library(gbm)
set.seed(1)
boost_model <- gbm(EMPSTAT ~., data = train2_boost, distribution = "bernoulli", n.trees = 100, interaction.depth = 3, shrinkage = 0.1)
boost_pred <- predict(boost_model, newdata = test1_boost, type = "response", n.trees = 100)
boost_pred_class <- ifelse(boost_pred > 0.5, 1, 0)

# Create boosting confusion matrix
table(boost_pred_class, test1_boost$EMPSTAT)
```
#### *Boosting Confusion Matrix*
<img width="544" alt="Screenshot 2024-04-08 at 12 57 18 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/770eb30f-9f45-40a1-8702-098b575bbfe7">

```r
# Organize the boost_model summary into a relative influence data frame to determine variable importance
variable_names <- c("EDUCD", "RELATE", "SEX", "AGE", "SPEAKENG", "MARST", "RACE", "FAMSIZE", "HISPAN", "YRSUSA2", "CITIZEN", "NCHILD", "SCHOOL", "VETSTAT")
relative_influence <- c(26.8954659, 23.2950799, 11.5776106, 10.2749420, 5.8514439, 4.9117615, 4.1419822, 3.9308735, 3.4863497, 3.3267069, 1.0281384, 0.6561295, 0.6235162, 0.0000000)
variable_importance <- data.frame(Variable <- factor(variable_names, levels = variable_names), Importance <- relative_influence)
variable_importance <- variable_importance[order(-variable_importance$Importance), ]

# Create a relative influence plot using variable_importance data frame
ggplot(variable_importance, aes(x = Variable, y = Importance)) +
  geom_bar(stat = "identity", fill = "skyblue") +
  labs(x = "Variable", y = "Relative Influence") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1)) +  
  geom_text(aes(label = round(Importance, 2)), vjust = -0.5)
```
#### *Boosting Relative Influence Plot*
<img width="606" alt="Screenshot 2024-04-08 at 1 09 48 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/a76521c2-21da-4c92-8319-2732d9dfe775">

### Results/Findings

While each modeling technique produced slightly different outcomes, some common occurrences were noticeable. The analysis results are summarized as follows:
1. Veteran status (VETSTAT) exhibited insignificance across all models.
2. The most influential predictors were AGE, educational attainment (EDUCD), relation to household head (RELATE), and SEX. This significance is evident in both the classification tree and boosting models. These two models, known for their balanced predictive capabilities, selected these variables as the most important.
3. RACE consistently demonstrated significance, particularly highlighted by the random forest model, where it ranked third in importance, following AGE and EDUCD.

### Recommendations

Based on the analysis, the following actions are recommended:
  - Advocate for anti-discrimination legislation targeting age, gender, and race biases in employment.
  - Promote policies that incentivize educational attainment for disadvantaged groups.
  - Develop employment programs for demographic groups with lower employment rates.
  - Increase access to career consulting services.
  - Analyze the root causes of unemployment and examine the success of current and past initiatives.
  - Study longitudinal employment trends to understand how predictors of employment status have evolved over time.

### Limitations

##### 1. Data Collected from Single Year (2021)
  - The study utilized data from the year 2021 due to its availability, which may limit the ability to capture longitudinal trends or changes over time in predictors of employment status.

##### 2. High Proportion of Employed Respondents (94%):
  - The disproportionate representation of employed individuals in the dataset may limit the ability to explore the underlying relationships that differentiate employed and unemployed individuals.

##### 3. Imbalanced Class Representation in Modeling:
  - Classification modeling tends to favor individuals from the majority class, potentially leading to biased predictions. To address this imbalance, the training set was artificially resampled, which could introduce limitations or biases in the analysis.

##### 4. Subjective Variable Selection:
  - Specific variables were selected based on personal beliefs about their potential significance as predictors of employment status. This subjective approach may overlook other potentially relevant variables, limiting the comprehensiveness of the analysis.

    
### References

Textbook: [An Introduction to Statistical Learning](https://www.statlearning.com/) 

