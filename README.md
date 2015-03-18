## Getting-and-Cleaning-Data

### Step 1 (download,unzip,and import files)

fileurl<- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip" 

download.file (fileurl, destfile = "Dataset.zip") 

unzip ("Dataset.zip") 
                
### Import files
### First set: ref files;

activities <- read.table ("activity_labels.txt",col.names=c("activity_id","activity")) 

features <- read.table("features.txt",col.names=c("feature_id","feature")) 

### Second set: test

subject_test <- read.table("./test/subject_test.txt",col.names="subject") 

x_test <- read.table("./test/X_test.txt") 

y_test <- read.table("./test/Y_test.txt",col.names="activity_id") 

### Get a full dataset of test

test_set <- cbind(subject_test, y_test, x_test) 

### Third set: train

subject_train <- read.table("./train/subject_train.txt",col.names="subject") 

x_train <- read.table("./train/X_train.txt") 

y_train <- read.table("./train/Y_train.txt",col.names="activity_id") 

### Get a full dataset of train 

train_set <- cbind (subject_train, y_train, x_train) 

### clean out working data 

rm ("x_test","y_test","subject_test","x_train","y_train","subject_train") 

### Step 2: 
### Merges the training and the test sets to create one data set. 

fulldata <- rbind (train_set,test_set) 

### clean out working data

rm ("test_set","train_set") 

### Step 3:
### Extracts only the measurements on the mean and standard deviation for each measurement.

### create a logic check vector from features

logic <- grepl("mean\\(\\)",features$feature)|grepl("std\\(\\)",features$feature) 

### expand the logic to include columns other than features (i.e. subjects & activities) 

logic <- c(as.logical("TRUE"),as.logical("TRUE"),logic) 

### check against logic vector,only export cols TRUE with either of logic below;

fulldata <- fulldata [,logic] 

### Step 4:
### Use descriptive activity names to name the activities in the data set
### use plyr package.merge function 

require(plyr) 

temp <- merge (activities,fulldata,by="activity_id") 

### output the same column order but using descriptive activity 

fulldata <- cbind (temp[,3:2],temp[,4:length(temp)]) 

### clean out once above step completed 

rm ("temp") 

### Step 5: 
### Appropriately label the data set with descriptive variable names.
### use features.txt as reference for labelling variables

names (fulldata) [3:length(fulldata)] <- as.vector(features[logic[3:563],2]) 

### ouput final tidy dataset (only mean&std) with proper desc and labels 

write.csv(fulldata,file="tidy_dataset.csv",row.names=FALSE) 

### Step 6: 
### Create a second, independent tidy data set with the average of each variable for each activity and each subject.

### load library reshape2 to use melt function
require(reshape2)
melted <- melt(fulldata, id=c("subject","activity"),measure.vars=as.vector(names(fulldata)[3:length(fulldata)]))

### apply mean by subject & activity on tidy dataset above
tidydata_2 <- dcast(melted,subject+activity ~ variable,mean)
        
### output onto a .csv file
write.csv (tidydata_2,file="tidy_dataset_2.csv",row.names=FALSE)
