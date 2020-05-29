---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

1. Load the data (i.e. read.csv()\color{red}{\verb|read.csv()|}read.csv())
1. Process/transform the data (if necessary) into a format suitable for your analysis


```r
d <- read.csv("activity.csv")
```


## What is mean total number of steps taken per day?
1. Calculate the total number of steps taken per day
1. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
1. Calculate and report the mean and median of the total number of steps taken per day



```r
dd <- na.omit(d)
dd <- aggregate(dd$steps, by=list(Date=dd$date), FUN=sum)
hist(dd$x, xlab="Total Steps Each Day", ylab="Count", main ="Total Steps per day", col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

* The mean total number of steps taken per day is: 

```r
mean(dd$x)
```

```
## [1] 10766.19
```
* The median total number of steps taken per day is: 

```r
median(dd$x)
```

```
## [1] 10765
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
1. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
d2 <- na.omit(d)
d5 <- aggregate(d2$steps, by=list(interval=d2$interval), FUN=mean)
plot(x=d5$interval, y=d5$x, type="l", ylab = "Average steps taken", xlab = "Internval", main = "Average steps taken during 5 minute interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

The following interval 

```r
d5$interval[which.max(d5$x)]
```

```
## [1] 835
```
contains the maximum number of steps.

## Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
1. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
1. Create a new dataset that is equal to the original dataset but with the missing data filled in.
1. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

There are 

```r
sum(is.na(d$steps))
```

```
## [1] 2304
```
missing values in the dataset.


```r
d2 <- d
for (i in 1:nrow(d2))
{
  if (is.na(d2$steps[i]))
  {
     n <- which(d2$interval[i]==d5$interval)
     d2$steps[i] <- d5[n,]$x
  }
}
dd2 <- aggregate(d2$steps, by=list(Date=d2$date), FUN=sum)
hist(dd2$x, xlab="Total Steps Each Day", ylab="Count", main ="Total Steps per day (filled w/ avg. interval)", col="red")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
* The mean total number of steps taken per day for this filled data set is: 

```r
mean(dd2$x)
```

```
## [1] 10766.19
```
* The median total number of steps taken per day for this filled data set is: 

```r
median(dd2$x)
```

```
## [1] 10766.19
```

Replacing the NA with average steps for 5 minute interval makes the median and the mean total of steps taken the same for this data set but the median is the same as before.


## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
1. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.



```r
d2$date <- as.Date(d2$date)
d2$day <- weekdays(d2$date)
d2$daytype <- "weekday"
d2$daytype[d2$day %in% c("Saturday", "Sunday")] <- "weekend"
d2wd <- subset(d2, d2$daytype == "weekday")
d2we <- subset(d2, d2$daytype == "weekend")
d5d2wd <- aggregate(d2wd$steps, by=list(Interval=d2wd$interval), FUN=mean)
d5d2we <- aggregate(d2we$steps, by=list(Interval=d2we$interval), FUN=mean)
par(mar=c(0.5, 0.5, 0.2, 0.2), mfrow=c(2,1),oma = c(4,4,0.2,0.2))
plot(x=d5d2wd$Interval, y=d5d2wd$x, type="l", ylab = "Average steps taken", xlab = "Internval", main = "Weekday")
plot(x=d5d2we$Interval, y=d5d2we$x, type="l", ylab = "Average steps taken", xlab = "Internval", main = "Weekend")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Based on the plots, on average, there are more steps taken on the weekends after 8am.  This indicates the person's tasks during the weekdays require less movements, i.e. less steps to be taken.
