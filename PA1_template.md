Download and preprocess the file.
---------------------------------

``` r
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 3.3.2

``` r
fileURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
download.file(fileURL, destfile = "repdata%2Fdata%2Factivity.zip", method = "curl")
unzip("repdata%2Fdata%2Factivity.zip", exdir = "data")
activity <- read.csv("data/activity.csv")
```

What is mean total number of steps taken per day?
-------------------------------------------------

1.  Calculate the total number of steps taken per day

``` r
totalSteps <- tapply(activity$steps, activity$date, sum, na.rm = TRUE)
head(totalSteps)
```

    ## 2012-10-01 2012-10-02 2012-10-03 2012-10-04 2012-10-05 2012-10-06 
    ##          0        126      11352      12116      13294      15420

1.  Make a histogram of the total number of steps taken each day

``` r
qplot(totalSteps, binwidth = 1000, xlab = "Number of Steps", ylab = "Frequency",
      main = "Total Number of Steps Per Day Frequency")
```

![](PA1_template_files/figure-markdown_github/unnamed-chunk-3-1.png)
