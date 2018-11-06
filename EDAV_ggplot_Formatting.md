GGPlot2 Formatting Walkthrough
================
Jack Gilligan

### 1. Introduction

GGPlot2's theme() element allows you to customize just about every aspect of your plot, from the color of the font to the length of the tick marks. The price for all this customizability is that theme() accepts about a billion arguments as input. Some of these arguments themselves take a set of sub-arguments, which can make learning and using theme() intimidating, or at least annoying.

By walking through the formatting of a geom\_bar plot step-by-step, I hope to introduce you to some aspects of theme() that I've found to be useful, as well as a few other formatting tips and tricks. I've chosen a bar plot to work with, but much of the information here can be applied to other ggplot2 objects as well.

Here are a few helpful resources to have nearby:

1.) <https://ggplot2.tidyverse.org/reference/theme.html>

2.) <http://ggplot.yhathq.com/docs/scale_y_continuous.html>

3.) <https://www.nceas.ucsb.edu/~frazier/RSpatialGuides/colorPaletteCheatsheet.pdf>

### 2. Setup and Basic Formatting

First we'll create a simple dataframe from the built-in 'state' data:

``` r
library(tidyr)
library(tibble)
state_areas <- data_frame(state.name, state.area)
colnames(state_areas) <- c("State","Area")
head(state_areas)
```

    ## # A tibble: 6 x 2
    ##   State        Area
    ##   <chr>       <dbl>
    ## 1 Alabama     51609
    ## 2 Alaska     589757
    ## 3 Arizona    113909
    ## 4 Arkansas    53104
    ## 5 California 158693
    ## 6 Colorado   104247

And make a basic geom\_bar plot:

``` r
library(ggplot2)
plt <- ggplot(state_areas, aes(x=State, y = Area))+
geom_bar(stat = "identity", fill = "red")+
ggtitle("US States by Area")
plt
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-2-1.png" style="display: block; margin: auto;" />

...But it's not exactly publishable: the x-axis values are too bunched up to read, the y-axis values are in scientific notation and don't indicate units, the title is small and off-center. And worst of all, it looks PLAIN.

Before we do any formatting with theme(), let's rearrange our states in order of (decreasing) area, reset the x-axis label which will change as a result of reordering, and set the y-axis label to indicate that our units are square miles.

For more on reordering, see the tutorial at <https://github.com/jtr13/codehelp/blob/master/R/reorder.md>

``` r
library(forcats)
plt <- ggplot(state_areas, aes(x = fct_reorder(State,-Area), y=Area))+
geom_bar(stat = "identity", fill = "red")+
ggtitle("US States by Area")+
xlab("State")+
ylab("Area (sq mi)")
plt
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

### 3. Formatting Using Theme()

Throughout this section, it will be helpful to follow along with the official ggplot2 theme() documentation. It's linked in the Introduction section and again here: <https://ggplot2.tidyverse.org/reference/theme.html>

Moments ago, we decided that the x-axis values were too bunched up to read.

We can access these values through the 'axis.text.x' argument of theme(), which takes an element\_text() object, whose orientation we can specify with the 'angle' sub-argument.

Let's rotate state names so they're vertical; that is, 270 degrees counterclockwise:

``` r
plt2 <- plt + theme(axis.text.x = element_text(angle = 270))
plt2
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

It looks better, but now state names aren't quite lined up with their bars.

We can shift the names over using the 'vjust' sub-argument, which takes values in \[0,1\]. Through trial and error, we find that setting vjust = 0.45 gets everything lined up pretty well:

``` r
plt3 <- plt + theme(axis.text.x = element_text(angle = 270, vjust = 0.45))
plt3
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

While we're at it, let's set 'hjust' = 0 which will "left" align all the state names:

``` r
plt4 <- plt + theme(axis.text.x = element_text(angle = 270, vjust = 0.45, hjust = 0))
plt4
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

And let's drop the font size down from the default (8?) to 7 for a little more room:

``` r
plt5 <- plt + theme(axis.text.x = element_text(angle = 270, vjust = 0.45, hjust = 0, size = 7))
plt5
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-7-1.png" style="display: block; margin: auto;" />

The last thing that really needs to be fixed before tackling the scientific notation in the y-axis values is the title: We will center align it and set the font size to 18:

``` r
plt6 <- plt + theme(
axis.text.x = element_text(angle = 270, vjust = 0.45, hjust = 0, size = 7),
plot.title = element_text(hjust = 0.5, size = 18))
plt6
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

Great! Except for the scientific notation, this plot is clean and readable.

But, we can continue adding theme() arguments to create an appropriately patriotic aesthetic for our US States data. And we will do just that:

``` r
plt7 <- plt + theme(
axis.text.x = element_text(angle = 270, vjust = 0.45, hjust = 0, size = 8, colour = "white"),
title = element_text(family = "serif", colour = "white", size = 15),
plot.title = element_text(hjust = 0.5, size = 24),
axis.text.y = element_text(colour = "white"),
plot.background = element_rect(fill = "navy"),
panel.grid = element_blank(),
panel.background = element_rect(fill = "white"),
axis.ticks = element_line(colour = "white")
)
plt7
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

We can save this theme to a variable to avoid having to type it out (or copy and paste it) multiple times for use in multiple plots.

``` r
My_Theme <- theme(
axis.text.x = element_text(angle = 270, vjust = 0.45, hjust = 0, size = 8, colour = "white"),
title = element_text(family = "serif", colour = "white", size = 15),
plot.title = element_text(hjust = 0.5, size = 24),
axis.text.y = element_text(colour = "white"),
plot.background = element_rect(fill = "navy"),
panel.grid = element_blank(),
panel.background = element_rect(fill = "white"),
axis.ticks = element_line(colour = "white")
)
```

### 4. Formatting with Scale\_\*\_Continuous()

Now let's finally deal with those pesky scientifically-notated y-axis labels.

The solution below applies the format() function to our 'labels' argument within the scale\_y\_continuous function.

``` r
plt8 <- plt + 
My_Theme +
scale_y_continuous(labels = function(x) format(x, scientific = FALSE))
plt8
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-11-1.png" style="display: block; margin: auto;" />

An alternative solution is to replace our original plot 'plt' with one that uses "Area/1000" as its y-aesthetic instead of "Area". Simply dividing by 1000 has the additional benefit of dropping three unsightly zeros from each of our y-axis values:

``` r
plt0 <- ggplot(state_areas, aes(x = fct_reorder(State,-Area), y = Area/1000))+
geom_bar(stat = "identity", fill = "red")+
ggtitle("US States by Area")+
xlab("State")+
ylab("Area [sq mi][000's]")

plt9 <- plt0 +
My_Theme 
plt9
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-12-1.png" style="display: block; margin: auto;" />

The scale\_y\_continuous function gives us complete control over any tickmarks on axes that reflect continuous variables.

We may want tickmarks every 100,000 square miles instead of every 200,000:

``` r
plt10 <- plt0 + 
My_Theme +
scale_y_continuous(breaks=seq(0,600,100))
plt10
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-13-1.png" style="display: block; margin: auto;" />

We may want something slightly more complicated (and not necessarily recommended), like tickmarks evenly spaced every 50,000 square miles until 250,000 (which is about the area of Texas), and then just one more tickmark indicating the exact max area of the dataset (Alaska):

``` r
plt11 <- plt0 + 
My_Theme +
scale_y_continuous(labels = function(x) format(x, scientific = FALSE), breaks=c(seq(0,250,50),
                                                                  as.integer(max(state_areas$Area/1000))))
plt11
```
<img src="https://github.com/JackGilligan/ggplot2-Formatting-Introduction/blob/master/unnamed-chunk-14-1.png" style="display: block; margin: auto;" />
