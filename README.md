#README
##Description Code

##Autor: Danilo Verdugo
###NOTE: Text translated automatic with GOOGLE TRASLATOR!!  (ORIGINAL SPANISH.)


##HERE:
##You should create one R script called run_analysis.R that does the following. 
##1) Merges the training and the test sets to create one data set.
##2) Extracts only the measurements on the mean and standard deviation for each measurement. 
##3) Uses descriptive activity names to name the activities in the data set
##4) Appropriately labels the data set with descriptive variable names. 
##5) Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 



#SET SESSION FOLDER FIST!
setwd("C:/Users/danilo.verdugo/Dropbox/personal/COURSERA DATA SCIENTIST/curso 3/trabajofinal")

#download all data
library(httr) 
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

##download file
file <- "datos.zip"
if(!file.exists(file)){
      print("descarga de archivo")
      download.file(url, file)
}

##unzip download file. descomprimir archivos
print("unziping...")
unzip(file, list = FALSE, overwrite = TRUE)

##var with working folder 
carpeta <-  "UCI HAR Dataset"

##read data from unzipped downloaded file
f <- paste(carpeta,"features.txt",sep="/")
featu <- data.frame()
featu <- read.table(f,sep="",stringsAsFactors=F)

##reading component data file
subj_data_f <- paste(carpeta, "/test/subject_test.txt",sep="")
col <- "id"
subj_data <- read.table(subj_data_f,sep="",stringsAsFactors=F, col.names= col)

subj_y <- paste(carpeta, "/test/y_test.txt",sep="")
col <- "activity"
subj_y_data <- read.table(subj_y,sep="",stringsAsFactors=F, col.names= col)

subj_x <- paste(carpeta, "/test/X_test.txt",sep="")
col <- featu$V2
subj_x_data <- read.table(subj_x,sep="",stringsAsFactors=F, col.names= col)

#INTEGRATE and GET PRINCIPAL COMPONENT
test <- cbind(subj_data,subj_y_data,subj_x_data)



##IDEM to TRAIN
subj_data_f <- paste(carpeta, "/train/subject_train.txt",sep="")
col <- "id"
subj_data <- read.table(subj_data_f,sep="",stringsAsFactors=F, col.names= col)

subj_y <- paste(carpeta, "/train/y_train.txt",sep="")
col <- "activity"
subj_y_data <- read.table(subj_y,sep="",stringsAsFactors=F, col.names= col)

subj_x <- paste(carpeta, "/train/X_train.txt",sep="")
col <- featu$V2
subj_x_data <- read.table(subj_x,sep="",stringsAsFactors=F, col.names= col)


#GET PRINCIPAL COMPONENT
train <- cbind(subj_data,subj_y_data,subj_x_data)


library(plyr)
##TAREA 1
### Merges the training and the test sets to create one data set.
mydataframe <- rbind(train, test)
mydataframe <- arrange(mydataframe, id, activity)

###CLEAN MEMORY
rm(featu, subj_data, subj_y, subj_y_data, subj_x,subj_x_data, test, train)

##TAREA 2
###Extracts only the measurements on the mean and standard deviation for each measurement. 
meanystd <- mydataframe[ ,c(1,2,grep("std", colnames(mydataframe)), grep("mean", colnames(mydataframe)))]

f <- paste(carpeta,"activity_labels.txt",sep="/")
label <- data.frame()

##TAREA 3
###Uses descriptive activity names to name the activities in the data set
label <- read.table(f,sep="",stringsAsFactors=F)

##TAREA 4
### Appropriately labels the data set with descriptive variable names. 
mydataframe$activity <- factor(mydataframe$activity, levels=label$V1, labels=label$V2)

##TAREA 5
###Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 
tidy <- ddply(meanystd, .(id, activity), .fun=function(x){ colMeans( x[ , -c(1:2)]) })
tidy$activity <- factor(tidy$activity, levels=label$V1, labels=label$V2)

f <- paste(carpeta, "/mytidydata.txt" ,sep="")

###Write tidy to disk
write.table(mydataframe,f,row.names = FALSE)




