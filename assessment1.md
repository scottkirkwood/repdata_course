---
title: "Reproducible Research: Peer Assessment 1"
author: Scott Kirkwood
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

Unzip and read activity data in one step.


```r
data <- read.csv(unz("activity.zip", "activity.csv"))
```

Convert date and interval to a date.

```r
data$date <- as.POSIXct(strptime(paste(data$date, data$interval), '%Y-%m-%d %H%M'))
```

Convert just the interval to a time.

```r
data$time <- as.POSIXct(strptime(data$interval, '%H%M'))
```

Add weekday column with levels.

```r
data$weekday <- factor(weekdays(data$date, abbreviate = TRUE),
    levels = c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'))
```

Add a weekend column.

```r
data$weekend <- factor(ifelse(data$weekday %in% c('Sat', 'Sun'), 'weekend', 'weekday'))
```

Aggregated the steps over days:

```r
data.byday <- aggregate(data$steps, by=list(data$date), sum, na.rm = TRUE)
```

Aggregated the steps by weekday:

```r
data.byweekday <- aggregate(data$steps, by=list(data$weekday), sum, na.rm = TRUE)
names(data.byweekday) <- c('weekday', 'steps')
levels(data.byweekday$weekday) <- c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun')
```

Aggregated the steps by weekday/weekend:


```r
data.byweekend.mean <- aggregate(x = data$steps,
                                 by = list(data$weekend),
                                 FUN = mean,
                                 na.rm = TRUE)
data.byweekend.sum <- aggregate(x = data$steps,
                                by = list(data$weekend),
                                FUN = sum,
                                na.rm = TRUE)
names(data.byweekend) <- c('weekend', 'steps')
```

Aggregate the steps by time.


```r
data.bytime.mean <- aggregate(x = data$steps,
                              by = list(data$time),
                              FUN = mean,
                              na.rm = TRUE)
names(data.bytime.mean) <- c('time', 'steps')
data.bytime.sum <- aggregate(x = data$steps,
                             by = list(data$time),
                             FUN = sum,
                             na.rm = TRUE)
```

## What is mean total number of steps taken per day?

Mean,


```r
mean(data.byday$x, na.rm = TRUE)
```

```
## [1] 34.05
```

Median

```r
median(data.byday$x[data.byday$x > 0], na.rm = TRUE)
```

```
## [1] 57
```

## What is the average daily activity pattern?


```r
plot(data.bytime.mean$time, data.bytime.mean$steps, type='l')
```

![plot of chunk plot1](figure/plot1.png) 

Time when we have the most steps.


```r
t <- data.bytime.mean$time[which.max(data.bytime.mean$steps)]
format(t, "%H:%M")
```

```
## [1] "15:50"
```

## Imputing missing values

1) Total number of missing values:


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```


## Are there differences in activity patterns between weekdays and weekends?
