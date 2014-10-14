---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data

Unzip and read activity data.


```r
data <- read.csv(unz("activity.zip", "activity.csv"))
```

Convert date column to a date and add a weekday value.

```r
data$date <- as.POSIXct(strptime(data$date, '%Y-%m-%d'))
data$weekday <- factor(weekdays(data$date, abbreviate = TRUE), levels  =
 c('Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'))
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

Aggregated the steps by weekday:


```r
data$weekend <- factor(ifelse(data$weekday %in% c('Sat', 'Sun'), 'weekend', 'weekday'))
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


```r
data.bytime.mean <- aggregate(x = data$steps,
                              by = list(data$interval),
                              FUN = mean,
                              na.rm = TRUE)

data.bytime.sum <- aggregate(x = data$steps,
                             by = list(data$interval),
                             FUN = sum,
                             na.rm = TRUE)
names(data.byweekend) <- c('weekend', 'steps')
```

## What is mean total number of steps taken per day?

Mean and median values.


```r
mean(data.byday$x)
```

```
## [1] 9354
```

```r
median(data.byday$x)
```

```
## [1] 10395
```

## What is the average daily activity pattern?


```r
plot(data.bytime.mean$Group.1, data.bytime.mean$x, type='l')
```

![plot of chunk plot1](figure/plot1.png) 

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?