# Reproducible Research: Peer Assessment 1
## Loading and preprocessing the data

```r
setwd("C:\\Tejo\\Datascience\\ReproducibleResearch\\Week2\\Assignment\\Assignment\\RepData_PeerAssessment1")
dir()  ## list the files under the current working directory.
```

```
## [1] "activity.csv"       "activity.zip"       "doc"               
## [4] "instructions_fig"   "PA1_template.html"  "PA1_template.md"   
## [7] "PA1_template.Rmd"   "PA1_template_files" "README.md"
```

```r
zipfilename<-"activity.zip"
unzip(zipfilename)  ## unzip the file.
activity<-read.csv(file="activity.csv", header = TRUE, sep = ",")
```
Lets check the dimension of the activity data

```r
dim(activity)
```

```
## [1] 17568     3
```
lets verify how many NA values present?

```r
summary(activity$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    0.00    0.00    0.00   37.38   12.00  806.00    2304
```
lets convert the date factor variable into a date variable.

```r
activity$date<-as.Date(as.character(activity$date, format = "%Y%m%d"))
```


## What is mean total number of steps taken per day?

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.3.1
```

```r
totalStepsperDay <- with(activity, tapply(steps, date, sum, na.rm=TRUE))
qplot(totalStepsperDay, xlab='Total steps per day', ylab='Frequency', main = "Total number of steps taken per day", binwidth= 1000)
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

mean and median of the total number of steps taken per day

```r
totalMeanStepsperday <- mean(totalStepsperDay)
totalMedianStepsperday <- median(totalStepsperDay)

print (paste("Mean:", totalMeanStepsperday))
```

```
## [1] "Mean: 9354.22950819672"
```

```r
print (paste("Median:", totalMedianStepsperday))
```

```
## [1] "Median: 10395"
```

## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
