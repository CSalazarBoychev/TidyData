# TidyData README
Getting and Cleaning Data: Course Project

1. Codebook.md describes the variables.

2. run_analysis.R should work like a charm, albeit somewhat slow (seconds to not more than a minute on a modern processor), since it is downloading, zunzipping and then reading and writing a several files in sequence.
##IMPORTANT: Keep at least a good 210Mb of RAM available to run the code to prevent R from crashing.##

The original study files are in the "UCI HAR Dataset folder" will be downloaded from the original website, not from this repository.

Below is the script included in the .R file with commentary on how it works:

### ---
### This code was developed for the Course Project of "Getting and Cleaning Data" of the John's Hopkins Univiersity's Coursera Data Science Specialization. It is intended for a peer graded assignment.
### Author of code: Carlos Salazar Boychev.
### Credits of original research and source files: Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. A Public Domain Dataset for Human Activity Recognition Using Smartphones. 21th European Symposium on Artificial Neural Networks, Computational Intelligence and Machine Learning, ESANN 2013. Bruges, Belgium 24-26 April 2013. 
### "R version 4.0.1 (2020-06-06)"
### Libraries required: data.table.
### Data source URL: https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
### ---

# Download required library for table functions to work
suppressPackageStartupMessages({
        library(data.table)
})

# Download and extract Zip archive in work folder:
dataURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(dataURL, destfile = "data.zip")
unzip("data.zip")

# Reads data in separated files (Training and Testing) and converts them to a single data frame:
        #Inspection shows that all data is raw, without headers and with space as separator.
features <- read.csv('./UCI HAR Dataset/features.txt', header = FALSE, sep = ' ')
features <- as.character(features[,2])

data.train.x <- read.table('./UCI HAR Dataset/train/X_train.txt')
data.train.activity <- read.csv('./UCI HAR Dataset/train/y_train.txt', header = FALSE, sep = ' ')
data.train.subject <- read.csv('./UCI HAR Dataset/train/subject_train.txt',header = FALSE, sep = ' ')

data.train <-  data.frame(data.train.subject, data.train.activity, data.train.x)
names(data.train) <- c(c('subject', 'activity'), features)

data.test.x <- read.table('./UCI HAR Dataset/test/X_test.txt')
data.test.activity <- read.csv('./UCI HAR Dataset/test/y_test.txt', header = FALSE, sep = ' ')
data.test.subject <- read.csv('./UCI HAR Dataset/test/subject_test.txt', header = FALSE, sep = ' ')

data.test <-  data.frame(data.test.subject, data.test.activity, data.test.x)
names(data.test) <- c(c('subject', 'activity'), features)

# 1. Merges the training and the test sets to create one data set.
data.merge <- rbind(data.train, data.test)

# 2. Extracts only the measurements on the mean and standard deviation for each measurement.
mean_std.extract <- grep('mean|std', features)
data.extract <- data.merge[,c(1,2,mean_std.extract + 2)]

# 3. Uses descriptive activity names to name the activities in the data set.
activity <- read.table('./UCI HAR Dataset/activity_labels.txt', header = FALSE)
activity <- as.character(activity[,2])
data.extract$activity <- activity[data.extract$activity]

# 4. Appropriately labels the data set with descriptive variable names.
name.new <- names(data.extract)
name.new <- gsub("[(][)]", "", name.new)
name.new <- gsub("^t", "TimeDomain_", name.new)
name.new <- gsub("^f", "FrequencyDomain_", name.new)
name.new <- gsub("Acc", "Accelerometer", name.new)
name.new <- gsub("Gyro", "Gyroscope", name.new)
name.new <- gsub("Mag", "Magnitude", name.new)
name.new <- gsub("-mean-", "_Mean_", name.new)
name.new <- gsub("-std-", "_StandardDeviation_", name.new)
name.new <- gsub("-", "_", name.new)
names(data.extract) <- name.new

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
data.tidy <- aggregate(data.extract [,3:81], by = list(activity = data.extract$activity, subject = data.extract$subject),FUN = mean)
write.table(x = data.tidy, file = "data_tidy.txt", row.names = FALSE)

# Validation:
validation <- read.table("data_tidy.txt")
View(validation)

# Session info
sessionInfo()
