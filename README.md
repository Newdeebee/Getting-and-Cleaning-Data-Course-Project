# Getting-and-Cleaning-Data-Course-Project
##downloading relevant packages
install.packages("readr")
install.packages("plyr")
library(readr)
library(plyr)
library(dplyr)

##Part1
##Downloading and unzipping the folder
download.file("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip",destfile = "C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\Data.zip")
unzip(zipfile = "C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\Data.zip")
list.files("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset")

## Reading the training dataset- xtrain/ytrain/subject train
xtrain<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\train\\X_train.txt",header = FALSE)
ytrain<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\train\\y_train.txt",header = FALSE)
subject_train<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\train\\subject_train.txt",header = FALSE)


## Reading the test dataset- xtrain/ytrain/subject train
xtest<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\test\\X_test.txt",header = FALSE)
ytest<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\test\\y_test.txt",header = FALSE)
subject_test<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\test\\subject_test.txt",header = FALSE)

##Reading the features dataset
features<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\features.txt",header = FALSE)

##Reading the activity labels dataset
activity_labels<- read.table("C:\\Users\\DeekshaBhardwaj\\Desktop\\R assignment\\UCI HAR Dataset\\activity_labels.txt",header = FALSE)


## Creating column names in training dataset
colnames(xtrain) = features[,2]
colnames(ytrain) = "activityId"
colnames(subject_train) = "subjectId"

## Creating column names in test dataset
colnames(xtest) = features[,2]
colnames(ytest) = "activityId"
colnames(subject_test) = "subjectId"

## Creating column names in activity labels dataset
colnames(activity_labels) <- c('activityId','activityType')

##Merging all the components in one dataset
train<- cbind(xtrain,ytrain,subject_train)
test<- cbind(xtest,ytest,subject_test)

##Merging train and test dataset into one
Combined<- rbind(train, test)



##Part2
# Next step is to read all the values that are available
colNames = colnames(Combined)

#Need to get a subset of all the mean and standards and the correspondongin activityID and subjectID 
mean_and_std = (grepl("activityId" , colNames) | grepl("subjectId" , colNames) | grepl("mean.." , colNames) | grepl("std.." , colNames))

#A subtset has to be created to get the required dataset
setForMeanAndStd <- Combined[ , mean_and_std == TRUE]


##Part3
SetWithActivityNames<- merge(setForMeanAndStd,activity_labels, by= "activityId",all.x = TRUE)

##Part4
##Combined and setForMeanAndStd are the outcomes and solutions

##Part5
##Calculate mean values for each of the columns
means <- suppressWarnings(aggregate(SetWithActivityNames,by = list(SetWithActivityNames$activityId,SetWithActivityNames$subjectId),function (x) mean(as.numeric(as.character(x)))))

##Clean up columns in the "means" dataframe.
means <-subset(means, select=-c(activityId,subjectId))
names(means)[names(means)=="Group.1"] <- "activityID"
names(means)[names(means)=="Group.2"] <- "subjectID"

# To create 'tidy' data, place "subjectID" column at beginning of dataframe.
means <- means %>% select(subjectID, everything())
write.table(means,file="tidy_data.txt",row.name=FALSE)
