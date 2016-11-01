---
title: "Codebook"
author: "taehoon"
date: "2016년 11월 1일"
output: html_document
---
***
 coodbook md describes the variables, the data, and any transformations or work that was performed to clean up the data in R script(run_analysis.R).

The script  run_analysis.R performs the 5 steps described in the course project's definition.

- step1 : Merges the training and the test sets to create one data set.

```{r}
  x_train <- read.table("./coursera_project_03/data/train/X_train.txt")
  y_train <- read.table("./coursera_project_03/data/train/y_train.txt")
  subject_train <- read.table("./coursera_project_03/data/train/subject_train.txt")
  
  x_test <- read.table("./coursera_project_03/data/test/X_test.txt")
  y_test <- read.table("./coursera_project_03/data/test/y_test.txt")
  subject_test <- read.table("./coursera_project_03/data/test/subject_test.txt")
  
  #vector data
  x_data <- rbind(x_train, x_test)
  
  #label
  y_data <- rbind(y_train,y_test)
  
  #subject
  subject_data <- rbind(subject_train, subject_test)
```
- step2 : Extracts only the measurements on the mean and standard deviation for each measurement.
```{r}
  feature_list <- read.table("./coursera_project_03/data/features.txt")
  feature_mean_std <- grep("(mean|std)", feature_list[,2])
  x_data <- x_data[,feature_mean_std]
  names(x_data) <- as.character(feature_list[feature_mean_std,2])
```
- step3 : Uses descriptive activity names to name the activities in the data set
```{r}
  activity_labels <- read.table("./coursera_project_03/data/activity_labels.txt")
  y_data[,1]<- activity_labels[y_data[,1],2]
```
- step4 : Appropriately labels the data set with descriptive variable names.
```{r}
  names(y_data)<-"activity"
  names(subject_data)<-"subject"
  alldata <- cbind(subject_data,y_data,x_data)
  alldata$subject <- as.factor(alldata$subject)
```
- step5 : From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject
```{r}
  melt_alldata <- melt(alldata, id=c("subject","activity"))
  cast_alldata <- cast(melt_alldata, subject + activity ~ variable, mean)
  tidy_data <-write.table(cast_alldata,"./coursera_project_03/data/tidy.txt")
```
