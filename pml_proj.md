---
title: "Prediction of exercise type from physical measurements"
author: "Parikshit Sanyal"
date: "31 August 2018"
output: html_document
---



## Loading the data

We load the training dataset in the variable 'tr'. This is the dataset we are later going to split into 'training' and 'testing'. The validation data 'val' (from pml-testing.csv) is going to be untouched till the last phase.


```r
tr <- read.csv('pml_proj/pml-training.csv')
```

```
## Warning in file(file, "rt"): cannot open file 'pml_proj/pml-training.csv':
## No such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

```r
val <- read.csv('pml_proj/pml-testing.csv')
```

```
## Warning in file(file, "rt"): cannot open file 'pml_proj/pml-testing.csv':
## No such file or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

We take a look at how big is the 'tr' dataset.


```r
dim(tr)
```

```
## Error in eval(expr, envir, enclos): object 'tr' not found
```

... which shows it is compiled of 19622 records of 160 variables. We take a look at the 'classe' variable which we have to predict


```r
summary(tr$classe)
```

```
## Error in summary(tr$classe): object 'tr' not found
```

It is a factor of 5 levels A, B, C, D and E. We now try to visualise relations between the variables. We check for NA values in the set


```r
sum(is.na(tr))
```

```
## Error in eval(expr, envir, enclos): object 'tr' not found
```

We need to impute these NA values by K nearest eighbours algorithm


```r
preProcValues <- preProcess(tr, method = c("knnImpute","center","scale"))
```

```
## Error in preProcess(tr, method = c("knnImpute", "center", "scale")): object 'tr' not found
```

```r
tr_processed <- predict(preProcValues, tr)
```

```
## Error in predict(preProcValues, tr): object 'preProcValues' not found
```

```r
sum(is.na(tr_processed))
```

```
## Error in eval(expr, envir, enclos): object 'tr_processed' not found
```

We use readings from accelometers in the belt , forearm, arm and dumbbell; we segregate these variables


```r
names(tr_processed)[grep("accel",names(tr_processed))]
```

```
## Error in eval(expr, envir, enclos): object 'tr_processed' not found
```

```r
ta <- names(tr_processed)[grep("^total_accel",names(tr_processed))]
```

```
## Error in eval(expr, envir, enclos): object 'tr_processed' not found
```
We now plot total accels against each other


```r
featurePlot(x = tr_processed[,ta],y=tr$classe,plot='pairs')
```

```
## Error in is.data.frame(x): object 'tr_processed' not found
```
Since the total_accels in each recorder do not show any correlation with each other, we can assume them to be independent variables, and use them as predictors.


```r
set.seed(107)
inTrain <- createDataPartition(
  y = tr_processed$classe,
  p = .75,
  list = FALSE
)
```

```
## Error in createDataPartition(y = tr_processed$classe, p = 0.75, list = FALSE): object 'tr_processed' not found
```

```r
training <- tr_processed[ inTrain,]
```

```
## Error in eval(expr, envir, enclos): object 'tr_processed' not found
```

```r
testing  <- tr_processed[-inTrain,]
```

```
## Error in eval(expr, envir, enclos): object 'tr_processed' not found
```

```r
nrow(training)
```

```
## Error in nrow(training): object 'training' not found
```

```r
nrow(testing)
```

```
## Error in nrow(testing): object 'testing' not found
```

We now train a KNN based model from the dataset and predict values from testing set


```r
val_acc <- val[,ta]
```

```
## Error in eval(expr, envir, enclos): object 'val' not found
```

```r
model_knn <- knn3(classe ~ .,data=training,k=3)
```

```
## Error in terms.formula(formula[-2], data = data): object 'training' not found
```

```r
pt_k <- predict(model_knn, testing, ype='class')
```

```
## Error in predict(model_knn, testing, ype = "class"): object 'model_knn' not found
```

```r
summary(testing$classe == pt_k)
```

```
## Error in summary(testing$classe == pt_k): object 'testing' not found
```

We then apply the model to the valuation set

```r
pv_k <- predict(model_knn,val_accmtype='class')
```

```
## Error in predict(model_knn, val_accmtype = "class"): object 'model_knn' not found
```

```r
pv_k
```

```
## Error in eval(expr, envir, enclos): object 'pv_k' not found
```

Which outputs E B E B E B B B E E E B E E B E E E B B for the 20 entries in val dataset.
