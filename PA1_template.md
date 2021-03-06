---
title: "Reproducible Research - Assignment 1"
date: "Sunday, December 21, 2014"
output: html_document  
---
##Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the “quantified self” movement ??? a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.  
This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.


##Loading and preprocessing the data  

```r
fileurl<-"http://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(fileurl, destfile = "activity.zip", mode = "wb")
unzip("activity.zip")
data<-read.csv("activity.csv")
steps_day <- tapply(data$steps, data$date, sum, na.rm=TRUE)
```
##What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.    

###1. Make a histogram of the total number of steps taken each day  


```r
qplot(steps_day, xlab='Total steps per day', ylab='Frequency', binwidth=1000)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

###2. Calculate and report the mean and median total number of steps taken per day

```r
sdMean <- mean(steps_day)
sdMedian <- median(steps_day)
```
Mean:

```
## [1] 9354.23
```
Median:

```
## [1] 10395
```

##What is the average daily activity pattern?  

###1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)   

```r
step_day_int <- aggregate(x=list(sdMean_int=data$steps), by=list(int=data$interval), FUN=mean, na.rm=TRUE)
ggplot(data=step_day_int, aes(x=int, y=sdMean_int)) +  geom_line() +  xlab("time interval") +  ylab("average steps") 
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

###2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?     

```r
sd_max <- which.max(step_day_int$sdMean_int)
time_int_max <- gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", step_day_int[sd_max,'int'])
time_int_max
```

```
## [1] "8:35"
```
##Imputing missing values  
###1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)  

```r
Missing <- length(which(is.na(data$steps)))
Missing
```

```
## [1] 2304
```
###2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.    


###3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
data1 <- data
data1$steps <- impute(data$steps, fun=mean)
```

###4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps_day_1 <- tapply(data1$steps, data1$date, sum)
qplot(steps_day_1, xlab='Total steps per day impute', ylab='Frequency', binwidth=1000)
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png) 

```r
sdMean_1 <- mean(steps_day_1)
sdMedian_1 <- median(steps_day_1)
```
Mean impute:

```
## [1] 10766.19
```
Median impute

```
## [1] 10766.19
```

##Are there differences in activity patterns between weekdays and weekends?  
###1. Create a new factor variable in the dataset with two levels ??? “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.  

```r
data1$dateType <-  ifelse(as.POSIXlt(data1$date)$wday %in% c(0,6), 'weekend', 'weekday')
```
###2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
avedata1 <- aggregate(steps ~ interval + dateType, data=data1, mean)
ggplot(avedata1, aes(interval, steps)) + geom_line() + facet_grid(dateType ~ .) + xlab("5-minute interval") + ylab("avarage number of steps")
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15-1.png) 
