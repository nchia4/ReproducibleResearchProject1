Download and preprocess the file.
---------------------------------

    library(ggplot2)

    fileURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
    download.file(fileURL, destfile = "repdata%2Fdata%2Factivity.zip", method = "curl")
    unzip("repdata%2Fdata%2Factivity.zip", exdir = "data")
    activity <- read.csv("data/activity.csv")

What is mean total number of steps taken per day?
-------------------------------------------------

1.  Calculate the total number of steps taken per day

<!-- -->

    totalSteps <- tapply(activity$steps, activity$date, sum, na.rm = FALSE)
    head(totalSteps)

    ## 2012-10-01 2012-10-02 2012-10-03 2012-10-04 2012-10-05 2012-10-06 
    ##         NA        126      11352      12116      13294      15420

1.  Make a histogram of the total number of steps taken each day

<!-- -->

    qplot(totalSteps, binwidth = 1000, xlab = "Number of Steps", ylab = "Frequency",
          main = "Total Number of Steps Per Day - Frequency")

    ## Warning: Removed 8 rows containing non-finite values (stat_bin).

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day

<!-- -->

    mean(totalSteps)

    ## [1] NA

    median(totalSteps)

    ## <NA> 
    ##   NA

What is the average daily activity pattern?
-------------------------------------------

1.  Make a time series plot (i.e. type="l") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    intervalData <- aggregate(list(steps = activity$steps), by = list(intervals = activity$interval),
                              FUN = mean, na.rm = TRUE)
    ggplot(intervalData, aes(intervals, steps)) +
      geom_line() +
      xlab("5 Minute Intervals") +
      ylab("Average Steps") +
      ggtitle("Average Number of Steps for Every 5 Minute Interval")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

<!-- -->

    intervalData[intervalData$steps == max(intervalData$steps), ]

    ##     intervals    steps
    ## 104       835 206.1698

Imputing Missing Values
-----------------------

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs)

<!-- -->

    sum(is.na(activity$steps))

    ## [1] 2304

1.  Devise a strategy for filling in all of the missing values in
    the dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.

<!-- -->

    fillNA <- function(steps, interval){
      filled <- NA
      if (!is.na(steps))
        filled <- c(steps)
      else 
        filled <- (intervalData[intervalData$interval == interval, "steps"])
      return(filled)
    }

1.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    updatedTable <- activity
    updatedTable$steps <- mapply(fillNA, updatedTable$steps, updatedTable$interval)

1.  Make a histogram of the total number of steps taken each day and
    Calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    dailySteps <- tapply(updatedTable$steps, updatedTable$date, FUN = sum)
    qplot(dailySteps, binwidth = 1000, xlab = "Total Number of Steps", ylab = "Frequency",
          main = "Total Number of Steps Per Day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

    mean(dailySteps)

    ## [1] 10766.19

    median(dailySteps)

    ## [1] 10766.19

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

1.  Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    dayFunction <- function(date){
      DoW <- weekdays(date)
      if (DoW %in% c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday"))
        return("weekday")
      else if (DoW %in% c("Saturday", "Sunday"))
        return("weekend")
      else
        return("NA")
    }
    updatedTable$date <- as.Date(updatedTable$date)
    updatedTable$DoW <- sapply(updatedTable$date, dayFunction)

1.  Make a panel plot containing a time series plot (i.e. type="l") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    averageSteps <- aggregate(steps ~ interval + DoW, data = updatedTable, mean)
    ggplot(averageSteps, aes(interval, steps)) +
      geom_line() +
      facet_grid(DoW ~ .) +
      xlab("5 Minute Intervals") +
      ylab("Average Steps Per Day") +
      ggtitle("Average Number of Daily Steps - Weekdays and Weekends")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-12-1.png)
