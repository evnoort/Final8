# Final assignment Machine Learning
The goal of this project is to predict the manner in which exercises taken from exercise data are done. 
The manner in which excercises are done is registered in the "classe" variable in the training set below. 

The following packages are required for this analysis.
library(caret)
library(dplyr)
library(randomForest)

The trainingSet is downloaded from [link](https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv)
The testSet is downloaded from [link](https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv)

Here below is the R code

## Load the downloaded test Set and training Set
trainSet <- read.csv("FinalAssignmentLesson8/trainingSet.csv", sep=",")
testSet <- read.csv("FinalAssignmentLesson8/testSet.csv", sep=",")

## Remove useless columns by using domain knowlegde
trainKeep <- subset(trainSet, select=-c(X,user_name,raw_timestamp_part_1,raw_timestamp_part_2,cvtd_timestamp,new_window,num_window))

## Keep only columns where 80% of the rows have a value
is.na(trainKeep) <- trainKeep==""
cols <- as.data.frame(colSums(is.na(trainKeep))/nrow(trainKeep))
names(cols)="NApct"
predCandidates <- subset(cols, NApct < 0.8)
trainSub <- trainKeep[,rownames(predCandidates)]

## Create a training and a test set
trainP <- createDataPartition(y=trainSub$classe, p = .6, list=FALSE)
training <- trainSub[trainP,]
testing <- trainSub[-trainP,]


## Fit a model using random forest using the training data   
set.seed(100)
modelFit <- randomForest(classe ~., data=training, type="class")

## Use a confusion matrix to determine the quality
confusionMatrix(predTrain,testing$classe)$overall['Accuracy']
## Accuracy 0.9931175 is high so the model is assumed to have a low out of sample error

# Apply prediction model to the given testset
cols_test <- rownames(cols)[1:nrow(cols)-1]
quizdata <- testSrc[,cols_test]
testPred <- predict(modelFit,testSet)

# Results
testPred
## [1] B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B



