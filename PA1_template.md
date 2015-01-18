# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
The data will be processed using the plyr library. Make sure the file activity.csv is located in your working directory


```r
library("plyr")
data <- read.csv("activity.csv", colClasses = c("numeric", "Date", "numeric"))
```

## What is mean total number of steps taken per day?

```r
t <- ddply(data, .(date), summarise, total=sum(steps, na.rm=T))
mean(t$total)
```

```
## [1] 9354.23
```

## What is the average daily activity pattern?

```r
ap <- ddply(data, .(interval), summarise, average=mean(steps, na.rm=T))
plot(ap,  type="l", main="Activity pattern")
```

![](./PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

## Imputing missing values
Checking na values shows that there are alot of missing values.

```r
nrow(data[is.na(data$steps),])
```

```
## [1] 2304
```
A strategy for minimizing the impact of na's used is to take the median value.

```r
data2 <- ddply(data, 
      .(interval), 
      transform, 
      steps=ifelse(is.na(steps), median(steps, na.rm=TRUE), steps))
```

## Are there differences in activity patterns between weekdays and weekends?

```r
## add a column containing true if its a weekend day otherwise false. Note! Swedish locale
data2$weekend <- weekdays(data2$date) %in% c("Lördag", "Söndag")
## calculate means
r1 <- ddply(data2[data2$weekend,], .(interval), summarise, average=mean(steps))
r2 <- ddply(data2[!data2$weekend,], .(interval), summarise, average=mean(steps))
## plot lines
plot(r1$interval, r1$average, type="l", col="red", xlab="Interval", ylab="Average", ylim=c(0,200))
lines(r2$interval, r2$average, col="blue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-6-1.png) 


