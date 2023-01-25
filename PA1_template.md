---
title: "Rep.Res-Course-Project-1"
author: "Course Participant"
date: "2023-01-25"
output:
  html_document: default
  pdf_document: default
---



## Reproducible Research Course Project 1

This is an R Markdown document providing the code with step-by-step explanations how to complete the Reproducible Research course.


1. Download-Load data set

The following lines of code describe how to download the data set in zip format, unzip it and load the containing csv file in the working directory.

At this step no data transformation was performed, but transformation was done depending on the tasks needed at each of the next steps.


```r
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(url, destfile = "raw_data.zip")
unzip("raw_data.zip")
data <- read.csv("activity.csv")
```

2. Histogram of the total number of steps taken each day

This step stores the total number of steps taken each day in a new variable `total_steps` and plots the histogram of the variable.


```r
total_steps = aggregate(data$steps, by = list(data$date), FUN=sum, na.rm = TRUE)
hist(total_steps$x, xlab = "Total number of steps taken each day", main = "Histogram of the total number of steps\n taken each day")
```

![plot of chunk Histogram](figure/Histogram-1.png)

3. Mean and median number of steps taken each day

The mean and median number of the `total_steps` variable are estimated with the `mean()` and `median()` function and are stored separately.


```r
mean_steps = mean(total_steps$x)
median_steps = median(total_steps$x)
```

The mean and median number of steps taken each day were  9354.2295082 and 10395, respectively.

4. Time series plot of the average number of steps taken

Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
interval_steps = aggregate(data$steps, by = list(data$interval), FUN = mean, na.rm=TRUE)
plot(interval_steps$Group.1, interval_steps$x, type="l", main = "Time Series plot", xlab = "5-minute Intervals", ylab = "Average number of steps taken", col = "green")
```

![plot of chunk Time-Series-plot](figure/Time-Series-plot-1.png)

5. The 5-minute interval that, on average, contains the maximum number of steps


```r
q5 = interval_steps[which.max(interval_steps$x),]
max_interval = q5$Group.1
max_steps = round(q5$x,3)
```

The maximum number of steps (206.17) on average is observed in the 835 5-minute interval.

6. Code to describe and show a strategy for imputing missing data

There are various methods for data imputation regarding missing values. In this case, after we identify which rows (indices) have missing values for the steps, we impute a random number - with the `runif()` function - ranging between the minimum and maximum value of the steps recorded.


```r
set.seed(25-01-2023)
imputed_data = data
n_NA = sum(is.na(imputed_data$steps))
empty_indeces = which(is.na(imputed_data$steps))
for (i in empty_indeces){
  imputed_data$steps[empty_indeces] = runif(1, min = min(imputed_data$steps, na.rm = TRUE), max = max(imputed_data$steps, na.rm = TRUE))
}
# Check that no missing values are observed
sum(is.na(imputed_data)) 
```

```
## [1] 0
```

The total number of missing values is 2304. The above lines of code describe how to impute data and have a new dataset `imputed_data` that contains no missing values.

7. Histogram of the total number of steps taken each day after missing values are imputed


```r
total_steps_imputed = aggregate(imputed_data$steps, by = list(imputed_data$date), FUN=sum, na.rm = TRUE)
hist(total_steps_imputed$x, xlab="Total number of steps taken each day", main="Histogram of the total number of steps\n taken each day - After Imputation", breaks = 5)
```

![plot of chunk Histogram-Imputed](figure/Histogram-Imputed-1.png)


8. Mean and median number of steps taken each day - after data imputation


```r
mean_steps_imputed = mean(total_steps_imputed$x)
median_steps_imputed = median(total_steps_imputed$x)
```

The mean and median number of steps taken each day after data imputation were 2.3447499 &times; 10<sup>4</sup> and 1.1458 &times; 10<sup>4</sup>, respectively.

We see that both the mean and the median have been increased after data imputation. Generally data imputation is considered a very sensitive topic, since it can influence the data distribution, depending on the imputation method.

8. Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

* Create a new factor variable in the data set with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

* Make a panel plot containing a time series plot of the 5-minute interval and the average number of steps taken, averaged across all weekday days or weekend days. 

In this step we used the dataset with the filled-in missing values.


```r
imputed_data$Date_f = as.Date(imputed_data$date, format = "%Y-%m-%d")
imputed_data$Type = NA
# The days are reported in the Greek language, so we used the days in Greek to split the data set to weekdays and weekend.
imputed_data$Type <- ifelse((weekdays(imputed_data$Date_f) == "Σάββατο" | weekdays(imputed_data$Date_f) == "Κυριακή"), "Weekend", "Weekday")
# Check
levels(as.factor(imputed_data$Type))
```

```
## [1] "Weekday" "Weekend"
```

```r
imputed_data$Type = as.factor(imputed_data$Type)

interval_steps_Weekdays = aggregate(imputed_data[imputed_data$Type=="Weekday", "steps"], by = list(imputed_data[imputed_data$Type=="Weekday", "interval"]), FUN = mean, na.rm=TRUE)
interval_steps_Weekends = aggregate(imputed_data[imputed_data$Type=="Weekend", "steps"], by = list(imputed_data[imputed_data$Type=="Weekend", "interval"]), FUN = mean, na.rm=TRUE)
par(mfrow=c(2,1))
plot(interval_steps_Weekdays$Group.1, interval_steps_Weekdays$x, type="l", main = "Time Series plot for the Weekdays", xlab = "5-minute Intervals", ylab = "Average number of steps taken", col = "green")
plot(interval_steps_Weekends$Group.1, interval_steps_Weekends$x, type="l", main = "Time Series plot for the Weekends", xlab = "5-minute Intervals", ylab = "Average number of steps taken", col = "red")
```

![plot of chunk Panel Plot](figure/Panel Plot-1.png)

Thank you.
