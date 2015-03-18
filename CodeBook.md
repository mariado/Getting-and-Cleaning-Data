## CodeBook
## Getting and Cleaning Data Project

This markdown document describes the variables, the data, and any transformations or work that is performed to clean up the data and produce a tidy data set file.

### Coding used in this project

1. Preparation

This step downloads, unZip and import relevant files as listed under __README.md__ _### Raw input files_ section.

2. Merges the training and the test sets to create one data set
 
This step merges the two outputs from _step 1_ and output it as ```{r} fulldata ``` to feed into following steps.
 
3. Extracts only the measurements on the mean and standard deviation for each measurement

This step creates a logical vector based on whether variable is a _"mean()"_ or _"std()"_ value according to names from _"features.txt"_.

```{r} grepl("mean\\(\\)",features$feature)|grepl("std\\(\\)",features$feature)```
 
It then output ```{r} fulldata ``` with only columns (variables) that are TRUE when subsetted against the logical vector. 

4. Uses descriptive activity names to name the activities in the data set

This step utilized _"activity_labels.txt"_ to substitude all activity numbers with descriptive wordings. To implement this, package ```{r} require(plyr)``` was loaded and function ```{r} merge() ``` was used to bring across referencing.

5. Appropriately labels the data set with descriptive variable names

This step brought across referencing of variable names to dataset colnames using the same logical vector from _step 3_, using ```{r} names()``` function in R. The first output _"tidy_dataset.csv"_ is now ready.
 
```{r} write.csv(fulldata,file="tidy_dataset.csv",row.names=FALSE)```

6. Creates a second, independent tidy data set with the average of each variable for each activity and each subject

This step applies further aggregation to first output with package ```{r} require(reshape2)``` was loaded to call melt and dcast funcitons. Mean was taken on each variable by all subject and activity combinations.
 
Again as per _step 5_ output the second tidy set to _"tidy_dataset_2.csv"_