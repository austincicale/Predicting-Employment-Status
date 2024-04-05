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

This project utilizes U.S. census microdata from 2021, sourced from [IPUMS USA](https://usa.ipums.org/usa/). The data is cross-sectional, focusing on individuals residing within the United States throughout the calendar year of 2021. Each observation within the data set represents an individual respondent.

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

In the initial data preparation phase, we performed the following tasks:
1. Removed all NA values and individuals not in the labor force, reducing the data from 3,252,599 observations to 261,202 observations.
2. Removed unnecessary and redundant variables.
3. Manipulated certain variables to simplify the data, such as merging similar education levels rather than having levels for each grade.
4. Converted the data types for certain variables to better represent the data and enhance analysis.


Our data composed of 15 variables, including our employment status binary variable in addition to predictors such as RACE, SEX, and EDUCATION. The majority of our variables were categorical, we only had 3 numeric variables, which were number of children in household, # of family members in household, and AGE.

### Exploratory Data Analysis (EDA)

- Which variables play the most significant role in predicting employment status?
- 
<img width="426" alt="Screenshot 2024-04-05 at 2 40 54 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/852c6a7e-303c-4a32-b037-a68ef6ab6680">
<img width="426" alt="Screenshot 2024-04-05 at 2 41 28 PM" src="https://github.com/austincicale/Predicting-Employment-Status/assets/77798880/44a7041c-a8ee-4530-838e-6f0f0cd32b70">


### Data Analysis

To comprehend a basic relationship between our predictor variables and employment status, a logistic regression model was created using all variables and observations. This basic model portrayed most of our predictors as statistically significant, with exception of the variables representing # of family members in household, # of children in household, and veteran status. The model was used in attempt to predict all observations in the data set, and with a classification threshold of 0.5, the model predicted all observations to be employed. This portrays the imbalance within our data. One option to increase the number of unemployed predictions could be to decrease the classification threshold. But we decided to resample the training set of our data using the ROSE package to obtain more balanced observations.
In addition to logistic regression, other modeling techniques that we used to predict employment status include linear and quadratic discriminant analysis, classification trees, random forests, boosting, support vector machines, and k-nearest neighbor.

### Results/Findings

All of our models were created using the validation set approach. 70% of our data was randomly assigned to the training set and the remaining observations were assigned to the validation set. The ROSE package was then used on our training set to balance employment status representation. While each modeling technique produced slightly different outcomes, there were some common occurrences that were noticeable. Specifically, the veteran status appeared to have no significance according to every modeling method. Our classification tree model selected AGE, educational attainment, relation to household head, and sex as the variables for its tree construction. The importance of these variables was mirrored in our boosting model as it assigned the four highest variable importance values to these four variables. The RACE variable also consistently appeared to be of importance, especially according to our random forest model.

### Recommendations

Content

### Limitations

The main limitation with our data is that 94% of the the respondents were employed, which makes sense, but it can make it challenging to explore the underlying relationships that differentiate employed and unemployed individuals. Classification modeling tends to favor individuals represented by the majority. To account for the imbalance in our data, we used the ROSE package in R to artificially balance our data through a bootstrap approach.

### References

Content

