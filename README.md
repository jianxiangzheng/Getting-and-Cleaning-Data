# Getting-and-Cleaning-Data
## test data
XTest<- read.table("UCI HAR Dataset/test/X_test.txt")

YTest<- read.table("UCI HAR Dataset/test/Y_test.txt")

SubjectTest <-read.table("UCI HAR Dataset/test/subject_test.txt")

## train data
XTrain<- read.table("UCI HAR Dataset/train/X_train.txt")

YTrain<- read.table("UCI HAR Dataset/train/Y_train.txt")

SubjectTrain <-read.table("UCI HAR Dataset/train/subject_train.txt")

## features and activity
features<-read.table("UCI HAR Dataset/features.txt")

activity<-read.table("UCI HAR Dataset/activity_labels.txt")

# 1. Merges the training and the test sets to create one data set.
X<-rbind(XTest, XTrain)

Y<-rbind(YTest, YTrain)

Subject<-rbind(SubjectTest, SubjectTrain)
## Dimension of new datasets
dim(X)
## [1] 10299   561
dim(Y)
## [1] 10299     1
dim(Subject)
## [1] 10299     1

# 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
## getting features indeces which contain mean() and std() in their name
index<-grep("mean\\(\\)|std\\(\\)", features[,2]) 
## count of features
length(index)
## [1] 66
## getting only variables with mean/stdev
X<-X[,index]
## checking dim of subset
dim(X)
## [1] 10299    66

# 3. Uses descriptive activity names to name the activities in the data set
## replacing numeric values with lookup value from activity.txt
Y[,1]<-activity[Y[,1],2]
 
# 4. Appropriately labels the data set with descriptive variable names. 
## getting names for variables
names<-features[index,2]
## updating colNames for new dataset
names(X)<-names 

names(Subject)<-"SubjectID"

names(Y)<-"Activity"

CleanedData<-cbind(Subject, Y, X)

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
CleanedData<-data.table(CleanedData)
## features average by Subject and by activity
TidyData <- CleanedData[, lapply(.SD, mean), by = 'SubjectID,Activity'] 

dim(TidyData)
## [1] 180  68

write.table(TidyData, file = "Tidy.txt", row.names = FALSE)
