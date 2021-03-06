---
title : "Reproducible Research: Peer Assessment 1"
author: "Vaibhav Kumar Singh"
date: "24/07/2020"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
#### 1. Load the data (i.e. read.csv())

```r
activity_data <- read.csv("activity.csv")
```
#### 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
activity_data$date <- as.Date(activity_data$date)
```


## What is mean total number of steps taken per day?
#### 1. Calculate the total number of steps taken per day

```r
tot_steps_per_day <- aggregate(steps ~ date, activity_data, sum)
```
#### 2. Make a histogram of the total number of steps taken each day

```r
hist(tot_steps_per_day$steps, breaks = 25, xlab = "total number of steps taken each day", main = "Histogram of the total number of steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

#### 3. Calculate and report the mean and median of the total number of steps taken per day

```r
mean(tot_steps_per_day$steps)
```

```
## [1] 10766.19
```

```r
median(tot_steps_per_day$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
#### 1. Make a time series plot (i.e. type ="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
avg_steps_per_interval <- aggregate(steps ~ interval, activity_data, mean)
plot(avg_steps_per_interval$interval, avg_steps_per_interval$steps, type = "l", xlab = "5-minute interval", ylab = "avg. no. of steps taken", main = "Time series plot")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
avg_steps_per_interval$interval[avg_steps_per_interval$steps==max(avg_steps_per_interval$steps)]
```

```
## [1] 835
```


## Imputing missing values
#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(activity_data))
```

```
## [1] 2304
```
#### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
I am filling in all of the missing values in the dataset with the mean for that 5-minute interval.

#### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_data2 <- activity_data
activity_data2$steps <- ifelse(is.na(activity_data2$steps), avg_steps_per_interval$steps[match(activity_data2$interval, avg_steps_per_interval$interval)], activity_data2$steps)
```
#### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day.

```r
tot_steps_per_day2 <- aggregate(steps ~ date, activity_data2, sum)
hist(tot_steps_per_day2$steps, breaks = 25, xlab = "total number of steps taken each day", main = "Histogram of the total number of steps taken each day")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```r
mean(tot_steps_per_day2$steps)
```

```
## [1] 10766.19
```

```r
median(tot_steps_per_day2$steps)
```

```
## [1] 10766.19
```
#### Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
The mean total number of steps taken per day is same as the first part of the assignment but the median total number of steps taken per day has increased slightly (i.e. by 1.19) from the first part of the assignment.


## Are there differences in activity patterns between weekdays and weekends?
#### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
activity_data2$day_type <- ifelse(weekdays(activity_data2$date) == 'Saturday' | weekdays(activity_data2$date) == 'Sunday', 'weekend', 'weekday')
```
#### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```r
avg_steps_per_interval_by_day_type <- aggregate(steps ~ interval + day_type, activity_data2, mean)
library(lattice)
xyplot(steps ~ interval | day_type, data = avg_steps_per_interval_by_day_type, layout = c(1, 2), type = "l", main = "Time series plot")
```

![](PA1_template_files/figure-html/unnamed-chunk-15-1.png)<!-- 
