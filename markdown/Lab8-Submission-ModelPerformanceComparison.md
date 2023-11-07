Business Intelligence Lab Submission Markdown
================
<team kubwa>
\<23/10/2023\>

- [Student Details](#student-details)
- [Setup Chunk](#setup-chunk)
- [Loading the Loan Status Train Imputed
  Dataset](#loading-the-loan-status-train-imputed-dataset)
  - [Description of the Dataset](#description-of-the-dataset)
- [\<The Resamples Function\>](#the-resamples-function)
  - [\<Call the `resamples` Function\>](#call-the-resamples-function)
  - [\<Display the Results\>](#display-the-results)

# Student Details

|                                                   |                                                                                                                                                                                                                                                                                                                                                                |     |
|---------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
| **Student ID Numbers and Names of Group Members** | *\<list one Student name, class group (just the letter; A, B, or C), and ID per line, e.g., 123456 - A - John Leposo; you should be between 2 and 5 members per group\>* \| \| 1. 128998 - B - Crispus Nzano \| \| 2. 134100 - B - Timothy Obosi \| \| 3. 134092 - B - Alison Kuria \| 4. 135269 - B - Clifford Kipchumba \| \| 5. 112826 - B - Matu Ngatia \| |     |
| **GitHub Classroom Group Name**                   | Team Kubwa \|                                                                                                                                                                                                                                                                                                                                                  |     |
| **Course Code**                                   | BBT4206                                                                                                                                                                                                                                                                                                                                                        |     |
| **Course Name**                                   | Business Intelligence II                                                                                                                                                                                                                                                                                                                                       |     |
| **Program**                                       | Bachelor of Business Information Technology                                                                                                                                                                                                                                                                                                                    |     |
| **Semester Duration**                             | 21<sup>st</sup> August 2023 to 28<sup>th</sup> November 2023                                                                                                                                                                                                                                                                                                   |     |

# Setup Chunk

We start by installing all the required packages

``` r
## formatR - Required to format R code in the markdown ----

if (require("languageserver")) {
  require("languageserver")
} else {
  install.packages("languageserver", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

# Introduction ----
# Resampling methods are techniques that can be used to improve the performance
# and reliability of machine learning algorithms. They work by creating
# multiple training sets from the original training set. The model is then
# trained on each training set, and the results are averaged. This helps to
# reduce overfitting and improve the model's generalization performance.

# Resampling methods include:
## Splitting the dataset into train and test sets ----
## Bootstrapping (sampling with replacement) ----
## Basic k-fold cross validation ----
## Repeated cross validation ----
## Leave One Out Cross-Validation (LOOCV) ----

# STEP 1. Install and Load the Required Packages ----
## mlbench ----
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## caret ----
if (require("caret")) {
  require("caret")
} else {
  install.packages("caret", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## kernlab ----
if (require("kernlab")) {
  require("kernlab")
} else {
  install.packages("kernlab", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

## randomForest ----
if (require("randomForest")) {
  require("randomForest")
} else {
  install.packages("randomForest", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

------------------------------------------------------------------------

**Note:** the following “*KnitR*” options have been set as the defaults
in this markdown:  
`knitr::opts_chunk$set(echo = TRUE, warning = FALSE, eval = TRUE, collapse = FALSE, tidy.opts = list(width.cutoff = 80), tidy = TRUE)`.

More KnitR options are documented here
<https://bookdown.org/yihui/rmarkdown-cookbook/chunk-options.html> and
here <https://yihui.org/knitr/options/>.

``` r
knitr::opts_chunk$set(
    eval = TRUE,
    echo = TRUE,
    warning = FALSE,
    collapse = FALSE,
    tidy = TRUE
)
```

------------------------------------------------------------------------

**Note:** the following “*R Markdown*” options have been set as the
defaults in this markdown:

> output:  
>   
> github_document:  
> toc: yes  
> toc_depth: 4  
> fig_width: 6  
> fig_height: 4  
> df_print: default  
>   
> editor_options:  
> chunk_output_type: console

# Loading the Loan Status Train Imputed Dataset

The Datasets are then loaded.

``` r
library(readr)
train_imputed <- read_csv("C:/Users/Cris/github-classroom/BBT4206-R-Lab7of15-AlgorithmSelection-team-kubwa/data/train_imputed.csv")
```

    ## Rows: 614 Columns: 12
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (7): Gender, Married, Dependents, Education, SelfEmployed, PropertyArea,...
    ## dbl (5): ApplicantIncome, CoapplicantIncome, LoanAmount, LoanAmountTerm, Cre...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
View(train_imputed)
```

## Description of the Dataset

We then display the number of observations and number of variables. 12
Variables and 614 observations.

``` r
dim(train_imputed)
```

    ## [1] 614  12

Next, we display the quartiles for each numeric
variable<span id="highlight" style="color: blue">*… this is the process
of **“storytelling using the data.”** The goal is to analyse the Loan
and Cubic dataset and try to train a model to make predictions( which
model is most suited for this dataset).*</span>

``` r
summary(train_imputed)
```

    ##     Gender            Married           Dependents         Education        
    ##  Length:614         Length:614         Length:614         Length:614        
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##  SelfEmployed       ApplicantIncome CoapplicantIncome   LoanAmount   
    ##  Length:614         Min.   :  150   Min.   :    0     Min.   :  150  
    ##  Class :character   1st Qu.: 2878   1st Qu.:    0     1st Qu.: 2875  
    ##  Mode  :character   Median : 3812   Median : 1188     Median : 3768  
    ##                     Mean   : 5403   Mean   : 1621     Mean   : 5371  
    ##                     3rd Qu.: 5795   3rd Qu.: 2297     3rd Qu.: 5746  
    ##                     Max.   :81000   Max.   :41667     Max.   :81000  
    ##  LoanAmountTerm  CreditHistory   PropertyArea          Status         
    ##  Min.   : 12.0   Min.   :0.000   Length:614         Length:614        
    ##  1st Qu.:360.0   1st Qu.:1.000   Class :character   Class :character  
    ##  Median :360.0   Median :1.000   Mode  :character   Mode  :character  
    ##  Mean   :342.3   Mean   :0.855                                        
    ##  3rd Qu.:360.0   3rd Qu.:1.000                                        
    ##  Max.   :480.0   Max.   :1.000

# \<The Resamples Function\>

The “resamples()” function checks that the models are comparable and
that they used the same training scheme (“train_control” configuration).
To do this, after the models are trained, they are added to a list and
we pass this list of models as an argument to the resamples() function
in R

``` r
## 3.a. Train the Models ---- We train the following models, all of which are
## using 10-fold repeated cross validation with 3 repeats: LDA CART KNN SVM
## Random Fores

train_control <- trainControl(method = "repeatedcv", number = 10, repeats = 3)

### LDA ----
set.seed(7)
train_imputed_model_lda <- train(Status ~ ., data = train_imputed, method = "lda",
    trControl = train_control)

### CART ----
set.seed(7)
train_imputed_model_cart <- train(Status ~ ., data = train_imputed, method = "rpart",
    trControl = train_control)

### KNN ----
set.seed(7)
train_imputed_model_knn <- train(Status ~ ., data = train_imputed, method = "knn",
    trControl = train_control)

### SVM ----
set.seed(7)
train_imputed_model_svm <- train(Status ~ ., data = train_imputed, method = "svmRadial",
    trControl = train_control)

### Random Forest ----
set.seed(7)
train_imputed_model_rf <- train(Status ~ ., data = train_imputed, method = "rf",
    trControl = train_control)
```

## \<Call the `resamples` Function\>

We then create a list of the model results and pass the list as an
argument to the `resamples` function.

``` r
results <- resamples(list(LDA = train_imputed_model_lda, CART = train_imputed_model_cart,
    KNN = train_imputed_model_knn, SVM = train_imputed_model_svm, RF = train_imputed_model_rf))
```

## \<Display the Results\>

This is the simplest comparison. It creates a table with one model per
row and its corresponding evaluation metrics displayed per column.

``` r
summary(results)
```

    ## 
    ## Call:
    ## summary.resamples(object = results)
    ## 
    ## Models: LDA, CART, KNN, SVM, RF 
    ## Number of resamples: 30 
    ## 
    ## Accuracy 
    ##           Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
    ## LDA  0.7377049 0.7960578 0.8130619 0.8137810 0.8360656 0.8688525    0
    ## CART 0.7049180 0.7714172 0.8064516 0.7947343 0.8196721 0.8688525    0
    ## KNN  0.5573770 0.6169355 0.6557377 0.6530434 0.6774194 0.7213115    0
    ## SVM  0.7377049 0.7911546 0.8064516 0.8094182 0.8326943 0.8524590    0
    ## RF   0.7377049 0.7877446 0.8064516 0.8050728 0.8225806 0.8524590    0
    ## 
    ## Kappa 
    ##            Min.     1st Qu.     Median      Mean    3rd Qu.      Max. NA's
    ## LDA   0.2339089  0.44725111 0.49539163 0.4930891 0.56480068 0.6543909    0
    ## CART  0.2855051  0.38377792 0.46883104 0.4562377 0.51904090 0.6543909    0
    ## KNN  -0.2263589 -0.04734239 0.01493286 0.0146983 0.09109683 0.2004626    0
    ## SVM   0.2339089  0.44265198 0.49041096 0.4767094 0.54651307 0.6174216    0
    ## RF    0.2339089  0.43317789 0.47285762 0.4699364 0.52405786 0.6047516    0

``` r
## 2. Box and Whisker Plot ---- This is useful for visually observing the
## spread of the estimated accuracies for different algorithms and how they
## relate.

scales <- list(x = list(relation = "free"), y = list(relation = "free"))
bwplot(results, scales = scales)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%20Eighth%20Code%20Chunk-1.png)<!-- -->

``` r
## 3. Dot Plots ---- They show both the mean estimated accuracy as well as the
## 95% confidence interval (e.g. the range in which 95% of observed scores
## fell).

scales <- list(x = list(relation = "free"), y = list(relation = "free"))
dotplot(results, scales = scales)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%20Eighth%20Code%20Chunk-2.png)<!-- -->

``` r
## 4. Scatter Plot Matrix ---- This is useful when considering whether the
## predictions from two different algorithms are correlated. If weakly
## correlated, then they are good candidates for being combined in an ensemble
## prediction.

splom(results)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%20Eighth%20Code%20Chunk-3.png)<!-- -->

``` r
## 5. Pairwise xyPlots ---- You can zoom in on one pairwise comparison of the
## accuracy of trial-folds for two models using an xyplot.

# xyplot plots to compare models
xyplot(results, models = c("LDA", "SVM"))
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%20Eighth%20Code%20Chunk-4.png)<!-- -->

``` r
# or xyplot plots to compare models
xyplot(results, models = c("SVM", "CART"))
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%20Eighth%20Code%20Chunk-5.png)<!-- -->

``` r
## 6. Statistical Significance Tests ---- This is used to calculate the
## significance of the differences between the metric distributions of the
## various models.

### Upper Diagonal ---- The upper diagonal of the table shows the estimated
### difference between the distributions. If we think that LDA is the most
### accurate model from looking at the previous graphs, we can get an estimate
### of how much better it is than specific other models in terms of absolute
### accuracy.

### Lower Diagonal ---- The lower diagonal contains p-values of the null
### hypothesis.  The null hypothesis is a claim that 'the distributions are the
### same'.  A lower p-value is better (more significant).

diffs <- diff(results)

summary(diffs)
```

    ## 
    ## Call:
    ## summary.diff.resamples(object = diffs)
    ## 
    ## p-value adjustment: bonferroni 
    ## Upper diagonal: estimates of the difference
    ## Lower diagonal: p-value for H0: difference = 0
    ## 
    ## Accuracy 
    ##      LDA       CART      KNN       SVM       RF       
    ## LDA             0.019047  0.160738  0.004363  0.008708
    ## CART 0.003626             0.141691 -0.014684 -0.010338
    ## KNN  < 2.2e-16 2.636e-13           -0.156375 -0.152029
    ## SVM  0.300813  0.047484  < 2.2e-16            0.004345
    ## RF   0.013136  0.768128  < 2.2e-16 1.000000           
    ## 
    ## Kappa 
    ##      LDA       CART      KNN       SVM       RF       
    ## LDA             0.036851  0.478391  0.016380  0.023153
    ## CART 0.005434             0.441539 -0.020472 -0.013699
    ## KNN  < 2.2e-16 1.044e-15           -0.462011 -0.455238
    ## SVM  0.088726  0.454348  < 2.2e-16            0.006773
    ## RF   0.006104  1.000000  < 2.2e-16 1.000000

**etc.** as per the lab submission requirements. Be neat and communicate
in a clear and logical manner.
