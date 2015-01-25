Coursera Readme File
========================================================
## Load dplyr package
library(dplyr)


## Load the feature and activity information
Features = read.table("features.txt")
Activities = read.table("activity_labels.txt")
## Transform the table
FeaturesT <- t(Features)
## Select only the "header" row
FeaturesT1 <- FeaturesT[2,]

## Load the test tables
Test = read.table("X_test.txt")
TestLables = read.table("y_test.txt")
TestSubject = read.table("subject_test.txt")

## Assign column names based on features
colnames(Test) <- FeaturesT1

## Remove duplicate column names
TestShort1 <- Test[, !duplicated(colnames(Test))]

## Select only mean and std variables
TestShorta <- select(TestShort1, contains("mean()"))
TestShortb <- select(TestShort1, contains("std()"))
TestShort2 <- cbind(TestShorta, TestShortb)

## Add row with activity information
Test1 <- cbind(TestLables, TestShort2)

## Add row with subject number information
Test2 <- cbind(TestSubject, Test1)

## Rename 2 columns
colnames(Test2)[1] <- "subject"
colnames(Test2)[2] <- "activityNUM"

## Add activity names
TestMerge = merge(Test2,Activities,by.x="activityNUM",by.y="V1")


## Load the training tables
Train = read.table("X_train.txt")
TrainLables = read.table("y_train.txt")
TrainSubject = read.table("subject_train.txt")

## Assign column names based on features
colnames(Train) <- FeaturesT1

## Remove duplicate column names
TrainShort1 <- Train[, !duplicated(colnames(Train))]

## Select only mean and std variables
TrainShorta <- select(TrainShort1, contains("mean()"))
TrainShortb <- select(TrainShort1, contains("std()"))
TrainShort2 <- cbind(TrainShorta, TrainShortb)

## Add row with activity information
Train1 <- cbind(TrainLables, TrainShort2)

## Add row with subject number information
Train2 <- cbind(TrainSubject, Train1)

## Rename 2 columns
colnames(Train2)[1] <- "subject"
colnames(Train2)[2] <- "activityNUM"

## Add activity names
TrainMerge = merge(Train2,Activities,by.x="activityNUM",by.y="V1")


## Merge all data
TotalData <- rbind(TestMerge, TrainMerge)

## Rename last column
colnames(TotalData)[69] <- "activity"

## Load reshape2 package
library(reshape2)

## Prepare the summary data
DataMelt <- melt(TotalData,id=c("activityNUM", "subject", "activity"))
SummaryDataMelt <- dcast(DataMelt, activity + subject ~ variable,mean)

## Create the summary file
write.table(SummaryDataMelt, file = "dataset.txt", row.names=FALSE)

