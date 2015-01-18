# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
unzip("activity.zip")
activity <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
1.Make a histogram of the total number of steps taken each day


```r
stepsbydate <- aggregate(steps ~ date, data = activity, FUN = sum)
hist(stepsbydate$steps, xlab = "number of steps",  main = "Histogram of the total number of steps taken each day")
```

![plot of chunk unnamed-chunk-2](./PA1_template_files/figure-html/unnamed-chunk-2.png) 

2.Calculate and report the mean and median total number of steps taken per day


```r
mn <-mean(stepsbydate$steps)
md <-median(stepsbydate$steps)
```
Mean is 1.0766 &times; 10<sup>4</sup> and Median is 10765.

## What is the average daily activity pattern?

1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
step_average <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE,simplify = TRUE)
plot(step_average, type="l", xlab = "time", ylab = "number of steps", main = "Average number of steps taken in 5-minute interval across all days")
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max_step<-names(which.max(step_average))
```
The max number of step on all days is  835


## Imputing missing values
1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
na_cnt <- sum(!complete.cases(activity$steps))
```
The Total number of missing values are 2304

2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
org_activity<- activity
for (i in 1:length(activity$steps)) {
    if (is.na(activity$steps[i])) {
        for (j in 1:length(step_average)) {
            if (as.character(activity$interval[i]) == names(step_average[j])) 
                activity$steps[i] = step_average[j]
        }
    }
}
```
3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
upd_activity<-activity
```

4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


```r
upd_sum_act <- aggregate(steps ~ date, data = upd_activity, FUN = sum)
hist(upd_sum_act$steps,  xlab = "number of steps", main = "Histogram of the total number of steps taken each day")
```

![plot of chunk unnamed-chunk-9](./PA1_template_files/figure-html/unnamed-chunk-9.png) 
Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
upd_mn <- mean(upd_sum_act$steps)
upd_md <- median(upd_sum_act$steps)
```
The update mean is 1.0766 &times; 10<sup>4</sup> and Median is 1.0766 &times; 10<sup>4</sup>



## Are there differences in activity patterns between weekdays and weekends?
1.Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.
