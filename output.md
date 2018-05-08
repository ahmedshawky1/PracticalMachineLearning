---
title: "Practical Machine Learning"
author: "AhmedShawky"
date: "May 8, 2018"
output: html_document
---

#Overview

The goal of your project is to predict the manner in which they did the exercise. This is the "classe" variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.


###Reading & Cleaning the Data

```r
set.seed(1234)
library(caret)
library(rpart)
library(rattle)
library(rpart.plot)

trainingData <- read.csv('pml-training.csv')
testingData_Quiz <- read.csv('pml-testing.csv')
dim(trainingData)
```

```
## [1] 19622   160
```

```r
dim(testingData_Quiz)
```

```
## [1]  20 160
```

```r
#Remove the X col as it was only a sequence 
trainingData<-trainingData[,-1]
testingData_Quiz<-testingData_Quiz[,-1]

NZV <- nearZeroVar(trainingData, saveMetrics = TRUE)
#Col which will be removed from Near Zero Value 
head(NZV[NZV$nzv==T,])
```

```
##                       freqRatio percentUnique zeroVar  nzv
## new_window             47.33005    0.01019264   FALSE TRUE
## kurtosis_roll_belt   1921.60000    2.02323922   FALSE TRUE
## kurtosis_picth_belt   600.50000    1.61553358   FALSE TRUE
## kurtosis_yaw_belt      47.33005    0.01019264   FALSE TRUE
## skewness_roll_belt   2135.11111    2.01304658   FALSE TRUE
## skewness_roll_belt.1  600.50000    1.72255631   FALSE TRUE
```

```r
trainingData <- trainingData[, !NZV$nzv]
testingData_Quiz <- testingData_Quiz[, !NZV$nzv]

#Remove NA 
colwithNA <-colSums( is.na( trainingData ) ) > 0
trainingData <-trainingData[,!colwithNA]
testingData_Quiz <-testingData_Quiz[,!colwithNA]
dim(trainingData)
```

```
## [1] 19622    58
```

###Get Train/test Data 

```r
Train <- createDataPartition(y=trainingData$classe, p=0.75, list=FALSE)
training <- trainingData[Train,] 
testing <- trainingData[-Train, ]
```

###using Decision Tree as the algorithms 

```r
mod_DT <- rpart(classe ~ ., data=training, method="class")
fancyRpartPlot(mod_DT)
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png)

```r
predictions <- predict(mod_DT, testing, type = "class")
```



###test the Results


```r
confusionMatrix(predictions, testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1333   44    4    0    0
##          B   36  786   44   35    0
##          C   26  112  791  127   36
##          D    0    7   16  612   98
##          E    0    0    0   30  767
## 
## Overall Statistics
##                                          
##                Accuracy : 0.8746         
##                  95% CI : (0.865, 0.8837)
##     No Information Rate : 0.2845         
##     P-Value [Acc > NIR] : < 2.2e-16      
##                                          
##                   Kappa : 0.8415         
##  Mcnemar's Test P-Value : NA             
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9556   0.8282   0.9251   0.7612   0.8513
## Specificity            0.9863   0.9709   0.9257   0.9705   0.9925
## Pos Pred Value         0.9652   0.8724   0.7244   0.8349   0.9624
## Neg Pred Value         0.9824   0.9593   0.9832   0.9540   0.9674
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2718   0.1603   0.1613   0.1248   0.1564
## Detection Prevalence   0.2816   0.1837   0.2227   0.1495   0.1625
## Balanced Accuracy      0.9709   0.8996   0.9254   0.8658   0.9219
```

##$ to answer the Quiz

```r
Prediction_Quiz <- predict(mod_DT, testingData_Quiz, type = "class")


Prediction_Quiz
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  C  A  A  E  D  C  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```
