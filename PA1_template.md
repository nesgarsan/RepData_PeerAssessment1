# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data


```r
activity <- read.csv("activity.csv", colClasses = c("numeric", "character", "numeric"))
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

## What is mean total number of steps taken per day?


```r
# 1.Calculate the total number of steps taken per day
stepsDay <- aggregate(steps ~ date, activity , sum)

# 2.Make a histogram of the total number of steps taken each day
hist(stepsDay $steps, main = "Total number of steps taken each day", xlab = "Steps/day")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

```r
# 3.Calculate and report the mean and median of the total number of steps taken per day
```

Mean:

```r
mean(stepsDay$steps)
```

```
## [1] 10766.19
```

Median:

```r
median(stepsDay$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

```r
# 1.Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
AverageSteps <- tapply(activity$steps, activity$interval, mean, na.rm = TRUE)

plot(row.names(AverageSteps), AverageSteps, type = "l", xlab = "5 min interval", 
    ylab = "Days", main = "Average number of steps taken")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

```r
# 2.Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
names(interval_num <- which.max(AverageSteps))
```

```
## [1] "835"
```


## Imputing missing values


```r
# 1.Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
sum(is.na(activity$steps))
```

```
## [1] 2304
```

```r
# 2.Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
```
Strategy is to replace NAs by meanin 5 min interval


```r
imputeValue<- round(mean(activity$steps, na.rm = TRUE))
imputeValue
```

```
## [1] 37
```

```r
# 3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

activityNotNAs<- activity

for (i in 1:nrow(activityNotNAs)) {
    if(is.na(activityNotNAs$steps[i])) {
        activityNotNAs$steps[i] <- imputeValue
    }
}


# 4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


stepsDayNotNAs <- aggregate(steps ~ date, activityNotNAs , sum)
hist(stepsDayNotNAs $steps, main = "Total number of steps taken each day", xlab = "Steps/day")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
mean(stepsDayNotNAs$steps)
```

```
## [1] 10751.74
```

```r
median(stepsDayNotNAs$steps)
```

```
## [1] 10656
```

```r
#Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps? 
```
Mean and median has slightly reduced. In addition, the total number of steps per day has increased.

## Are there differences in activity patterns between weekdays and weekends?


```r
# 1.Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

Sys.setlocale("LC_TIME", "English") 
```

```
## [1] "English_United States.1252"
```

```r
activityNotNAs$dayType <- weekdays(as.Date(activityNotNAs$date))
activityNotNAs$dayType <- ifelse(activityNotNAs$dayType %in% c("Saturday", "Sunday"),"weekend", "weekday")


# 2.Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

AverageSteps<- aggregate(steps ~ interval + dayType, data = activityNotNAs, mean)
names(AverageSteps) <- c("interval", "dayType", "steps")

library(lattice)

xyplot(steps ~ interval | dayType, AverageSteps, type = "l", layout = c(1, 2), xlab = "Time Interval (5 min)", ylab = "Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

