---
title: "Data Visualization for Exploratory Data Analysis"
output: 
  html_document:
    keep_md: true
    toc: true
    toc_float: true
---

# Data Visualization Project 03


In this exercise you will explore methods to create different types of data visualizations (such as plotting text data, or exploring the distributions of continuous variables).


## PART 1: Density Plots

Using the dataset obtained from FSU's [Florida Climate Center](https://climatecenter.fsu.edu/climate-data-access-tools/downloadable-data), for a station at Tampa International Airport (TPA) for 2022, attempt to recreate the charts shown below which were generated using data from 2016. You can read the 2022 dataset using the code below: 


``` r
library(tidyverse) # Load the tidyverse package to use needed relevant functions like ggplot
weather_tpa <- read_csv("https://raw.githubusercontent.com/aalhamadani/datasets/master/tpa_weather_2022.csv")
# random sample 
sample_n(weather_tpa, 4)
```

```
## # A tibble: 4 × 7
##    year month   day precipitation max_temp min_temp ave_temp
##   <dbl> <dbl> <dbl>         <dbl>    <dbl>    <dbl>    <dbl>
## 1  2022     2    10          0          72       47     59.5
## 2  2022    12    27          0          68       40     54  
## 3  2022     1    16          0.68       71       62     66.5
## 4  2022     2     5          0.02       71       55     63
```

See Slides from Week 4 of Visualizing Relationships and Models (slide 10) for a reminder on how to use this type of dataset with the `lubridate` package for dates and times (example included in the slides uses data from 2016).

Using the 2022 data: 

(a) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_facet.png" width="80%" style="display: block; margin: auto;" />

Hint: the option `binwidth = 3` was used with the `geom_histogram()` function.


``` r
library(lubridate) # Load the lubridate package to alter ymd data
weather_clean <- weather_tpa %>%
  # Combine year, month, day into one column using - separators
  unite("doy", year, month, day, sep = "-") %>%
  # Alter the data
  mutate(doy = ymd(doy), # Converts united column to a date column
         max_temp = as.double(max_temp),
         min_temp = as.double(min_temp),
         precipitation = as.double(precipitation)) %>%
  # Extracts month out of date column into its own called "month"
  mutate(month = month(doy, label = TRUE, abbr = FALSE))

# Filter data for NA values
weather_filt <- weather_clean %>%
  filter(!is.na(max_temp))
```



``` r
# Plot histogram of max temp for each month, filling by month
ggplot(weather_filt, aes(x = max_temp, fill = factor(month))) +
  geom_histogram(binwidth = 3, color = "white", linewidth = 1) +
  # Create 12 charts that are in 4 columns
  facet_wrap(~ month, ncol = 4) +
  # Limit the range of the x and y axis
  coord_cartesian(xlim = c(55, 97), ylim = c(0,20)) +
  # Choose a coloring scheme
  scale_fill_viridis_d() +
  theme_bw() +
  # Alter size, color, and look of aesthetics to resemble the reference graph from above
  theme(strip.text = element_text(size = 14),
        axis.title.x = element_text(size = 15),
        axis.title.y = element_text(size = 15),
        axis.text.x = element_text(size = 14, color = "gray40"),
        axis.text.y = element_text(size = 15, color = "gray40"),
        axis.ticks = element_line(size = 0.75),
        axis.ticks.length = unit(0.2, "cm"),
        legend.position = "none") +
  # Add labels
  labs(x = "Maximum Temperatures", y = "Number of Days")
```

```
## Warning: The `size` argument of `element_line()` is deprecated as of ggplot2 3.4.0.
## ℹ Please use the `linewidth` argument instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](amato_project_03_files/figure-html/unnamed-chunk-4-1.png)<!-- -->


(b) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_density.png" width="80%" style="display: block; margin: auto;" />

Hint: check the `kernel` parameter of the `geom_density()` function, and use `bw = 0.5`.


``` r
# Plot density vs max temp line graph using geom_density() function
ggplot(weather_filt, aes(x = max_temp)) +
  # Use kernel "epanechnikov" as it resembles the reference graph above the most
  geom_density(kernel = "epanechnikov", bw = 0.5, color = "black", linewidth = 1, fill = "gray50") +
  # Limit the range of the y axis
  coord_cartesian(ylim = c(0,0.08)) +
  theme_minimal() +
  # Alter size, color, and look of aesthetics to resemble the reference graph from above
  theme(axis.title.x = element_text(size = 15), axis.title.y = element_text(size = 15),
        axis.text.x = element_text(size = 15), axis.text.y = element_text(size = 15)) +
  # Add labels to graph
  labs(x = "Max temperature", y = "density")
```

![](amato_project_03_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


(c) Create a plot like the one below:

<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_density_facet.png" width="80%" style="display: block; margin: auto;" />

Hint: default options for `geom_density()` were used. 


``` r
# Plot Density vs Max temp line graph for each month using the geom_density() function
ggplot(weather_filt, aes(x = max_temp, fill = factor(month))) +
  geom_density(color = "black", linewidth = 1, alpha = 0.5) +
  # Create 12 charts that are in 4 columns
  facet_wrap(~ month, ncol = 4) +
  # Create a limit for the x and y axis
  coord_cartesian(xlim = c(55, 97), ylim = c(0.005,0.25)) +
  # Choose a coloring scheme
  scale_fill_viridis_d() +
  theme_bw() +
  # Alter size, color, and look of aesthetics to resemble the reference graph from above
  theme(strip.text = element_text(size = 14),
        axis.title.x = element_text(size = 17), axis.title.y = element_text(size = 15),
        plot.title = element_text(size = 20),
        axis.text.x = element_text(size = 15, color = "gray40"),
        axis.text.y = element_text(size = 15, color = "gray40"),
        axis.ticks = element_line(size = 0.75),
        axis.ticks.length = unit(0.2, "cm"),
        legend.position = "none") +
  # Add labels to graph
  labs(title = "Density plots for each month in 2022", x = "Maximum Temperatures", y = "")
```

![](amato_project_03_files/figure-html/unnamed-chunk-8-1.png)<!-- -->


(d) Generate a plot like the chart below:


<img src="https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/figures/tpa_max_temps_ridges_plasma.png" width="80%" style="display: block; margin: auto;" />

Hint: use the`{ggridges}` package, and the `geom_density_ridges()` function paying close attention to the `quantile_lines` and `quantiles` parameters. The plot above uses the `plasma` option (color scale) for the _viridis_ palette.


``` r
library(ggridges)
```

```
## Warning: package 'ggridges' was built under R version 4.4.3
```

``` r
library(viridis)
```

```
## Warning: package 'viridis' was built under R version 4.4.3
```

```
## Loading required package: viridisLite
```

```
## Warning: package 'viridisLite' was built under R version 4.4.3
```

``` r
# Plot a month vs max temp graph using the geom_density_ridges_graph, filling with the plasma gradient
ggplot(weather_filt, aes(x = max_temp, y = factor(month), fill = stat(x))) +
  geom_density_ridges_gradient(linewidth = 1, quantile_lines = TRUE, quantiles = 0.5,
                               from = 51, to = 101) +
  scale_fill_viridis_c(option = "plasma", name = "") +
  # Create a limit for the x axis
  coord_cartesian(xlim = c(50, 100)) +
  
  theme_ridges() +
  # Alter size, color, and look of aesthetics to resemble the reference graph from above
  theme(axis.title.x = element_text(size = 17),
        axis.text.x = element_text(size = 13, color = "gray40"),
        axis.text.y = element_text(size = 13, color = "gray40", vjust = 0.5),
        legend.key.size = unit(1, 'cm'),
        legend.text = element_text(size = 13)) +
  # Add labels to graph
  labs(x = "Maximum temperature (in Fahrenheit degrees)", y = "")
```

```
## Warning: `stat(x)` was deprecated in ggplot2 3.4.0.
## ℹ Please use `after_stat(x)` instead.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

```
## Picking joint bandwidth of 1.93
```

![](amato_project_03_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


(e) Create a plot of your choice that uses the attribute for precipitation _(values of -99.9 for temperature or -99.99 for precipitation represent missing data)_.


``` r
# Filter the data to get rid of rows having NA or "-99.99" inputs
weather_filt_precip <- weather_clean %>%
  filter(!is.na(max_temp), max_temp != -99.99) %>%
  filter(!is.na(max_temp), min_temp != -99.99) %>%
  filter(!is.na(max_temp), ave_temp != -99.99) %>%
  filter(!is.na(precipitation), precipitation != -99.99) %>%
  group_by(month) %>%
  # Summarize precipitation and calculate average max temp in each month
  summarise(total_precip = sum(precipitation), max_temp_avg = mean(max_temp))

# Plot month vs total precipitation, filling by average max temp
ggplot(weather_filt_precip, aes(x = month, y = total_precip, fill = max_temp_avg)) +
  geom_col() +
  # Create a limit for the y axis
  coord_cartesian(ylim = c(0,12.5)) +
  # Choose a coloring scheme
  scale_fill_viridis_c(option = "plasma") +
  # Make the bar chart lay flat on the 0 tick of the x axis
  scale_y_continuous(expand = expansion(mult = c(0, 0.05))) +
  # Add labels to the graph
  labs(title = "Monthly Precipitation Total and Average Temperature",
       x = "",
       y = "Total Precipitation",
       fill = "Average Max Temp") +
  theme_bw() +
  # Alter size, color, and look of aesthetics to resemble the reference graph from above
  theme(plot.title = element_text(size = 17),
        axis.title.x = element_text(size = 15),
        axis.text.x = element_text(size = 12, color = "gray40"),
        axis.text.y = element_text(size = 12, color = "gray40"),
        legend.key.size = unit(0.75, 'cm'),
        legend.text = element_text(size = 13),
        legend.title = element_text(size = 12)) +
  # Flip the x and y axis for better readability and visibility
  coord_flip()
```

```
## Coordinate system already present. Adding new coordinate system, which will
## replace the existing one.
```

![](amato_project_03_files/figure-html/unnamed-chunk-11-1.png)<!-- -->


## PART 2 

> **You can choose to work on either Option (A) or Option (B)**. Remove from this template the option you decided not to work on. 


### Option (A): Visualizing Text Data

Review the set of slides (and additional resources linked in it) for visualizing text data: Week 6 PowerPoint slides of Visualizing Text Data. 

Choose any dataset with text data, and create at least one visualization with it. For example, you can create a frequency count of most used bigrams, a sentiment analysis of the text data, a network visualization of terms commonly used together, and/or a visualization of a topic modeling approach to the problem of identifying words/documents associated to different topics in the text data you decide to use. 

Make sure to include a copy of the dataset in the `data/` folder, and reference your sources if different from the ones listed below:

- [Billboard Top 100 Lyrics](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/BB_top100_2015.csv)

- [RateMyProfessors comments](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/rmp_wit_comments.csv)

- [FL Poly News Articles](https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/flpoly_news_SP23.csv)


(to get the "raw" data from any of the links listed above, simply click on the `raw` button of the GitHub page and copy the URL to be able to read it in your computer using the `read_csv()` function)


``` r
Top100_lyrics_data <-
read_csv("https://raw.githubusercontent.com/aalhamadani/dataviz_final_project/main/data/BB_top100_2015.csv")
```

```
## Rows: 100 Columns: 6
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (3): Song, Artist, Lyrics
## dbl (3): Rank, Year, Source
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
# filter the data a create a new table that only contains 12 rows of songs
Song_choice <- c("uptown funk", "watch me", "shut up and dance", "sugar", "earned it", "love me like you do", "shake it off", "stiches", "animals", "stay with me", "centuries", "honey im good", "bad blood")
Song_data <- Top100_lyrics_data %>% 
  filter(Song %in% Song_choice)
```


``` r
library(tidytext)
```

```
## Warning: package 'tidytext' was built under R version 4.4.3
```

``` r
# filter the data to ignore filler words and count the total number of each type of word
lyrics_tokens <- Song_data %>%
  unnest_tokens(word, Lyrics) %>%
  anti_join(stop_words, by = "word") %>%
  group_by(Song) %>%
  # Counts how many of each kind of word there are
  count(word, sort = TRUE) %>%
  top_n(7, n) %>%
  ungroup() %>%
  mutate(word = fct_inorder(word))
```


``` r
# Plot a column chart of word vs count
ggplot(lyrics_tokens, aes(x = n, y = fct_rev(word), fill = Song)) +
  geom_col() +
  guides(fill = FALSE) +
  labs(x = NULL, y = NULL) +
  scale_fill_viridis_d() +
  # Create multiple charts instead of plotting all in 1
  facet_wrap(vars(Song), scales = "free_y") +
  theme_minimal() +
  # Add labels to graph
  labs(title = "Most Used Words in Various Songs")
```

```
## Warning: The `<scale>` argument of `guides()` cannot be `FALSE`. Use "none" instead as
## of ggplot2 3.3.4.
## This warning is displayed once every 8 hours.
## Call `lifecycle::last_lifecycle_warnings()` to see where this warning was
## generated.
```

![](amato_project_03_files/figure-html/unnamed-chunk-14-1.png)<!-- -->



