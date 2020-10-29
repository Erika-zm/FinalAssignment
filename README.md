# FinalAssignment

FinalAssignmentGetting-CleaningData
This repository is the final assignment or project of Getting and Cleaning Data course.

It includes the following files:

* A R script called run_analysis.R. It can be run as long as the Samsung data is in your working directory.
* A README.md describing how the script work
* A Code Book which describes the variables and finally
* A final data called tidydata.txt.

The run_analysis.R script consist of the following steps:

#Download the data for the project using download.file
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip", destfile = "/home/erika07/Desktop/Dataset.zip", method = "curl")

#Unzip the file 
unzip(zipfile = "/home/erika07/Desktop/Dataset.zip", exdir = "/home/erika07/Desktop")

#Read & convert features

features = read.table('./features.txt', header = FALSE, sep = ' ') 
features = as.character(features[,2])

activities = read.table("./activity_labels.txt", col.names = c("code", "activity"))

#Read & convert data from test folder 
data.test.subject = read.table('./test/subject_test.txt', header = FALSE, sep = ' ') 
data.test.x = read.table('./test/X_test.txt') 
data.test.activity = read.table('./test/y_test.txt', header = FALSE, sep = ' ')

#Make a data frame from test folder 
data.test = data.frame(data.test.subject, data.test.activity, data.test.x) 
names(data.test) = c(c('subject', 'activity'), features)

#Read data from train folder 
data.train.subject = read.table('./train/subject_train.txt', header = FALSE, sep = ' ') 
data.train.x = read.table('./train/X_train.txt') 
data.train.activity = read.table('./train/y_train.txt', header = FALSE, sep = ' ')

#Make a data frame from train folder 
data.train = data.frame(data.train.subject, data.train.activity, data.train.x) 
names(data.train) = c(c('subject', 'activity'), features)


1. Merges the training and the test sets to create one data set
test.train = rbind(data.test, data.train)

2. Extracts only the measurements on the mean and standard deviation for each measurement
library(dplyr) 
Mean.Std <- test.train %>% select(c(,3:50), contains("mean"), contains("std")) 
Mean.Std

3. Uses descriptive activity names to name the activities in the data set
test.train$activity = activities[test.train$activity, 2]

4. Appropriately labels the data set with descriptive variable names

names(test.train)[2] = "activity" 
names(test.train) = gsub("Acc", "Accelerometer", names(test.train)) 
names(test.train) = gsub("Gyro", "Gyroscope", names(test.train)) 
names(test.train) = gsub("BodyBody", "Body", names(test.train)) 
names(test.train) = gsub("Mag", "Magnitude", names(test.train)) 
names(test.train) = gsub("^t", "Time", names(test.train)) 
names(test.train) = gsub("^f", "Frequency", names(test.train)) 
names(test.train) = gsub("tBody", "TimeBody", names(test.train)) 
names(test.train) = gsub("-mean()", "Mean", names(test.train), ignore.case = TRUE) 
names(test.train) = gsub("-std()", "STD", names(test.train), ignore.case = TRUE) 
names(test.train) = gsub("-freq()", "Frequency", names(test.train), ignore.case = TRUE) 
names(test.train) = gsub("angle", "Angle", names(test.train)) 
names(test.train) = gsub("gravity", "Gravity", names(test.train))

5. Independent final tidy data set with the average of each variable for each activity and each subject
test.train2 = aggregate(. ~subject + activity, test.train, mean) write.table(test.train2, file = "tidydata.txt", row.name=FALSE)
