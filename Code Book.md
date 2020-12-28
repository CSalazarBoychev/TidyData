# Code Book for TidyData
Getting and Cleaning Data: Course Project

## VARIABLES:

### features, data.train.x, data.train, data.test.x,
Reads data to incoprorate the headers from Features, but the datasets from Testing and Training folders, and converts them to a single data frame for each dataset.
The original data is raw, without headers and with space as separator. The output keeps these features.

### data.merge
The data frames that integrate both datasets are incorporated into a single dataframe.

### mean_std.extract
Extracts only the measurements on the mean and standard deviation based on the columns of the dataset.

### data.extract <- data.merge[,c(1,2,mean_std.extract + 2)]
Adds the mean and standard deviation to the bigger dataset.

### activity
Reads and formats the six activity codes and names from activity_labels file.

### name.new
Appropriately labels the data set with descriptive variable names from data.extract.

### data.tidy
Uses aggregate and write.table function to create a second, independent tidy data set with the average of each variable for each activity and each subject.

### Validation:
Reads the data.tidy.txt file and opens it with the View function.

### Session info
Prints version information about R, the OS and attached or loaded packages.
