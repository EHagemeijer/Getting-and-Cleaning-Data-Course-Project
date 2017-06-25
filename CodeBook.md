# Getting & cleaning data JHC
# EFGH
# 25-6-2017


# This script will perform the following steps on the UCI HAR Dataset downloaded from 

# https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

# 1 Merges the training and the test sets to create one data set.
# 2 Extracts only the measurements on the mean and standard deviation for each measurement.
# 3 Uses descriptive activity names to name the activities in the data set
# 4 Appropriately labels the data set with descriptive variable names.
# 5 From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


# Clean up workspace

rm(list=ls())


# 1. Merge the training and the test sets to create one data set.

# set working directory to the location dataset 

setwd("~/Data Science/Coursera/tidyfile")

# Read in the data from files
# Read train data

X_train <- read.table("./UCI HAR Dataset/train/X_train.txt")
Y_train <- read.table("./UCI HAR Dataset/train/Y_train.txt")
Sub_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")


# read test data

X_test <- read.table("./UCI HAR Dataset/test/X_test.txt")
Y_test <- read.table("./UCI HAR Dataset/test/Y_test.txt")
Sub_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")


# read data description

variable_names <- read.table("./UCI HAR Dataset/features.txt")

# read activity labels

activity_labels <- read.table("./UCI HAR Dataset/activity_labels.txt")

# 1. Merges the training and the test sets to create one data set.

X_total <- rbind(X_train, X_test)
Y_total <- rbind(Y_train, Y_test)
Sub_total <- rbind(Sub_train, Sub_test)

# 2. Extract only the measurements on the mean and standard deviation for each measurement. 

selected_var <- variable_names[grep("mean\\(\\)|std\\(\\)",variable_names[,2]),]
X_total <- X_total[,selected_var[,1]]

# 3. Use descriptive activity names to name the activities in the data set
# Merge the finalData set with the acitivityType table to include descriptive activity names

colnames(Y_total) <- "activity"
Y_total$activitylabel <- factor(Y_total$activity, labels = as.character(activity_labels[,2]))

activitylabel <- Y_total[,-1]

# 4. Appropriately labels the data set with descriptive variable names.

colnames(X_total) <- variable_names[selected_var[,1],2]


# 5. From the data set in step 4, creates a second, independent tidy data set with the average
# of each variable for each activity and each subject.

colnames(Sub_total) <- "subject"

total <- cbind(X_total, activitylabel, Sub_total)
total_mean <- total %>% group_by(activitylabel, subject) %>% summarise_all(funs(mean))

write.table(total_mean, file = "./tidydata.txt", row.names = FALSE, col.names = TRUE)


# THIS IS END
