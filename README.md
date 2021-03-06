run_analysis
============
## Stage00: Preparation
setwd("C:/Work/SUM/HACE/JohnHopkins_Data/DataAnalysis/data/getdata-projectfiles-UCI HAR Dataset/UCI HAR Dataset")

## Stage1: Merges the training and the test sets to create one data set
# Stage1.1: Bring data
tstlabel <- read.table("./test/y_test.txt", col.names="label")
tstsubject <- read.table("./test/subject_test.txt", col.names="subject")
tstdata <- read.table("./test/X_test.txt")
trlabel <- read.table("./train/y_train.txt", col.names="label")
trsubject <- read.table("./train/subject_train.txt", col.names="subject")
trdata <- read.table("./train/X_train.txt")
# Stage1.2: Combining
data <- rbind(cbind(tstsubject, tstlabel, tstdata),
              cbind(trsubject, trlabel, trdata))

## Stage2: Extracts only the measurements on the mean and standard deviation for each measurement
# Stage2.1: Bring features with mean, & s.d.
fts <- read.table("features.txt", strip.white=TRUE, stringsAsFactors=FALSE)
fts.m.sd <- fts[grep("mean\\(\\)|std\\(\\)", fts$V2), ]
# Stage2.2: Select means & s.d.
msd <- data[, c(1, 2, fts.m.sd$V1+2)]

## Stage3: Uses descriptive activity names to name the activities in the data set
lbls <- read.table("activity_labels.txt", stringsAsFactors=FALSE)
msd$label <- lbls[msd$label, 2]

## stage4: Appropriately labels the data set with descriptive variable names
# Stage4.1: Preparation
coln <- c("subject", "label", fts.m.sd$V2)
# Stage4.1: tidy & list
coln <- tolower(gsub("[^[:alpha:]]", "", coln))
# Stage4.2: list
colnames(msd) <- coln

## stage5: Creates a second, independent tidy data set with the average of each variable for each activity and each subject
# Stage5.1: Tidy Set with Average
Data <- aggregate(msd[, 3:ncol(msd)],
                       by=list(subject = msd$subject, 
                               label = msd$label),
                       mean)
# Stage5.2: Write Data
write.table(format(Data, scientific=T), "tidy2.txt",
            row.names=F, col.names=F, quote=2)
