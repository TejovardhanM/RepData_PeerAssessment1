---
title: "Reproducible Research: Peer Assessment 1"
output: 
html_document:
keep_md: true
---
## Loading and preprocessing the data
```{r echo=TRUE}
setwd("C:\\Tejo\\Datascience\\ReproducibleResearch\\Week2\\Assignment\\Assignment\\RepData_PeerAssessment1")
dir()  ## list the files under the current working directory.
library(plyr)
library(lattice)
library(ggplot2)
zipfilename<-"activity.zip"
unzip(zipfilename)  ## unzip the file.
activity<-read.csv(file="activity.csv", header = TRUE, sep = ",")
```
Lets check the dimension of the activity data
```{r echo=TRUE}
dim(activity)
```
lets verify how many NA values present?
```{r echo=TRUE}
summary(activity$steps)
```
lets convert the date factor variable into a date variable.
```{r echo=TRUE}
activity$date<-as.Date(as.character(activity$date, format = "%Y%m%d"))
activity$interval<-format(strptime(sprintf("%04d", activity$interval), format="%H%M"), format = "%H:%M")

```


## What is mean total number of steps taken per day?
```{r echo=TRUE}

actsteps<-ddply(activity, .(date), summarise, stepsperday= sum(steps, na.rm = TRUE))
hist(actsteps$stepsperday, xlab="No of steps", ylab= "interval", main= "total number of steps taken per day"
     , col = "lightblue", labels=TRUE)

```

mean and median of the total number of steps taken per day
```{r echo =TRUE}

totalMeanStepsperday <- mean(actsteps$stepsperday)
totalMedianStepsperday <- median(actsteps$stepsperday)

print (paste("Mean:", totalMeanStepsperday))
print (paste("Median:", totalMedianStepsperday))
```

## What is the average daily activity pattern?
```{r echo=TRUE}

avgactivity<-ddply(activity,.(interval) ,summarise,AvgSteps = mean(steps, na.rm = TRUE))

plot(avgactivity$AvgSteps, type="l", main = "Average Number of Steps/Interval Over All Days", xlab = "Interval", ylab = "Average steps", col="orangered")
MaxAvg<-avgactivity[which.max(avgactivity$AvgSteps),]$AvgSteps
legend("topright", paste("MaxAvgSteps: ", MaxAvg))

```


## Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```{r echo=TRUE}
table(is.na(activity$steps))[2]
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

a. Create a new dataset that is equal to the original dataset but with the missing data filled in.
```{r echo=TRUE}
mergedactivity<-merge(activity, avgactivity, by.x = "interval", by.y = "interval")

index<-is.na(mergedactivity$steps)
mergedactivity$steps[index]<- mergedactivity$AvgSteps[index]
```
b.Make a histogram of the total number of steps taken each day. 

```{r echo=TRUE}

actsteps<-ddply(mergedactivity, .(date), summarise, stepsperday= sum(steps))
hist(actsteps$stepsperday, xlab="No of steps", ylab= "interval", main= "Histogram of Total Number of Steps/Day (Imputed)"
     , col = "lightblue", labels=TRUE)

```

c. and Calculate and report the mean and median total number of steps taken per day.
```{r echo=TRUE}
mean(actsteps$stepsperday)
median(actsteps$stepsperday)
```
Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

## Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```{r echo=TRUE}
mergedactivity$Weekday<-mergedactivity$date
wdays<- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday")
wdays[c(6,7)]
mergedactivity$Weekday<-factor(weekdays(mergedactivity$date, abbreviate=FALSE) %in% wdays[c(6,7)], levels = c(TRUE, FALSE), labels = c("Weekends", "Weekday"))

```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).



```{r echo=TRUE}

weekdayactivity<-ddply(mergedactivity, .(date,Weekday), summarise, AvgSteps= mean(steps))

##xyplot(AvgSteps~date|Weekday, data = weekdayactivity, 
##               ylab="Average Steps", 
##               type = "l",
##               main=" ")

qplot(date, AvgSteps, data=weekdayactivity, geom=c("line"),
    xlab="Interval", ylab="Number of steps", main="") +
    facet_wrap(~ Weekday, ncol=1)

##dev.copy(png, 'HistTotalStepsimputed.png')
##dev.off()
```

 
 