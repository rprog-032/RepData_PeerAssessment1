# Course Project 1



# Introduction #

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

The data for this assignment can be downloaded from the course web site <https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip>
the dataset **Activity monitoring data [52K]**.


The variables included in this dataset are:
<ul>
<li>steps: Number of steps taking in a 5-minute interval (missing values are coded as NA).</li>

<li>date: The date on which the measurement was taken in YYYY-MM-DD format.</li>

<li>interval: Identifier for the 5-minute interval in which measurement was taken..</li>
</ul>

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.





# Assignment #

This assignment consists in writting a report that answers the questions detailed below.



#### Loading and preprocessing the data ####


1. Load the data (i.e. read.csv())

```r
activity <- read.csv("./activity.csv", header = TRUE, sep = ",")
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


2. Process/transform the data (if necessary) into a format suitable for your analysis



#### What is mean total number of steps taken per day? For this part of the assignment, you can ignore the missing values in the dataset. ####

1. Calculate the total number of steps taken per day

```r
activity_na <- activity[complete.cases(activity), ]
steps_day <- tapply(activity_na$steps, activity_na$date, sum, na.rm = TRUE)
head(steps_day)
```

```
## 2012-10-01 2012-10-02 2012-10-03 2012-10-04 2012-10-05 2012-10-06 
##         NA        126      11352      12116      13294      15420
```


2. Make a histogram of the total number of steps taken each day.

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.2.3
```

```r
qplot(steps_day, geom="histogram", binwidth=1000, main="Total number of steps taken each day")
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)


3. Calculate and report the mean and median of the total number of steps taken per day.

```r
mv <- is.na(steps_day)
mean(steps_day[!mv])
```

```
## [1] 10766.19
```

```r
median(steps_day[!mv])
```

```
## [1] 10765
```


####   What is the average daily activity pattern? ####

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).

```r
interval_day <- tapply(activity_na$steps, activity_na$interval,
                         mean,
                         na.rm = FALSE)

plot(interval_day,
     type = "l",
     xlab = "5-minute interval",
     ylab = "Average across all days",
     main= "Average daily activity pattern"
     )
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
names(interval_day)[interval_day == max(interval_day)]
```

```
## [1] "835"
```


#### Imputing missing values ####


1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs).

```r
mvalues <- sum(is.na(activity$steps))
head(mvalues)
```

```
## [1] 2304
```


2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
activity_fill<-activity
activity_fill$steps[is.na(activity_fill$steps)] <- mean(activity_fill$steps, na.rm = TRUE)
# Checking if there are missing values
sum(is.na(activity_fill$steps))
```

```
## [1] 0
```


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_fill<-activity
activity_fill$steps[is.na(activity_fill$steps)] <- mean(activity_fill$steps, na.rm = TRUE)
```

4. Make a histogram of the total number of steps taken each day and calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps_day_2 <- tapply(activity_fill$steps, activity_fill$date, sum, na.rm = TRUE)
qplot(steps_day_2, geom="histogram")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)

```r
mean(steps_day_2)
```

```
## [1] 10766.19
```

```r
median(steps_day_2)
```

```
## [1] 10766.19
```

These values differ from the estimates from the first part of the assignment.

The impact of imputing missing data on the estimates of the total daily number of steps is that the median is higher, being the same value that the mean.


#### Are there differences in activity patterns between weekdays and weekends? For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part. ####

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activity_fill$day <- weekdays(as.Date(activity_fill$date))

activity_fill$day_type <- ifelse(activity_fill$day == "sábado" | activity_fill$day == 
                                 "domingo", "Weekend", "Weekday")
head(activity_fill)
```

```
##     steps       date interval           day day_type
## 1 37.3826 2012-10-01        0 segunda-feira  Weekday
## 2 37.3826 2012-10-01        5 segunda-feira  Weekday
## 3 37.3826 2012-10-01       10 segunda-feira  Weekday
## 4 37.3826 2012-10-01       15 segunda-feira  Weekday
## 5 37.3826 2012-10-01       20 segunda-feira  Weekday
## 6 37.3826 2012-10-01       25 segunda-feira  Weekday
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
activity_fill_agg <- aggregate (steps ~ interval + day_type, activity_fill, mean)

ggp <- ggplot(activity_fill_agg,aes(x=interval,y=steps,color=day_type))+
  geom_line() +
  facet_grid(.~day_type,scales = "free",space="free") +
  labs(x= "5-minute interval", 
       y = "Average of steps",
       main = "Average across all Weekdays and Weekends"
       )
print(ggp)
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)

