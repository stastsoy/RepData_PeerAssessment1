# Reproducible Research: Peer Assessment 1






## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis



```r
setwd("C:/Users/spbstsoy/Documents/Course5Week2")

df=read.csv('activity.csv')

df$date <- as.Date(as.character(df$date), format= "%Y-%m-%d")
```



## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
3. Calculate and report the mean and median of the total number of steps taken per day



```r
df.no.NA= subset(df, !is.na(df$steps))

Total.by.date = as.data.frame(df.no.NA %>% group_by(date) %>% summarise(Total.Steps=sum(steps)))

hist(Total.by.date$Total.Steps,
     main="Histogram of the total number of steps taken each day", 
     xlab="Number of steps", 
     ylab="Count")
```

![](PA1_template_files/figure-html/steps per day-1.png)<!-- -->

The mean total number of steps per day is 10766.19 and the median is 10765



## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
Five.Average =as.data.frame(df.no.NA %>% group_by(interval) %>% summarize(Average.Steps=mean(steps)))

plot(Five.Average$interval, Five.Average$Average.Steps, 
     type="l",
     xlab="Interval",
     ylab="Average steps",
     main="Average steps during 5 min interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

The highest average step count happened during interval         835



## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


There are 2304 NAs found in the data.


```r
df.full = df
for (i in 1:nrow(df.full)) {
    if (is.na(df.full$steps[i])) {
        n = which(df.full$interval[i] == Five.Average$interval)
        df.full$steps[i] = Five.Average$Average.Steps[n]
    }
}


Total.by.date = as.data.frame(df.full %>% group_by(date) %>% summarise(Total.Steps=sum(steps)))

hist(Total.by.date$Total.Steps,
     main="Histogram of the total number of steps taken each day", 
     xlab="Number of steps", 
     ylab="Count")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

The mean total number of steps per day is 10766.19 and the median is 10766.19

The mean is unchanged, but the median moved slightly and is now equal to the mean.


## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.



```r
df.full$day = weekdays(df.full$date)
df.full$daytype = "weekday"
df.full$daytype [df.full$day %in% c("Saturday", "Sunday")] = "weekend"

Day.Average = as.data.frame(df.full %>% group_by(daytype, interval) %>% summarize(Average.Steps=mean(steps)))

qplot(interval, Average.Steps, data=Day.Average,
      geom="line",
      xlab="Interval",
      ylab="Average Number of Steps",
      main="Average steps Weekends vs. Weekdays",
      facets=daytype ~ .)
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->






