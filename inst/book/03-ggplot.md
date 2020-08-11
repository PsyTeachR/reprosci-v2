# Data Visualisation {#ggplot}

<img src="images/memes/better_graphs.png" class="meme right">

Take the [quiz](#ggplot-quiz) to see if you need to review this chapter.

## Learning Objectives

### Basic

1. Understand what types of graphs are best for [different types of data](#vartypes)
    + 1 discrete
    + 1 continuous
    + 2 discrete
    + 2 continuous
    + 1 discrete, 1 continuous
    + 3 continuous
2. Create common types of graphs with ggplot2
    + [`geom_bar()`](#geom_bar)
    + [`geom_density()`](#geom_density)
    + [`geom_freqpoly()`](#geom_freqpoly)
    + [`geom_histogram()`](#geom_histogram)
    + [`geom_violin()`](#geom_violin)
    + [`geom_boxplot()`](#geom_boxplot)
    + [`geom_col()`](#geom_col)
    + [`geom_point()`](#geom_point)
    + [`geom_smooth()`](#geom_smooth)
3. Set custom [labels](#custom-labels) and [colours](#custom-colours)
4. Represent factorial designs with different colours or facets
5. [Save plots](#ggsave) as an image file
    
### Intermediate

6. Superimpose different types of graphs
7. Add lines to graphs
8. Deal with [overlapping data](#overlap)
9. Create less common types of graphs
    + [`geom_tile()`](#geom_tile)
    + [`geom_density2d()`](#geom_density2d)
    + [`geom_bin2d()`](#geom_bin2d)
    + [`geom_hex()`](#geom_hex)
    + [`geom_count()`](#geom_count)

### Advanced

10. Arrange plots in a grid using [`cowplot`](#cowplot)
11. Adjust axes (e.g., flip coordinates, set axis limits)
12. Change the [theme](#theme)
13. Create interactive graphs with [`plotly`](#plotly)


## Resources

* [Look at Data](http://socviz.co/look-at-data.html) from [Data Vizualization for Social Science](http://socviz.co/)
* [Chapter 3: Data Visualisation](http://r4ds.had.co.nz/data-visualisation.html) of *R for Data Science*
* [Chapter 28: Graphics for communication](http://r4ds.had.co.nz/graphics-for-communication.html) of *R for Data Science*
* [Graphs](http://www.cookbook-r.com/Graphs) in *Cookbook for R*
* [ggplot2 cheat sheet](https://github.com/rstudio/cheatsheets/raw/master/data-visualization-2.1.pdf)
* [ggplot2 documentation](https://ggplot2.tidyverse.org/reference/)
* [The R Graph Gallery](http://www.r-graph-gallery.com/) (this is really useful)
* [Top 50 ggplot2 Visualizations](http://r-statistics.co/Top50-Ggplot2-Visualizations-MasterList-R-Code.html)
* [R Graphics Cookbook](http://www.cookbook-r.com/Graphs/) by Winston Chang
* [ggplot extensions](https://www.ggplot2-exts.org/)
* [plotly](https://plot.ly/ggplot2/) for creating interactive graphs


[Stub for this lesson](stubs/3_viz.Rmd)

## Setup


```r
# libraries needed for these graphs
library(tidyverse)
library(plotly)
library(cowplot) 
set.seed(30250) # makes sure random numbers are reproducible
```

## Common Variable Combinations {#vartypes}

**Continuous** variables are properties you can measure, like height. **Discrete** (or categorical) variables are things you can count, like the number of pets you have. Categorical variables can be **nominal**, where the categories don't really have an order, like cats, dogs and ferrets (even though ferrets are obviously best). They can also be **ordinal**, where there is a clear order, but the distance between the categories isn't something you could exactly equate, like points on a Likert rating scale.

Different types of visualisations are good for different types of variables. 

<div class="try">
<p>Before you read ahead, come up with an example of each type of variable combination and sketch the types of graphs that would best display these data.</p>
<ul>
<li>1 discrete</li>
<li>1 continuous</li>
<li>2 discrete</li>
<li>2 continuous</li>
<li>1 discrete, 1 continuous</li>
<li>3 continuous</li>
</ul>
</div>

### Data

The code below creates some data frames with different types of data. We'll learn how to simulate data like this in the [Probability & Simulation](#sim) chapter, but for now just run the code chunk below.

* `pets` has a column with pet type
* `pet_happy` has `happiness` and `age` for 500 dog owners and 500 cat owners
* `x_vs_y` has two correlated continuous variables (`x` and `y`)
* `overlap` has two correlated ordinal variables and 1000 observations so there is a lot of overlap
* `overplot` has two correlated continuous variables and 10000 observations

<div class="try">
<p>First, think about what kinds of graphs are best for representing these different types of data.</p>
</div>


```r
pets <- tibble(
  pet = sample(
    c("dog", "cat", "ferret", "bird", "fish"), 
    100, 
    TRUE, 
    c(0.45, 0.40, 0.05, 0.05, 0.05)
  )
)

pet_happy <- tibble(
  pet = rep(c("dog", "cat"), each = 500),
  happiness = c(rnorm(500, 55, 10), rnorm(500, 45, 10)),
  age = rpois(1000, 3) + 20
)

x_vs_y <- tibble(
  x = rnorm(100),
  y = x + rnorm(100, 0, 0.5)
)

overlap <- tibble(
  x = rbinom(1000, 10, 0.5),
  y = x + rbinom(1000, 20, 0.5)
)

overplot <- tibble(
  x = rnorm(10000),
  y = x + rnorm(10000, 0, 0.5)
)
```



## Basic Plots

### Bar plot {#geom_bar}

Bar plots are good for categorical data where you want to represent the count.


```r
ggplot(pets, aes(pet)) +
  geom_bar()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/barplot-1.png" alt="Bar plot" width="100%" />
<p class="caption">(\#fig:barplot)Bar plot</p>
</div>

### Density plot {#geom_density}

Density plots are good for one continuous variable, but only if you have a fairly 
large number of observations.


```r
ggplot(pet_happy, aes(happiness)) +
  geom_density()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/density-1.png" alt="Density plot" width="100%" />
<p class="caption">(\#fig:density)Density plot</p>
</div>

You can represent subsets of a variable by assigning the category variable to the argument `group`, `fill`, or `color`. 


```r
ggplot(pet_happy, aes(happiness, fill = pet)) +
  geom_density(alpha = 0.5)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/density-grouped-1.png" alt="Grouped density plot" width="100%" />
<p class="caption">(\#fig:density-grouped)Grouped density plot</p>
</div>

<div class="try">
<p>Try changing the <code>alpha</code> argument to figure out what it does.</p>
</div>

### Frequency Polygons {#geom_freqpoly}

If you don't want smoothed distributions, try `geom_freqpoly()`.


```r
ggplot(pet_happy, aes(happiness, color = pet)) +
  geom_freqpoly(binwidth = 1)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/freqpoly-1.png" alt="Frequency ploygon plot" width="100%" />
<p class="caption">(\#fig:freqpoly)Frequency ploygon plot</p>
</div>

<div class="try">
<p>Try changing the <code>binwidth</code> argument to 5 and 0.1. How do you figure out the right value?</p>
</div>

### Histogram {#geom_histogram}

Histograms are also good for one continuous variable, and work well if you don't have many observations. Set the `binwidth` to control how wide each bar is.


```r
ggplot(pet_happy, aes(happiness)) +
  geom_histogram(binwidth = 1, fill = "white", color = "black")
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/histogram-1.png" alt="Histogram" width="100%" />
<p class="caption">(\#fig:histogram)Histogram</p>
</div>

<div class="info">
<p>Histograms in ggplot look pretty bad unless you set the <code>fill</code> and <code>color</code>.</p>
</div>

If you show grouped histograms, you also probably want to change the default `position` argument.


```r
ggplot(pet_happy, aes(happiness, fill=pet)) +
  geom_histogram(binwidth = 1, alpha = 0.5, position = "dodge")
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/histogram-grouped-1.png" alt="Grouped Histogram" width="100%" />
<p class="caption">(\#fig:histogram-grouped)Grouped Histogram</p>
</div>

<div class="try">
<p>Try changing the <code>position</code> argument to “identity”, “fill”, “dodge”, or “stack”.</p>
</div>

### Column plot {#geom_col}

Column plots are the worst way to represent grouped continuous data, but also one of the most common.

To make column plots with error bars, you first need to calculate the means, error bar uper limits (`ymax`) and error bar lower limits (`ymin`) for each category. You'll learn more about how to use the code below in the next two lessons.


```r
# calculate mean and SD for each pet
avg_pet_happy <- pet_happy %>%
  group_by(pet) %>%
  summarise(
    mean = mean(happiness),
    sd = sd(happiness)
  )
```

```
## `summarise()` ungrouping output (override with `.groups` argument)
```

```r
ggplot(avg_pet_happy, aes(pet, mean, fill=pet)) +
  geom_col(alpha = 0.5) +
  geom_errorbar(aes(ymin = mean - sd, ymax = mean + sd), width = 0.25) +
  geom_hline(yintercept = 40)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/colplot-1.png" alt="Column plot" width="100%" />
<p class="caption">(\#fig:colplot)Column plot</p>
</div>

<div class="try">
<p>What do you think <code>geom_hline()</code> does?</p>
</div>

### Boxplot {#geom_boxplot}

Boxplots are great for representing the distribution of grouped continuous 
variables. They fix most of the problems with using barplots for continuous data.


```r
ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_boxplot(alpha = 0.5)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/boxplot-1.png" alt="Box plot" width="100%" />
<p class="caption">(\#fig:boxplot)Box plot</p>
</div>

### Violin plot {#geom_violin}

Violin pots are like sideways, mirrored density plots. They give even more information than a boxplot about distribution and are especially useful when you have non-normal distributions.


```r
ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_violin(
    trim = FALSE,
    draw_quantiles = c(0.25, 0.5, 0.75), 
    alpha = 0.5
  )
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/violin-1.png" alt="Violin plot" width="100%" />
<p class="caption">(\#fig:violin)Violin plot</p>
</div>

<div class="try">
<p>Try changing the numbers in the <code>draw_quantiles</code> argument.</p>
</div>

### Scatter plot {#geom_point}

Scatter plots are a good way to represent the relationship between two continuous variables.


```r
ggplot(x_vs_y, aes(x, y)) +
  geom_point()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/scatter-1.png" alt="Scatter plot using geom_point()" width="100%" />
<p class="caption">(\#fig:scatter)Scatter plot using geom_point()</p>
</div>

### Line graph {#geom_smooth}

You often want to represent the relationship as a single line.


```r
ggplot(x_vs_y, aes(x, y)) +
  geom_smooth(method="lm")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/line-1.png" alt="Line plot using geom_smooth()" width="100%" />
<p class="caption">(\#fig:line)Line plot using geom_smooth()</p>
</div>

## Customisation

### Labels {#custom-labels}

You can set custom titles and axis labels in a few different ways.


```r
ggplot(x_vs_y, aes(x, y)) +
  geom_smooth(method="lm") +
  ggtitle("My Plot Title") +
  xlab("The X Variable") +
  ylab("The Y Variable")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/line-labels1-1.png" alt="Set custom labels with ggtitle(), xlab() and ylab()" width="100%" />
<p class="caption">(\#fig:line-labels1)Set custom labels with ggtitle(), xlab() and ylab()</p>
</div>


```r
ggplot(x_vs_y, aes(x, y)) +
  geom_smooth(method="lm") +
  labs(title = "My Plot Title",
       x = "The X Variable",
       y = "The Y Variable")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/line-labels2-1.png" alt="Set custom labels with labs()" width="100%" />
<p class="caption">(\#fig:line-labels2)Set custom labels with labs()</p>
</div>

### Colours {#custom-colours}

You can set custom values for colour and fill using functions like `scale_colour_manual()` and `scale_fill_manual()`. The [Colours chapter in Cookbook for R](http://www.cookbook-r.com/Graphs/Colors_(ggplot2)/) has many more ways to customise colour.


```r
ggplot(pet_happy, aes(pet, happiness, colour = pet, fill = pet)) +
  geom_violin() +
  scale_color_manual(values = c("darkgreen", "dodgerblue")) +
  scale_fill_manual(values = c("#CCFFCC", "#BBDDFF"))
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/line-labels-1.png" alt="Set custom colour" width="100%" />
<p class="caption">(\#fig:line-labels)Set custom colour</p>
</div>
 
### Save as File {#ggsave}

You can save a ggplot using `ggsave()`. It saves the last ggplot you made, 
by default, but you can specify which plot you want to save if you assigned that plot to a variable.

You can set the `width` and `height` of your plot. The default units are inches, but you can change the `units` argument to "in", "cm", or "mm".



```r
box <- ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_boxplot(alpha = 0.5)

violin <- ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_violin(alpha = 0.5)

ggsave("demog_violin_plot.png", width = 5, height = 7)

ggsave("demog_box_plot.jpg", plot = box, width = 5, height = 7)
```


## Combination Plots

### Violinbox plot

To demonstrate the use of `facet_grid()` for factorial designs, we create a new column called `agegroup` to split the data into participants older than the meadian age or younger than the median age. New factors will display in alphabetical order, so we can use the `factor()` function to set the levels in the order we want.


```r
pet_happy %>%
  mutate(agegroup = ifelse(age<median(age), "Younger", "Older"),
         agegroup = factor(agegroup, levels = c("Younger", "Older"))) %>%
  ggplot(aes(pet, happiness, fill=pet)) +
    geom_violin(trim = FALSE, alpha=0.5, show.legend = FALSE) +
    geom_boxplot(width = 0.25, fill="white") +
    facet_grid(.~agegroup) +
    scale_fill_manual(values = c("orange", "green"))
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/violinbox-1.png" alt="Violin-box plot" width="100%" />
<p class="caption">(\#fig:violinbox)Violin-box plot</p>
</div>

<div class="info">
<p>Set the <code>show.legend</code> argument to <code>FALSE</code> to hide the legend. We do this here because the x-axis already labels the pet types.</p>
</div>

### Violin-point-range plot

You can use `stat_summary()` to superimpose a point-range plot showning the mean ± 1 SD. You'll learn how to write your own functions in the lesson on [Iteration and Functions](#func).


```r
ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_violin(
    trim = FALSE,
    alpha = 0.5
  ) +
  stat_summary(
    fun.y = mean,
    fun.ymax = function(x) {mean(x) + sd(x)},
    fun.ymin = function(x) {mean(x) - sd(x)},
    geom="pointrange"
  )
```

```
## Warning: `fun.y` is deprecated. Use `fun` instead.
```

```
## Warning: `fun.ymin` is deprecated. Use `fun.min` instead.
```

```
## Warning: `fun.ymax` is deprecated. Use `fun.max` instead.
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/stat-summary-1.png" alt="Point-range plot using stat_summary()" width="100%" />
<p class="caption">(\#fig:stat-summary)Point-range plot using stat_summary()</p>
</div>

### Violin-jitter plot

If you don't have a lot of data points, it's good to represent them individually. You can use `geom_jitter` to do this.


```r
pet_happy %>%
  sample_n(50) %>%  # choose 50 random observations from the dataset
  ggplot(aes(pet, happiness, fill=pet)) +
  geom_violin(
    trim = FALSE,
    draw_quantiles = c(0.25, 0.5, 0.75), 
    alpha = 0.5
  ) + 
  geom_jitter(
    width = 0.15, # points spread out over 15% of available width
    height = 0, # do not move position on the y-axis
    alpha = 0.5, 
    size = 3
  )
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/violin-jitter-1.png" alt="Violin-jitter plot" width="100%" />
<p class="caption">(\#fig:violin-jitter)Violin-jitter plot</p>
</div>

### Scatter-line graph

If your graph isn't too complicated, it's good to also show the individual data points behind the line.


```r
ggplot(x_vs_y, aes(x, y)) +
  geom_point(alpha = 0.25) +
  geom_smooth(method="lm")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/scatter-line-1.png" alt="Scatter-line plot" width="100%" />
<p class="caption">(\#fig:scatter-line)Scatter-line plot</p>
</div>

### Grid of plots {#cowplot}

You can use the [ `cowplot`](https://cran.r-project.org/web/packages/cowplot/vignettes/introduction.html) package to easily make grids of different graphs. First, you have to assign each plot a name. Then you list all the plots as the first arguments of `plot_grid()` and provide a list of labels.


```r
my_hist <- ggplot(pet_happy, aes(happiness, fill=pet)) +
  geom_histogram(
    binwidth = 1, 
    alpha = 0.5, 
    position = "dodge", 
    show.legend = FALSE
  )

my_violin <- ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_violin(
    trim = FALSE,
    draw_quantiles = c(0.5), 
    alpha = 0.5, 
    show.legend = FALSE
  )

my_box <- ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_boxplot(alpha=0.5, show.legend = FALSE)

my_density <- ggplot(pet_happy, aes(happiness, fill=pet)) +
  geom_density(alpha=0.5, show.legend = FALSE)

my_bar <- pet_happy %>%
  group_by(pet) %>%
  summarise(
    mean = mean(happiness),
    sd = sd(happiness)
  ) %>%
  ggplot(aes(pet, mean, fill=pet)) +
    geom_bar(stat="identity", alpha = 0.5, 
             color = "black", show.legend = FALSE) +
    geom_errorbar(aes(ymin = mean - sd, ymax = mean + sd), width = 0.25)

plot_grid(
  my_violin, 
  my_box, 
  my_density, 
  my_bar, 
  labels = c("A", "B", "C", "D")
)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/cowplot-1.png" alt="Grid of plots using cowplot" width="100%" />
<p class="caption">(\#fig:cowplot)Grid of plots using cowplot</p>
</div>


## Overlapping Discrete Data {#overlap}

### Reducing Opacity 

You can deal with overlapping data points (very common if you're using Likert scales) by reducing the opacity of the points. You need to use trial and error to adjust these so they look right.


```r
ggplot(overlap, aes(x, y)) +
  geom_point(size = 5, alpha = .05) +
  geom_smooth(method="lm")
```

```
## `geom_smooth()` using formula 'y ~ x'
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/overlap-alpha-1.png" alt="Deal with overlapping data using transparency" width="100%" />
<p class="caption">(\#fig:overlap-alpha)Deal with overlapping data using transparency</p>
</div>

### Proportional Dot Plots {#geom_count}

Or you can set the size of the dot proportional to the number of overlapping observations using `geom_count()`.


```r
overlap %>%
  ggplot(aes(x, y)) +
  geom_count(color = "#663399")
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/overlap-size-1.png" alt="Deal with overlapping data using geom_count()" width="100%" />
<p class="caption">(\#fig:overlap-size)Deal with overlapping data using geom_count()</p>
</div>

Alternatively, you can transform your data to create a count column and use the count to set the dot colour.


```r
overlap %>%
  group_by(x, y) %>%
  summarise(count = n()) %>%
  ggplot(aes(x, y, color=count)) +
  geom_point(size = 5) +
  scale_color_viridis_c()
```

```
## `summarise()` regrouping output by 'x' (override with `.groups` argument)
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/overlap-colour-1.png" alt="Deal with overlapping data using dot colour" width="100%" />
<p class="caption">(\#fig:overlap-colour)Deal with overlapping data using dot colour</p>
</div>


<div class="info">
<p>The <a href="https://cran.r-project.org/web/packages/viridis/vignettes/intro-to-viridis.html">viridis package</a> changes the colour themes to be easier to read by people with colourblindness and to print better in greyscale. Viridis is built into <code>ggplot2</code> since v3.0.0. It uses <code>scale_colour_viridis_c()</code> and <code>scale_fill_viridis_c()</code> for continuous variables and <code>scale_colour_viridis_d()</code> and <code>scale_fill_viridis_d()</code> for discrete variables.</p>
</div>

## Overlapping Continuous Data

Even if the variables are continuous, overplotting might obscure any relationships if you have lots of data.


```r
overplot %>%
  ggplot(aes(x, y)) + 
  geom_point()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/overplot-point-1.png" alt="Overplotted data" width="100%" />
<p class="caption">(\#fig:overplot-point)Overplotted data</p>
</div>

### 2D Density Plot {#geom_density2d}
Use `geom_density2d()` to create a contour map.


```r
overplot %>%
  ggplot(aes(x, y)) + 
  geom_density2d()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/density2d-1.png" alt="Contour map with geom_density2d()" width="100%" />
<p class="caption">(\#fig:density2d)Contour map with geom_density2d()</p>
</div>

You can use `stat_density_2d(aes(fill = ..level..), geom = "polygon")` to create a heatmap-style density plot. 


```r
overplot %>%
  ggplot(aes(x, y)) + 
  stat_density_2d(aes(fill = ..level..), geom = "polygon") +
  scale_fill_viridis_c()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/density2d-fill-1.png" alt="Heatmap-density plot" width="100%" />
<p class="caption">(\#fig:density2d-fill)Heatmap-density plot</p>
</div>


### 2D Histogram {#geom_bin2d}

Use `geom_bin2d()` to create a rectangular heatmap of bin counts. Set the `binwidth` to the x and y dimensions to capture in each box.


```r
overplot %>%
  ggplot(aes(x, y)) + 
  geom_bin2d(binwidth = c(1,1))
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/bin2d-1.png" alt="Heatmap of bin counts" width="100%" />
<p class="caption">(\#fig:bin2d)Heatmap of bin counts</p>
</div>

### Hexagonal Heatmap {#geom_hex}

Use `geomhex()` to create a hexagonal heatmap of bin counts. Adjust the `binwidth`, `xlim()`, `ylim()` and/or the figure dimensions to make the hexagons more or less stretched.


```r
overplot %>%
  ggplot(aes(x, y)) + 
  geom_hex(binwidth = c(0.25, 0.25))
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/overplot-hex-1.png" alt="Hexagonal heatmap of bin counts" width="100%" />
<p class="caption">(\#fig:overplot-hex)Hexagonal heatmap of bin counts</p>
</div>

### Correlation Heatmap {#geom_tile}

I've included the code for creating a correlation matrix from a table of variables, but you don't need to understand how this is done yet. We'll cover `mutate()` and `gather()` functions in the [dplyr](#dplyr) and [tidyr](#tidyr) lessons.


```r
# generate two sets of correlated variables (a and b)
heatmap <- tibble(
    a1 = rnorm(100),
    b1 = rnorm(100)
  ) %>% 
  mutate(
    a2 = a1 + rnorm(100),
    a3 = a1 + rnorm(100),
    a4 = a1 + rnorm(100),
    b2 = b1 + rnorm(100),
    b3 = b1 + rnorm(100),
    b4 = b1 + rnorm(100)
  ) %>%
  cor() %>% # create the correlation matrix
  as.data.frame() %>% # make it a data frame
  rownames_to_column(var = "V1") %>% # set rownames as V1
  gather("V2", "r", a1:b4) # wide to long (V2)
```

Once you have a correlation matrix in the correct (long) format, it's easy to make a heatmap using `geom_tile()`.


```r
ggplot(heatmap, aes(V1, V2, fill=r)) +
  geom_tile() +
  scale_fill_viridis_c()
```

<div class="figure" style="text-align: center">
<img src="03-ggplot_files/figure-html/heatmap-1.png" alt="Heatmap using geom_tile()" width="100%" />
<p class="caption">(\#fig:heatmap)Heatmap using geom_tile()</p>
</div>

<div class="info">
<p>The file type is set from the filename suffix, or by specifying the argument <code>device</code>, which can take the following values: “eps”, “ps”, “tex”, “pdf”, “jpeg”, “tiff”, “png”, “bmp”, “svg” or “wmf”.</p>
</div>

## Interactive Plots {#plotly}

You can use the `plotly` package to make interactive graphs. Just assign your ggplot to a variable and use the function `ggplotly()`.


```r
demog_plot <- ggplot(pet_happy, aes(pet, happiness, fill=pet)) +
  geom_point(position = position_jitter(width= 0.2, height = 0), size = 2)

ggplotly(demog_plot)
```

<div class="figure" style="text-align: center">
<!--html_preserve--><div id="htmlwidget-4c31b190c41ec6fb0a4f" style="width:100%;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-4c31b190c41ec6fb0a4f">{"x":{"data":[{"x":[1.17452184921131,0.837392969522625,0.905814275890589,1.17603061404079,1.06399660427123,1.19148589177057,1.17920320238918,0.911308425571769,0.933239712845534,1.19564946824685,1.10091838724911,0.961731719598174,1.16888038525358,1.00488662756979,0.97376678371802,1.00983182322234,1.05865497374907,1.19923756504431,0.859491002652794,0.95817330237478,1.04952964875847,0.896694375388324,1.19732142640278,1.10532175721601,1.0921706370078,0.9330322554335,1.03776726974174,1.11706803590059,0.80386843373999,0.944613048899919,0.849900049250573,0.904297821130604,1.18659185664728,1.01274114213884,0.852002772130072,1.0791059916839,0.819313253369182,1.05473181558773,0.886158769018948,1.13027543285862,1.02845658976585,1.19133354667574,1.18423379352316,1.090532935597,1.08270872924477,1.07712611099705,1.07152436748147,1.08742496166378,0.873177727404982,1.09052714379504,0.837851782795042,1.04022816615179,1.00628880802542,0.989441099204123,0.806117073073983,1.08491637594998,0.838936586305499,0.858479010686278,1.01898338207975,1.02901530088857,0.963741565868258,1.10612689275295,1.02746714409441,0.917606882285327,1.0348621416837,1.16232283739373,1.14681664668024,0.90108893821016,0.802480209432542,0.980603049043566,1.19328970815986,1.04137543896213,1.16044529182836,0.978642112016678,0.928519605565816,1.08064135024324,0.964845069125295,1.17446930836886,1.10916302502155,1.07309442656115,1.12410474997014,0.92745480844751,0.849349620752037,1.10511435521767,0.89992655524984,1.12468348806724,0.887641648575664,0.813206388615072,0.96261520665139,1.04726700065657,1.1376841683872,0.987964095640928,1.16658213343471,1.03438970092684,0.90057110125199,1.15804974567145,0.981896292697638,0.912617485877126,0.935830287821591,0.888920156285167,1.01208766242489,1.11283339085057,0.914509753044695,0.882533473335206,1.16949789840728,1.12149295574054,0.893248085770756,1.01055671349168,1.06899749245495,1.1902685303241,1.19544829819351,0.807432227488607,1.05298544783145,1.18525361409411,1.1150719538331,0.822304684389383,0.836154614761472,0.881720071844757,0.910903716832399,1.01159141436219,1.17358987750486,1.19503754861653,1.1847807568498,0.966505148634315,1.14519168082625,1.09176739063114,0.920686389040202,1.07307953191921,0.925911375973374,1.15396664887667,0.900695951096714,0.966136521566659,1.1964347044006,0.854547574557364,0.877705362066627,0.966631484031677,0.959361515846103,0.856696899514645,1.02140615284443,1.1621406218037,0.956498855724931,0.944790158048272,1.07718404261395,1.06050356505439,0.901468699332327,0.953298586048186,0.895509597472847,1.06976024871692,0.904732028767467,0.899241095222533,0.852457854524255,0.902579683158547,1.18375984039158,1.14026248212904,0.980428967718035,1.12725893938914,0.994069290347397,1.02757326001301,1.15898904306814,0.910407617222518,1.1498492336832,0.953533592447638,1.09588589062914,1.16285354206339,0.980741338245571,1.04569627912715,0.93662622384727,0.819276711065322,1.05716826561838,1.03085501873866,1.08602011399344,0.804730991832912,0.867018896713853,1.152042805776,0.845947748143226,1.15389228267595,1.13780441591516,0.87043061023578,0.984027555771172,0.825293015222996,1.00462726987898,0.89395264564082,1.0907102573663,1.06625105729327,1.1772767833434,1.06297184498981,0.936787573527545,0.922777329012752,1.14463825374842,0.94824374159798,0.8384925827384,0.893286917544901,0.904645341355354,0.97619611248374,1.08622083440423,0.803917380142957,1.19916585851461,0.954245730116963,1.10725747281685,1.19716189531609,1.14582586083561,0.916660629399121,1.1368699288927,1.17912822803482,1.0057316865772,0.854963833838701,1.1594622887671,0.806433281768113,1.14760988559574,0.81629078220576,0.826151624321938,1.19519459158182,1.19240908082575,0.901444539986551,0.965389076247811,1.14881057403982,1.14843342518434,0.898533318005502,0.855346039310098,0.819527762103826,1.07838226137683,1.04186380235478,1.04024754976854,0.879626875557005,0.80125471604988,1.08838309934363,1.11685905167833,0.818468801956624,1.01295253550634,1.1891007472761,0.925725820939988,1.09710546731949,0.919848992489278,1.13865818884224,1.02542556868866,1.06327793011442,1.12969961324707,0.894165477156639,0.97433049287647,1.07440559612587,0.855816275812685,0.819635688420385,0.803207296784967,0.86339283650741,0.904129557032138,1.01715556550771,0.981613632105291,0.934521590452641,0.967927084676921,0.825593119673431,1.10079554580152,1.0305653963238,0.808806460816413,1.06317361360416,1.14253387423232,0.896974500734359,1.18155093146488,1.11566849444062,1.11505094757304,1.02148600295186,0.841348743438721,0.90979285761714,0.855151577852666,1.00261047706008,1.11620660144836,1.07744874693453,1.13002805085853,1.16549084214494,0.914837150275707,0.898315011058003,0.968368661589921,1.19927680492401,0.988023428898305,0.85869075069204,1.17346168374643,1.13425558833405,0.913819495402276,1.10636317217723,0.928106836043298,1.12826983109117,0.838019842840731,1.17772652348503,0.818329690676183,0.96744732260704,0.99450448891148,0.823589649423957,0.841844086907804,1.1422526515089,1.11426277607679,1.03003607541323,1.12236408442259,1.05110738743097,0.869548144284636,0.837230512220412,0.908891339134425,0.918172407709062,1.05159986177459,1.02958529489115,1.05387021470815,1.13590090395883,0.926470057852566,0.995532422885299,0.917859650310129,1.19574748706073,0.83914063218981,0.928885972779244,1.15651520667598,0.809253922756761,0.9788912714459,0.952708604745567,1.00061652334407,0.935328164882958,0.802468546014279,0.863668260257691,0.935106602311134,1.18208759119734,0.84948884434998,0.991729240585119,1.04867984270677,1.11973576862365,1.00375055810437,0.870961654093117,0.836091303545982,1.19102377910167,1.07485160203651,0.864029231760651,1.14457205915824,1.08875952251256,1.1549452806823,1.00111931478605,0.844969057757407,1.09813287341967,1.07801315793768,1.09485475048423,1.08371118670329,0.999471561424434,1.16661220574752,0.941657193377614,0.877987603470683,1.07405744688585,1.03133632326499,0.907658385019749,0.90143729550764,0.941664517298341,1.00064666653052,0.834613969735801,1.18293746132404,0.869918167125434,0.950902169756591,0.809350032918155,0.894209135510027,0.932553817983717,1.02132386639714,1.03734010467306,1.17234055176377,1.00794950183481,0.897212249971926,1.13298930907622,1.11441882066429,1.00806907406077,1.18001199420542,1.0930301964283,1.05932472273707,0.90015845829621,0.958556296862662,0.90539369136095,0.87240622183308,1.18968193810433,0.917438779957592,0.941483583860099,0.90056847948581,0.855896949581802,1.18068994916975,0.930650622025132,1.08264529583976,1.06334912776947,1.12029705876485,1.0811351983808,0.898422227427363,0.841073028184473,1.06944860043004,1.15792481591925,1.03666441226378,0.992357381060719,0.875801081862301,0.852626261767,0.978128857910633,0.837614517193288,0.949332430399954,1.16558353435248,1.0126188904047,1.08923631655052,1.04371541952714,0.841600873880088,0.956886579468846,1.05787845849991,1.13499501459301,1.0419508879073,0.945480002928525,1.02668965971097,0.989601548574865,1.16198412058875,1.02985834013671,1.05449571320787,1.17981827054173,0.931995669566095,1.14792818259448,1.1837581763044,1.0120801942423,0.974913143180311,0.986239729262888,0.967006974853575,1.13400924904272,1.16337819239125,0.841830900683999,1.13962996676564,0.96781505709514,0.938563511054963,1.19282621815801,1.074407022167,0.930004276242107,0.913656750228256,0.995935286767781,1.11908872509375,0.813008305430412,1.06393994009122,0.950155038759112,1.16719435090199,1.10140747660771,0.975821580830961,0.925624657608569,1.12066937163472,0.982279663067311,0.854851674009115,0.923987327702343,0.995076035987586,1.04820457994938,1.02024749899283,1.17619599308819,0.838408179208636,0.885320764407516,1.16166508663446,0.856488448008895,1.13907782798633,1.0728785562329,0.995918593741953,1.06068469583988,0.98444636752829,0.841567925922573,1.05900946669281,1.05735875042155,1.0983686292544,0.89420562190935,1.04377389829606,1.18027605134994,0.896383188571781,1.01093572145328,1.03762843487784,0.942212487850338,0.914782807696611,1.05902477912605,0.966447673458606,1.10561721315607,1.06123613202944,1.16810846105218,0.835277646966279,1.16060607116669,1.12679059142247,0.818742474354804,1.10123921195045,0.840611314214766,1.01876603942364,1.08636336000636,1.06636048853397,0.814960765466094,0.876002143230289,1.03393589360639,0.810450210236013,0.883896442968398,1.10377255044878,1.07770517868921,0.995453323516995,0.926408224832267,0.928707143850625,0.944170989561826,0.940089053846896,0.830696113128215,1.09167981911451,0.872647846862674,0.932172240037471,1.0046180752106,0.923874622397125,0.932056796457619,0.959088757261634,1.18006232110783,0.961586723756045,1.03831615094095,0.827355644386262,1.08551710937172,1.19183829696849],"y":[35.3811778980652,44.8219715184304,39.2173032467429,51.0467175864377,57.065646571496,59.2583745783749,38.6269458335796,44.9047226313368,42.2549963304467,51.2502565642543,48.108659653327,45.0530525900592,42.7519932548031,50.7436497539717,54.5121505271154,31.8709522079484,35.8687235248078,50.8435780336784,25.3243994804453,57.9421212366076,42.9896341882336,44.1665187644521,31.3466674778537,45.8155917365736,47.1046667065194,41.1843045093704,43.0071088046759,42.6812983244253,41.2709028908419,31.8879943187694,31.9900716201833,51.1946790287161,54.1810269202146,56.6080142387776,31.0677757711284,54.4491498578571,47.3547547403568,56.1066379713011,59.7671752409216,33.8795865456588,52.500719786526,29.9646627974749,47.012457047925,61.1665444430676,37.415322174558,52.9711878087744,45.4632050151875,54.750585778299,34.586532194498,38.86011814353,58.0090077428656,35.8346878673852,41.5761912788066,29.3179361524087,60.0591684226618,55.1100335513044,59.0468217286712,44.0808285742799,47.4248902288718,46.8370364843163,68.6455901669851,44.7948606685633,51.776737329203,51.8210149729511,55.053580747997,46.4924477272146,32.6734009122175,39.1397096449609,47.1327113858191,39.3979003349048,49.2507706945753,40.1897944468376,31.6247393953437,40.2206619499785,62.1275164919012,35.2523658671875,66.7972824150252,52.5748571302188,49.1931598376863,49.5967865549996,53.1517354289546,58.4049164249284,59.7281402911733,57.1244954910085,55.5063725037733,70.498489544224,54.5679338659033,48.0452763746218,38.5027470191609,50.1325067716551,40.8909047205347,42.6637379981198,48.4916093586211,37.7017322967972,51.8699832698518,50.4443437912629,31.4504167390183,39.6232672294671,54.3183894372092,28.6860699565753,41.9410475635885,54.4813905787944,54.1113121661233,40.6246000133681,44.6281507228987,48.7287664689397,56.7567621607922,37.4859100119033,48.1239033896428,55.0533154143494,46.8802605332637,32.8610509760892,42.823883779389,59.4918958289697,64.9329092736719,39.0342870555263,25.0669689350711,36.1643610560199,54.1174389513585,51.9495317975065,46.2366885494556,49.587946549363,33.8384582638267,47.2061675037226,35.8788597847467,34.2114296225947,42.2958078886889,55.0481494299282,59.978749783638,28.5899161318859,52.2724600667564,46.8326576272608,71.2403290629513,39.2520466143459,52.3709259309121,56.8035035451839,52.0185899828462,25.7084260164093,58.2695765747047,30.2006472535596,58.6398099719759,48.5869282191415,68.9993089003824,44.1224401277201,51.6352379677984,68.1256644206222,27.5178283194337,42.0105894924589,34.9021605197341,33.9203369865282,47.7042043604661,43.0288296782435,47.0833880103838,53.7936520596596,32.6073505441537,60.8511638416271,61.6304037565955,41.7514365231247,57.8235421105175,46.6830805527213,44.8178276627585,56.0079950109924,55.6178165342756,29.8731706855415,55.8035286252884,36.067018332602,36.7166966248736,55.6039159953624,35.3682104344071,56.643538290697,39.6744681231543,49.3513315141119,37.8579810238087,31.1210086179983,34.8001389250396,57.6674665292238,59.2590003690604,38.0976018359415,35.9237616430318,40.5600595070076,61.3905630917231,63.0239659224377,50.4336376702161,57.7994051645972,47.7206817629305,54.9224929102974,77.5810683511197,50.2758570155833,43.3078407352268,47.1077409568026,41.7236401734888,46.1694883262395,51.4022235421503,24.9950458916537,42.1523982662211,32.4244147222287,51.8125853518243,35.212551897853,46.2326177977572,50.3069089522438,38.6447918040119,43.8248780394455,55.2093395374968,31.5901974750658,25.9038739965618,52.8338103867552,45.0320569941603,49.9511541876668,54.7643336558559,37.2151097969731,42.3243058111672,35.7886157551071,41.4763222832257,49.0254803902814,44.5529994139913,43.4532610442828,53.6106570528103,35.0881033521933,46.2897298993652,43.7619981214053,46.8458800903657,47.5529064151464,47.3783773378773,28.4077916753697,47.5240946575458,53.260051613555,47.4411617497182,37.2190516687142,43.0110042564814,52.5779689045228,38.6941466261844,41.5603994811627,34.8587754321875,31.9138372517676,38.8888660706534,51.7168702995673,36.5666853508916,46.4220885389424,46.4565119178831,37.1617973240384,42.889407883293,61.9734006224119,65.4138594410705,54.3588141859161,38.1510320786396,42.8246925006936,51.8479563105076,47.5340035961881,44.4926386913947,34.0216586094972,60.0294498247253,43.0588840511554,30.977292406543,53.0939955047098,39.2518296518348,48.1788376696666,50.7146213134774,40.3902587199197,29.134669673164,33.5623461291658,41.7380302293396,53.2188281297887,58.4282197257284,37.7604603384393,55.1806302709262,48.5414548164169,34.3235426878358,47.9972289812523,54.6189612604858,28.8536571535931,38.8403501967562,31.6049749200994,53.8964673020864,27.5430411333314,57.4371259511589,46.0790232775894,43.5438475657288,48.3930223835943,34.5449871002911,47.7052369045711,43.8887495568193,41.4711612763537,46.5674782261323,56.9703988109214,46.0844306953934,47.7349611131696,63.0575661125902,62.306333829638,53.0304576002181,24.0344741520413,36.0006339029012,46.6427926405313,47.2398587421419,38.4639831354719,36.2606496994207,57.1105217919983,36.4714847248397,49.0750797743774,72.2955245868335,47.9616617630947,41.7423441631097,55.7266773096622,44.5692095595866,37.7403625410456,44.3215192566116,41.3710508688733,36.1208769068858,44.7680079651631,54.0916852237344,27.4819374057567,44.0005282193953,59.7401171334029,32.901835539898,46.5564582820534,49.0960090186226,60.1805893960787,51.7415946881985,64.6380425918757,47.3824315979939,54.9385221202497,53.0136894665389,52.2162782603976,64.1456525544445,49.0354238356568,44.0781885036968,57.0445137111033,51.6821160568402,34.2332585892791,41.4426266430015,58.3817821209781,51.8312911457327,42.3281688523281,42.1239908646225,28.0595422843748,37.3030287865251,52.1242510390634,40.5924487690425,53.6736870322361,27.6404835096828,61.8156334556404,40.9544982178934,51.5792905375618,52.4233677398587,49.1690312209772,57.726950860457,34.9877445237654,38.9728408672699,41.6373623894274,42.4695220301696,48.4661096495681,47.0591990443877,59.4773829088427,63.9374262408604,57.5860100362243,38.1229725698407,39.1372571916366,44.7544488483543,35.0617604556116,41.3329867393869,38.3763401004266,36.8278250258893,61.7116182527638,29.4214319759866,41.9246020829454,39.0895947736108,47.6189198238223,69.8007328861621,28.0720917676133,26.4325202782375,47.9509278878915,31.5529172546707,28.3535263910498,54.3210455281708,41.5697576827806,55.4992064260723,50.0487046711159,43.0463527329072,58.8620093787008,58.0713524765874,21.3702695157613,33.3904300582367,40.087385663402,42.1002351917317,39.4996369987186,32.915521943964,28.8709354977335,34.5139815655534,36.6651255192073,41.1900188464134,42.357947062102,43.1257275777002,38.6217179018049,42.2778526631199,34.6674798749957,55.7698620460136,43.4942640179044,44.4332059011481,42.8751193406722,43.6947450427919,46.2677499208834,48.7751417642862,61.4741589704342,43.4918949824207,29.2744287974983,51.4817461411424,31.2432704849293,29.1055089802294,52.4220099889636,51.8250177264719,64.6052352941444,45.9755324315457,27.3116123145124,39.8311203577871,44.2856839387027,45.1262292461221,50.4791985981813,59.1191975065334,49.0881476063084,38.4282940883216,71.0780434694101,55.1624442521494,34.7807007843169,34.2421484268185,37.0354538189787,41.467806208145,38.2233657265271,68.9348396062863,45.3983674771773,45.1114025653748,58.3521541455263,47.126206223312,44.8781458778108,20.2576509187066,49.3635898258255,56.1665003391157,46.1057746654312,45.4855906755331,44.5592088268045,37.4887061558133,40.2958478079105,55.2972860712518,60.8803278593241,44.583590889843,37.8887549483432,47.3216088845618,40.8716305489611,24.924474725466,38.3025253145777,51.0464914906965,50.5922579722676,50.9234629607449,59.9802753800985,40.0233947275803,47.5095137678404,38.6038216419583,64.7763563441065,69.8801782011327,41.8830880454377,53.4188425762503,40.5871952469539,44.4593693031987,33.4565566858733,56.1254748261717,29.390420553251,50.9064255468724,58.0843215106214,47.3400050818469,47.2736233505797,42.9649607610517,28.3976885517259,53.3814706388523,46.5818485664013,47.4724360876433,38.885648947661,49.6351327440611,31.9868960271045,29.0072034413613,59.2852293590092,72.9948293765684,36.7691531958363,38.2152213017322,43.8876047788783,56.3051184827452,48.024474310634,51.8002572040429,47.8953450979482,33.1831481748138,41.042434440092,52.8709687116033,65.4483766561458,50.2008476999244,42.8385745713839,61.2716181621525,37.8619685544109,45.4940906814598,43.8782159335267,40.688232635243,37.1411654935385,41.9588758652705,31.2608583064805],"text":["pet: cat<br />happiness: 35.38118<br />pet: cat","pet: cat<br />happiness: 44.82197<br />pet: cat","pet: cat<br />happiness: 39.21730<br />pet: cat","pet: cat<br />happiness: 51.04672<br />pet: cat","pet: cat<br />happiness: 57.06565<br />pet: cat","pet: cat<br />happiness: 59.25837<br />pet: cat","pet: cat<br />happiness: 38.62695<br />pet: cat","pet: cat<br />happiness: 44.90472<br />pet: cat","pet: cat<br />happiness: 42.25500<br />pet: cat","pet: cat<br />happiness: 51.25026<br />pet: cat","pet: cat<br />happiness: 48.10866<br />pet: cat","pet: cat<br />happiness: 45.05305<br />pet: cat","pet: cat<br />happiness: 42.75199<br />pet: cat","pet: cat<br />happiness: 50.74365<br />pet: cat","pet: cat<br />happiness: 54.51215<br />pet: cat","pet: cat<br />happiness: 31.87095<br />pet: cat","pet: cat<br />happiness: 35.86872<br />pet: cat","pet: cat<br />happiness: 50.84358<br />pet: cat","pet: cat<br />happiness: 25.32440<br />pet: cat","pet: cat<br />happiness: 57.94212<br />pet: cat","pet: cat<br />happiness: 42.98963<br />pet: cat","pet: cat<br />happiness: 44.16652<br />pet: cat","pet: cat<br />happiness: 31.34667<br />pet: cat","pet: cat<br />happiness: 45.81559<br />pet: cat","pet: cat<br />happiness: 47.10467<br />pet: cat","pet: cat<br />happiness: 41.18430<br />pet: cat","pet: cat<br />happiness: 43.00711<br />pet: cat","pet: cat<br />happiness: 42.68130<br />pet: cat","pet: cat<br />happiness: 41.27090<br />pet: cat","pet: cat<br />happiness: 31.88799<br />pet: cat","pet: cat<br />happiness: 31.99007<br />pet: cat","pet: cat<br />happiness: 51.19468<br />pet: cat","pet: cat<br />happiness: 54.18103<br />pet: cat","pet: cat<br />happiness: 56.60801<br />pet: cat","pet: cat<br />happiness: 31.06778<br />pet: cat","pet: cat<br />happiness: 54.44915<br />pet: cat","pet: cat<br />happiness: 47.35475<br />pet: cat","pet: cat<br />happiness: 56.10664<br />pet: cat","pet: cat<br />happiness: 59.76718<br />pet: cat","pet: cat<br />happiness: 33.87959<br />pet: cat","pet: cat<br />happiness: 52.50072<br />pet: cat","pet: cat<br />happiness: 29.96466<br />pet: cat","pet: cat<br />happiness: 47.01246<br />pet: cat","pet: cat<br />happiness: 61.16654<br />pet: cat","pet: cat<br />happiness: 37.41532<br />pet: cat","pet: cat<br />happiness: 52.97119<br />pet: cat","pet: cat<br />happiness: 45.46321<br />pet: cat","pet: cat<br />happiness: 54.75059<br />pet: cat","pet: cat<br />happiness: 34.58653<br />pet: cat","pet: cat<br />happiness: 38.86012<br />pet: cat","pet: cat<br />happiness: 58.00901<br />pet: cat","pet: cat<br />happiness: 35.83469<br />pet: cat","pet: cat<br />happiness: 41.57619<br />pet: cat","pet: cat<br />happiness: 29.31794<br />pet: cat","pet: cat<br />happiness: 60.05917<br />pet: cat","pet: cat<br />happiness: 55.11003<br />pet: cat","pet: cat<br />happiness: 59.04682<br />pet: cat","pet: cat<br />happiness: 44.08083<br />pet: cat","pet: cat<br />happiness: 47.42489<br />pet: cat","pet: cat<br />happiness: 46.83704<br />pet: cat","pet: cat<br />happiness: 68.64559<br />pet: cat","pet: cat<br />happiness: 44.79486<br />pet: cat","pet: cat<br />happiness: 51.77674<br />pet: cat","pet: cat<br />happiness: 51.82101<br />pet: cat","pet: cat<br />happiness: 55.05358<br />pet: cat","pet: cat<br />happiness: 46.49245<br />pet: cat","pet: cat<br />happiness: 32.67340<br />pet: cat","pet: cat<br />happiness: 39.13971<br />pet: cat","pet: cat<br />happiness: 47.13271<br />pet: cat","pet: cat<br />happiness: 39.39790<br />pet: cat","pet: cat<br />happiness: 49.25077<br />pet: cat","pet: cat<br />happiness: 40.18979<br />pet: cat","pet: cat<br />happiness: 31.62474<br />pet: cat","pet: cat<br />happiness: 40.22066<br />pet: cat","pet: cat<br />happiness: 62.12752<br />pet: cat","pet: cat<br />happiness: 35.25237<br />pet: cat","pet: cat<br />happiness: 66.79728<br />pet: cat","pet: cat<br />happiness: 52.57486<br />pet: cat","pet: cat<br />happiness: 49.19316<br />pet: cat","pet: cat<br />happiness: 49.59679<br />pet: cat","pet: cat<br />happiness: 53.15174<br />pet: cat","pet: cat<br />happiness: 58.40492<br />pet: cat","pet: cat<br />happiness: 59.72814<br />pet: cat","pet: cat<br />happiness: 57.12450<br />pet: cat","pet: cat<br />happiness: 55.50637<br />pet: cat","pet: cat<br />happiness: 70.49849<br />pet: cat","pet: cat<br />happiness: 54.56793<br />pet: cat","pet: cat<br />happiness: 48.04528<br />pet: cat","pet: cat<br />happiness: 38.50275<br />pet: cat","pet: cat<br />happiness: 50.13251<br />pet: cat","pet: cat<br />happiness: 40.89090<br />pet: cat","pet: cat<br />happiness: 42.66374<br />pet: cat","pet: cat<br />happiness: 48.49161<br />pet: cat","pet: cat<br />happiness: 37.70173<br />pet: cat","pet: cat<br />happiness: 51.86998<br />pet: cat","pet: cat<br />happiness: 50.44434<br />pet: cat","pet: cat<br />happiness: 31.45042<br />pet: cat","pet: cat<br />happiness: 39.62327<br />pet: cat","pet: cat<br />happiness: 54.31839<br />pet: cat","pet: cat<br />happiness: 28.68607<br />pet: cat","pet: cat<br />happiness: 41.94105<br />pet: cat","pet: cat<br />happiness: 54.48139<br />pet: cat","pet: cat<br />happiness: 54.11131<br />pet: cat","pet: cat<br />happiness: 40.62460<br />pet: cat","pet: cat<br />happiness: 44.62815<br />pet: cat","pet: cat<br />happiness: 48.72877<br />pet: cat","pet: cat<br />happiness: 56.75676<br />pet: cat","pet: cat<br />happiness: 37.48591<br />pet: cat","pet: cat<br />happiness: 48.12390<br />pet: cat","pet: cat<br />happiness: 55.05332<br />pet: cat","pet: cat<br />happiness: 46.88026<br />pet: cat","pet: cat<br />happiness: 32.86105<br />pet: cat","pet: cat<br />happiness: 42.82388<br />pet: cat","pet: cat<br />happiness: 59.49190<br />pet: cat","pet: cat<br />happiness: 64.93291<br />pet: cat","pet: cat<br />happiness: 39.03429<br />pet: cat","pet: cat<br />happiness: 25.06697<br />pet: cat","pet: cat<br />happiness: 36.16436<br />pet: cat","pet: cat<br />happiness: 54.11744<br />pet: cat","pet: cat<br />happiness: 51.94953<br />pet: cat","pet: cat<br />happiness: 46.23669<br />pet: cat","pet: cat<br />happiness: 49.58795<br />pet: cat","pet: cat<br />happiness: 33.83846<br />pet: cat","pet: cat<br />happiness: 47.20617<br />pet: cat","pet: cat<br />happiness: 35.87886<br />pet: cat","pet: cat<br />happiness: 34.21143<br />pet: cat","pet: cat<br />happiness: 42.29581<br />pet: cat","pet: cat<br />happiness: 55.04815<br />pet: cat","pet: cat<br />happiness: 59.97875<br />pet: cat","pet: cat<br />happiness: 28.58992<br />pet: cat","pet: cat<br />happiness: 52.27246<br />pet: cat","pet: cat<br />happiness: 46.83266<br />pet: cat","pet: cat<br />happiness: 71.24033<br />pet: cat","pet: cat<br />happiness: 39.25205<br />pet: cat","pet: cat<br />happiness: 52.37093<br />pet: cat","pet: cat<br />happiness: 56.80350<br />pet: cat","pet: cat<br />happiness: 52.01859<br />pet: cat","pet: cat<br />happiness: 25.70843<br />pet: cat","pet: cat<br />happiness: 58.26958<br />pet: cat","pet: cat<br />happiness: 30.20065<br />pet: cat","pet: cat<br />happiness: 58.63981<br />pet: cat","pet: cat<br />happiness: 48.58693<br />pet: cat","pet: cat<br />happiness: 68.99931<br />pet: cat","pet: cat<br />happiness: 44.12244<br />pet: cat","pet: cat<br />happiness: 51.63524<br />pet: cat","pet: cat<br />happiness: 68.12566<br />pet: cat","pet: cat<br />happiness: 27.51783<br />pet: cat","pet: cat<br />happiness: 42.01059<br />pet: cat","pet: cat<br />happiness: 34.90216<br />pet: cat","pet: cat<br />happiness: 33.92034<br />pet: cat","pet: cat<br />happiness: 47.70420<br />pet: cat","pet: cat<br />happiness: 43.02883<br />pet: cat","pet: cat<br />happiness: 47.08339<br />pet: cat","pet: cat<br />happiness: 53.79365<br />pet: cat","pet: cat<br />happiness: 32.60735<br />pet: cat","pet: cat<br />happiness: 60.85116<br />pet: cat","pet: cat<br />happiness: 61.63040<br />pet: cat","pet: cat<br />happiness: 41.75144<br />pet: cat","pet: cat<br />happiness: 57.82354<br />pet: cat","pet: cat<br />happiness: 46.68308<br />pet: cat","pet: cat<br />happiness: 44.81783<br />pet: cat","pet: cat<br />happiness: 56.00800<br />pet: cat","pet: cat<br />happiness: 55.61782<br />pet: cat","pet: cat<br />happiness: 29.87317<br />pet: cat","pet: cat<br />happiness: 55.80353<br />pet: cat","pet: cat<br />happiness: 36.06702<br />pet: cat","pet: cat<br />happiness: 36.71670<br />pet: cat","pet: cat<br />happiness: 55.60392<br />pet: cat","pet: cat<br />happiness: 35.36821<br />pet: cat","pet: cat<br />happiness: 56.64354<br />pet: cat","pet: cat<br />happiness: 39.67447<br />pet: cat","pet: cat<br />happiness: 49.35133<br />pet: cat","pet: cat<br />happiness: 37.85798<br />pet: cat","pet: cat<br />happiness: 31.12101<br />pet: cat","pet: cat<br />happiness: 34.80014<br />pet: cat","pet: cat<br />happiness: 57.66747<br />pet: cat","pet: cat<br />happiness: 59.25900<br />pet: cat","pet: cat<br />happiness: 38.09760<br />pet: cat","pet: cat<br />happiness: 35.92376<br />pet: cat","pet: cat<br />happiness: 40.56006<br />pet: cat","pet: cat<br />happiness: 61.39056<br />pet: cat","pet: cat<br />happiness: 63.02397<br />pet: cat","pet: cat<br />happiness: 50.43364<br />pet: cat","pet: cat<br />happiness: 57.79941<br />pet: cat","pet: cat<br />happiness: 47.72068<br />pet: cat","pet: cat<br />happiness: 54.92249<br />pet: cat","pet: cat<br />happiness: 77.58107<br />pet: cat","pet: cat<br />happiness: 50.27586<br />pet: cat","pet: cat<br />happiness: 43.30784<br />pet: cat","pet: cat<br />happiness: 47.10774<br />pet: cat","pet: cat<br />happiness: 41.72364<br />pet: cat","pet: cat<br />happiness: 46.16949<br />pet: cat","pet: cat<br />happiness: 51.40222<br />pet: cat","pet: cat<br />happiness: 24.99505<br />pet: cat","pet: cat<br />happiness: 42.15240<br />pet: cat","pet: cat<br />happiness: 32.42441<br />pet: cat","pet: cat<br />happiness: 51.81259<br />pet: cat","pet: cat<br />happiness: 35.21255<br />pet: cat","pet: cat<br />happiness: 46.23262<br />pet: cat","pet: cat<br />happiness: 50.30691<br />pet: cat","pet: cat<br />happiness: 38.64479<br />pet: cat","pet: cat<br />happiness: 43.82488<br />pet: cat","pet: cat<br />happiness: 55.20934<br />pet: cat","pet: cat<br />happiness: 31.59020<br />pet: cat","pet: cat<br />happiness: 25.90387<br />pet: cat","pet: cat<br />happiness: 52.83381<br />pet: cat","pet: cat<br />happiness: 45.03206<br />pet: cat","pet: cat<br />happiness: 49.95115<br />pet: cat","pet: cat<br />happiness: 54.76433<br />pet: cat","pet: cat<br />happiness: 37.21511<br />pet: cat","pet: cat<br />happiness: 42.32431<br />pet: cat","pet: cat<br />happiness: 35.78862<br />pet: cat","pet: cat<br />happiness: 41.47632<br />pet: cat","pet: cat<br />happiness: 49.02548<br />pet: cat","pet: cat<br />happiness: 44.55300<br />pet: cat","pet: cat<br />happiness: 43.45326<br />pet: cat","pet: cat<br />happiness: 53.61066<br />pet: cat","pet: cat<br />happiness: 35.08810<br />pet: cat","pet: cat<br />happiness: 46.28973<br />pet: cat","pet: cat<br />happiness: 43.76200<br />pet: cat","pet: cat<br />happiness: 46.84588<br />pet: cat","pet: cat<br />happiness: 47.55291<br />pet: cat","pet: cat<br />happiness: 47.37838<br />pet: cat","pet: cat<br />happiness: 28.40779<br />pet: cat","pet: cat<br />happiness: 47.52409<br />pet: cat","pet: cat<br />happiness: 53.26005<br />pet: cat","pet: cat<br />happiness: 47.44116<br />pet: cat","pet: cat<br />happiness: 37.21905<br />pet: cat","pet: cat<br />happiness: 43.01100<br />pet: cat","pet: cat<br />happiness: 52.57797<br />pet: cat","pet: cat<br />happiness: 38.69415<br />pet: cat","pet: cat<br />happiness: 41.56040<br />pet: cat","pet: cat<br />happiness: 34.85878<br />pet: cat","pet: cat<br />happiness: 31.91384<br />pet: cat","pet: cat<br />happiness: 38.88887<br />pet: cat","pet: cat<br />happiness: 51.71687<br />pet: cat","pet: cat<br />happiness: 36.56669<br />pet: cat","pet: cat<br />happiness: 46.42209<br />pet: cat","pet: cat<br />happiness: 46.45651<br />pet: cat","pet: cat<br />happiness: 37.16180<br />pet: cat","pet: cat<br />happiness: 42.88941<br />pet: cat","pet: cat<br />happiness: 61.97340<br />pet: cat","pet: cat<br />happiness: 65.41386<br />pet: cat","pet: cat<br />happiness: 54.35881<br />pet: cat","pet: cat<br />happiness: 38.15103<br />pet: cat","pet: cat<br />happiness: 42.82469<br />pet: cat","pet: cat<br />happiness: 51.84796<br />pet: cat","pet: cat<br />happiness: 47.53400<br />pet: cat","pet: cat<br />happiness: 44.49264<br />pet: cat","pet: cat<br />happiness: 34.02166<br />pet: cat","pet: cat<br />happiness: 60.02945<br />pet: cat","pet: cat<br />happiness: 43.05888<br />pet: cat","pet: cat<br />happiness: 30.97729<br />pet: cat","pet: cat<br />happiness: 53.09400<br />pet: cat","pet: cat<br />happiness: 39.25183<br />pet: cat","pet: cat<br />happiness: 48.17884<br />pet: cat","pet: cat<br />happiness: 50.71462<br />pet: cat","pet: cat<br />happiness: 40.39026<br />pet: cat","pet: cat<br />happiness: 29.13467<br />pet: cat","pet: cat<br />happiness: 33.56235<br />pet: cat","pet: cat<br />happiness: 41.73803<br />pet: cat","pet: cat<br />happiness: 53.21883<br />pet: cat","pet: cat<br />happiness: 58.42822<br />pet: cat","pet: cat<br />happiness: 37.76046<br />pet: cat","pet: cat<br />happiness: 55.18063<br />pet: cat","pet: cat<br />happiness: 48.54145<br />pet: cat","pet: cat<br />happiness: 34.32354<br />pet: cat","pet: cat<br />happiness: 47.99723<br />pet: cat","pet: cat<br />happiness: 54.61896<br />pet: cat","pet: cat<br />happiness: 28.85366<br />pet: cat","pet: cat<br />happiness: 38.84035<br />pet: cat","pet: cat<br />happiness: 31.60497<br />pet: cat","pet: cat<br />happiness: 53.89647<br />pet: cat","pet: cat<br />happiness: 27.54304<br />pet: cat","pet: cat<br />happiness: 57.43713<br />pet: cat","pet: cat<br />happiness: 46.07902<br />pet: cat","pet: cat<br />happiness: 43.54385<br />pet: cat","pet: cat<br />happiness: 48.39302<br />pet: cat","pet: cat<br />happiness: 34.54499<br />pet: cat","pet: cat<br />happiness: 47.70524<br />pet: cat","pet: cat<br />happiness: 43.88875<br />pet: cat","pet: cat<br />happiness: 41.47116<br />pet: cat","pet: cat<br />happiness: 46.56748<br />pet: cat","pet: cat<br />happiness: 56.97040<br />pet: cat","pet: cat<br />happiness: 46.08443<br />pet: cat","pet: cat<br />happiness: 47.73496<br />pet: cat","pet: cat<br />happiness: 63.05757<br />pet: cat","pet: cat<br />happiness: 62.30633<br />pet: cat","pet: cat<br />happiness: 53.03046<br />pet: cat","pet: cat<br />happiness: 24.03447<br />pet: cat","pet: cat<br />happiness: 36.00063<br />pet: cat","pet: cat<br />happiness: 46.64279<br />pet: cat","pet: cat<br />happiness: 47.23986<br />pet: cat","pet: cat<br />happiness: 38.46398<br />pet: cat","pet: cat<br />happiness: 36.26065<br />pet: cat","pet: cat<br />happiness: 57.11052<br />pet: cat","pet: cat<br />happiness: 36.47148<br />pet: cat","pet: cat<br />happiness: 49.07508<br />pet: cat","pet: cat<br />happiness: 72.29552<br />pet: cat","pet: cat<br />happiness: 47.96166<br />pet: cat","pet: cat<br />happiness: 41.74234<br />pet: cat","pet: cat<br />happiness: 55.72668<br />pet: cat","pet: cat<br />happiness: 44.56921<br />pet: cat","pet: cat<br />happiness: 37.74036<br />pet: cat","pet: cat<br />happiness: 44.32152<br />pet: cat","pet: cat<br />happiness: 41.37105<br />pet: cat","pet: cat<br />happiness: 36.12088<br />pet: cat","pet: cat<br />happiness: 44.76801<br />pet: cat","pet: cat<br />happiness: 54.09169<br />pet: cat","pet: cat<br />happiness: 27.48194<br />pet: cat","pet: cat<br />happiness: 44.00053<br />pet: cat","pet: cat<br />happiness: 59.74012<br />pet: cat","pet: cat<br />happiness: 32.90184<br />pet: cat","pet: cat<br />happiness: 46.55646<br />pet: cat","pet: cat<br />happiness: 49.09601<br />pet: cat","pet: cat<br />happiness: 60.18059<br />pet: cat","pet: cat<br />happiness: 51.74159<br />pet: cat","pet: cat<br />happiness: 64.63804<br />pet: cat","pet: cat<br />happiness: 47.38243<br />pet: cat","pet: cat<br />happiness: 54.93852<br />pet: cat","pet: cat<br />happiness: 53.01369<br />pet: cat","pet: cat<br />happiness: 52.21628<br />pet: cat","pet: cat<br />happiness: 64.14565<br />pet: cat","pet: cat<br />happiness: 49.03542<br />pet: cat","pet: cat<br />happiness: 44.07819<br />pet: cat","pet: cat<br />happiness: 57.04451<br />pet: cat","pet: cat<br />happiness: 51.68212<br />pet: cat","pet: cat<br />happiness: 34.23326<br />pet: cat","pet: cat<br />happiness: 41.44263<br />pet: cat","pet: cat<br />happiness: 58.38178<br />pet: cat","pet: cat<br />happiness: 51.83129<br />pet: cat","pet: cat<br />happiness: 42.32817<br />pet: cat","pet: cat<br />happiness: 42.12399<br />pet: cat","pet: cat<br />happiness: 28.05954<br />pet: cat","pet: cat<br />happiness: 37.30303<br />pet: cat","pet: cat<br />happiness: 52.12425<br />pet: cat","pet: cat<br />happiness: 40.59245<br />pet: cat","pet: cat<br />happiness: 53.67369<br />pet: cat","pet: cat<br />happiness: 27.64048<br />pet: cat","pet: cat<br />happiness: 61.81563<br />pet: cat","pet: cat<br />happiness: 40.95450<br />pet: cat","pet: cat<br />happiness: 51.57929<br />pet: cat","pet: cat<br />happiness: 52.42337<br />pet: cat","pet: cat<br />happiness: 49.16903<br />pet: cat","pet: cat<br />happiness: 57.72695<br />pet: cat","pet: cat<br />happiness: 34.98774<br />pet: cat","pet: cat<br />happiness: 38.97284<br />pet: cat","pet: cat<br />happiness: 41.63736<br />pet: cat","pet: cat<br />happiness: 42.46952<br />pet: cat","pet: cat<br />happiness: 48.46611<br />pet: cat","pet: cat<br />happiness: 47.05920<br />pet: cat","pet: cat<br />happiness: 59.47738<br />pet: cat","pet: cat<br />happiness: 63.93743<br />pet: cat","pet: cat<br />happiness: 57.58601<br />pet: cat","pet: cat<br />happiness: 38.12297<br />pet: cat","pet: cat<br />happiness: 39.13726<br />pet: cat","pet: cat<br />happiness: 44.75445<br />pet: cat","pet: cat<br />happiness: 35.06176<br />pet: cat","pet: cat<br />happiness: 41.33299<br />pet: cat","pet: cat<br />happiness: 38.37634<br />pet: cat","pet: cat<br />happiness: 36.82783<br />pet: cat","pet: cat<br />happiness: 61.71162<br />pet: cat","pet: cat<br />happiness: 29.42143<br />pet: cat","pet: cat<br />happiness: 41.92460<br />pet: cat","pet: cat<br />happiness: 39.08959<br />pet: cat","pet: cat<br />happiness: 47.61892<br />pet: cat","pet: cat<br />happiness: 69.80073<br />pet: cat","pet: cat<br />happiness: 28.07209<br />pet: cat","pet: cat<br />happiness: 26.43252<br />pet: cat","pet: cat<br />happiness: 47.95093<br />pet: cat","pet: cat<br />happiness: 31.55292<br />pet: cat","pet: cat<br />happiness: 28.35353<br />pet: cat","pet: cat<br />happiness: 54.32105<br />pet: cat","pet: cat<br />happiness: 41.56976<br />pet: cat","pet: cat<br />happiness: 55.49921<br />pet: cat","pet: cat<br />happiness: 50.04870<br />pet: cat","pet: cat<br />happiness: 43.04635<br />pet: cat","pet: cat<br />happiness: 58.86201<br />pet: cat","pet: cat<br />happiness: 58.07135<br />pet: cat","pet: cat<br />happiness: 21.37027<br />pet: cat","pet: cat<br />happiness: 33.39043<br />pet: cat","pet: cat<br />happiness: 40.08739<br />pet: cat","pet: cat<br />happiness: 42.10024<br />pet: cat","pet: cat<br />happiness: 39.49964<br />pet: cat","pet: cat<br />happiness: 32.91552<br />pet: cat","pet: cat<br />happiness: 28.87094<br />pet: cat","pet: cat<br />happiness: 34.51398<br />pet: cat","pet: cat<br />happiness: 36.66513<br />pet: cat","pet: cat<br />happiness: 41.19002<br />pet: cat","pet: cat<br />happiness: 42.35795<br />pet: cat","pet: cat<br />happiness: 43.12573<br />pet: cat","pet: cat<br />happiness: 38.62172<br />pet: cat","pet: cat<br />happiness: 42.27785<br />pet: cat","pet: cat<br />happiness: 34.66748<br />pet: cat","pet: cat<br />happiness: 55.76986<br />pet: cat","pet: cat<br />happiness: 43.49426<br />pet: cat","pet: cat<br />happiness: 44.43321<br />pet: cat","pet: cat<br />happiness: 42.87512<br />pet: cat","pet: cat<br />happiness: 43.69475<br />pet: cat","pet: cat<br />happiness: 46.26775<br />pet: cat","pet: cat<br />happiness: 48.77514<br />pet: cat","pet: cat<br />happiness: 61.47416<br />pet: cat","pet: cat<br />happiness: 43.49189<br />pet: cat","pet: cat<br />happiness: 29.27443<br />pet: cat","pet: cat<br />happiness: 51.48175<br />pet: cat","pet: cat<br />happiness: 31.24327<br />pet: cat","pet: cat<br />happiness: 29.10551<br />pet: cat","pet: cat<br />happiness: 52.42201<br />pet: cat","pet: cat<br />happiness: 51.82502<br />pet: cat","pet: cat<br />happiness: 64.60524<br />pet: cat","pet: cat<br />happiness: 45.97553<br />pet: cat","pet: cat<br />happiness: 27.31161<br />pet: cat","pet: cat<br />happiness: 39.83112<br />pet: cat","pet: cat<br />happiness: 44.28568<br />pet: cat","pet: cat<br />happiness: 45.12623<br />pet: cat","pet: cat<br />happiness: 50.47920<br />pet: cat","pet: cat<br />happiness: 59.11920<br />pet: cat","pet: cat<br />happiness: 49.08815<br />pet: cat","pet: cat<br />happiness: 38.42829<br />pet: cat","pet: cat<br />happiness: 71.07804<br />pet: cat","pet: cat<br />happiness: 55.16244<br />pet: cat","pet: cat<br />happiness: 34.78070<br />pet: cat","pet: cat<br />happiness: 34.24215<br />pet: cat","pet: cat<br />happiness: 37.03545<br />pet: cat","pet: cat<br />happiness: 41.46781<br />pet: cat","pet: cat<br />happiness: 38.22337<br />pet: cat","pet: cat<br />happiness: 68.93484<br />pet: cat","pet: cat<br />happiness: 45.39837<br />pet: cat","pet: cat<br />happiness: 45.11140<br />pet: cat","pet: cat<br />happiness: 58.35215<br />pet: cat","pet: cat<br />happiness: 47.12621<br />pet: cat","pet: cat<br />happiness: 44.87815<br />pet: cat","pet: cat<br />happiness: 20.25765<br />pet: cat","pet: cat<br />happiness: 49.36359<br />pet: cat","pet: cat<br />happiness: 56.16650<br />pet: cat","pet: cat<br />happiness: 46.10577<br />pet: cat","pet: cat<br />happiness: 45.48559<br />pet: cat","pet: cat<br />happiness: 44.55921<br />pet: cat","pet: cat<br />happiness: 37.48871<br />pet: cat","pet: cat<br />happiness: 40.29585<br />pet: cat","pet: cat<br />happiness: 55.29729<br />pet: cat","pet: cat<br />happiness: 60.88033<br />pet: cat","pet: cat<br />happiness: 44.58359<br />pet: cat","pet: cat<br />happiness: 37.88875<br />pet: cat","pet: cat<br />happiness: 47.32161<br />pet: cat","pet: cat<br />happiness: 40.87163<br />pet: cat","pet: cat<br />happiness: 24.92447<br />pet: cat","pet: cat<br />happiness: 38.30253<br />pet: cat","pet: cat<br />happiness: 51.04649<br />pet: cat","pet: cat<br />happiness: 50.59226<br />pet: cat","pet: cat<br />happiness: 50.92346<br />pet: cat","pet: cat<br />happiness: 59.98028<br />pet: cat","pet: cat<br />happiness: 40.02339<br />pet: cat","pet: cat<br />happiness: 47.50951<br />pet: cat","pet: cat<br />happiness: 38.60382<br />pet: cat","pet: cat<br />happiness: 64.77636<br />pet: cat","pet: cat<br />happiness: 69.88018<br />pet: cat","pet: cat<br />happiness: 41.88309<br />pet: cat","pet: cat<br />happiness: 53.41884<br />pet: cat","pet: cat<br />happiness: 40.58720<br />pet: cat","pet: cat<br />happiness: 44.45937<br />pet: cat","pet: cat<br />happiness: 33.45656<br />pet: cat","pet: cat<br />happiness: 56.12547<br />pet: cat","pet: cat<br />happiness: 29.39042<br />pet: cat","pet: cat<br />happiness: 50.90643<br />pet: cat","pet: cat<br />happiness: 58.08432<br />pet: cat","pet: cat<br />happiness: 47.34001<br />pet: cat","pet: cat<br />happiness: 47.27362<br />pet: cat","pet: cat<br />happiness: 42.96496<br />pet: cat","pet: cat<br />happiness: 28.39769<br />pet: cat","pet: cat<br />happiness: 53.38147<br />pet: cat","pet: cat<br />happiness: 46.58185<br />pet: cat","pet: cat<br />happiness: 47.47244<br />pet: cat","pet: cat<br />happiness: 38.88565<br />pet: cat","pet: cat<br />happiness: 49.63513<br />pet: cat","pet: cat<br />happiness: 31.98690<br />pet: cat","pet: cat<br />happiness: 29.00720<br />pet: cat","pet: cat<br />happiness: 59.28523<br />pet: cat","pet: cat<br />happiness: 72.99483<br />pet: cat","pet: cat<br />happiness: 36.76915<br />pet: cat","pet: cat<br />happiness: 38.21522<br />pet: cat","pet: cat<br />happiness: 43.88760<br />pet: cat","pet: cat<br />happiness: 56.30512<br />pet: cat","pet: cat<br />happiness: 48.02447<br />pet: cat","pet: cat<br />happiness: 51.80026<br />pet: cat","pet: cat<br />happiness: 47.89535<br />pet: cat","pet: cat<br />happiness: 33.18315<br />pet: cat","pet: cat<br />happiness: 41.04243<br />pet: cat","pet: cat<br />happiness: 52.87097<br />pet: cat","pet: cat<br />happiness: 65.44838<br />pet: cat","pet: cat<br />happiness: 50.20085<br />pet: cat","pet: cat<br />happiness: 42.83857<br />pet: cat","pet: cat<br />happiness: 61.27162<br />pet: cat","pet: cat<br />happiness: 37.86197<br />pet: cat","pet: cat<br />happiness: 45.49409<br />pet: cat","pet: cat<br />happiness: 43.87822<br />pet: cat","pet: cat<br />happiness: 40.68823<br />pet: cat","pet: cat<br />happiness: 37.14117<br />pet: cat","pet: cat<br />happiness: 41.95888<br />pet: cat","pet: cat<br />happiness: 31.26086<br />pet: cat"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(248,118,109,1)","opacity":1,"size":7.55905511811024,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)"}},"hoveron":"points","name":"cat","legendgroup":"cat","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[1.94286177158356,2.13214225498959,1.94749630233273,2.09581905016676,1.84801771165803,2.15678919414058,1.96419725362211,1.88693681219593,1.95972072901204,1.83104589367285,1.90399920167401,2.16662293849513,2.1930724516511,2.19546998692676,2.18177858814597,2.07392579466104,1.85342912878841,1.91738878507167,1.9107746261172,1.97898977138102,1.91555800866336,2.11606873497367,1.88404332930222,2.14370791893452,2.12090960210189,1.8118344636634,2.05509490799159,1.97435030937195,1.86906232545152,1.87275602808222,2.05905679995194,1.93274106923491,2.12262303754687,1.97197090415284,2.17659955425188,1.98989086551592,1.91957625923678,1.80296925464645,2.02047546533868,2.01644769376144,2.06802793256938,1.99079373637214,1.8802667863667,2.16526834908873,1.94043359253556,1.86667783325538,2.07224767962471,2.15319239068776,2.09188241632655,1.81755615938455,2.10022355699912,1.98026034608483,2.06038390593603,2.10104277450591,1.8571898361668,1.85820724992082,1.88857880393043,1.94870371129364,1.96176226306707,2.1335273809731,1.91193991294131,2.16183360693976,1.80825558463112,2.06050158608705,2.00677148615941,2.0360581189394,1.85070056803524,2.04969982411712,1.97103770030662,2.10595431933179,2.08823546115309,1.81738038631156,2.18859322872013,2.11054994631559,2.13963399613276,1.86370718004182,1.99814633922651,1.82583064008504,1.93474434046075,1.88556485669687,1.86625306410715,2.09770754650235,2.06103610470891,1.94992111269385,2.0490397632122,2.1752183040604,1.81927142078057,2.03950832728297,2.12941925181076,1.86760585000739,1.84076666627079,1.89559430330992,1.8761658330448,1.94589580120519,1.88188834311441,1.98104207441211,1.96182244224474,2.08751245168969,2.14641384957358,2.12776765013114,1.95216005500406,1.85076774936169,1.94912330182269,2.15479498477653,1.93579495269805,1.81575626209378,2.01364972218871,1.94178554220125,2.09767978312448,2.11593931848183,1.83444437589496,2.15294445613399,2.1661209281534,2.1785811255686,1.87033379254863,2.0553628093563,1.98636165531352,1.9994866409339,2.19106753896922,2.07433757381514,2.13491221126169,2.07705609472468,1.83532761102542,2.10938464114442,2.17619102206081,1.93722590012476,1.95796710951254,1.93586211614311,1.89235237464309,1.95107594532892,1.89014097796753,2.1499100279063,1.8961641388014,2.03613256718963,1.85764189483598,2.06208381634206,1.82753929551691,1.98658265108243,2.1570060803555,1.93488665241748,2.16543658189476,2.15552496742457,2.07970928288996,1.99289378952235,1.9448888185434,1.99794136062264,2.14482264257967,1.9299935166724,1.98159224167466,2.1794855796732,2.04792271396145,1.80188542138785,2.18282737517729,2.16991506377235,1.81233703512698,1.88487362433225,1.94729872308671,2.12819340266287,2.08975034831092,2.07420459100977,2.04462567213923,2.16509157605469,2.14321375750005,1.99640089133754,2.05506071969867,1.84436894757673,1.91876142993569,1.98332315469161,2.03726787632331,1.87715729018673,2.06230675531551,2.17067572120577,1.90267771398649,2.06510789990425,2.196611411497,1.84047507625073,1.84411907568574,1.87188351498917,1.93805044041947,1.9104185490869,2.0989550055936,2.13137636594474,1.96473484924063,1.93616702426225,2.18846638984978,1.89339953260496,2.18322768993676,1.92692503510043,2.18412960814312,1.89874201668426,1.98353989170864,2.10614278502762,1.90899226060137,1.89240436274558,1.9509319097735,2.05917951529846,1.93274297211319,2.0814645412378,2.109808208514,1.82716744244099,2.02113946741447,1.97331754630432,2.15933037409559,2.14054170250893,2.18597629200667,1.98162909727544,2.11954248966649,2.10364978099242,1.95964084919542,1.9194744784385,1.91169631648809,1.95214292714372,2.14706594254822,2.06571763390675,1.85982392756268,1.85440286612138,2.06753761526197,2.0155421378091,1.97318242201582,1.98571125231683,1.91729211509228,2.19043550100178,2.17320216652006,1.90192213403061,2.01866070050746,1.87252970300615,1.95349233215675,1.89173657717183,1.9826289890334,1.88900139462203,1.80594048593193,2.1699620324187,2.03047053655609,1.97137174615636,1.98567687980831,1.80989829171449,1.82730335537344,1.91593407047912,2.17689794115722,2.11249367352575,1.89396243896335,1.92630308261141,1.82766786627471,2.04353075148538,1.85480496492237,2.12343928040937,2.09572840621695,2.19484742246568,2.03511715149507,1.82353977896273,2.01093545742333,2.0742316223681,2.04513055440038,2.06028619669378,1.91540512433276,2.04451491618529,2.12788640018553,1.84735719840974,2.07799349147826,1.83990478813648,1.8556097118184,1.90600168202072,2.03935171701014,2.15129009401426,1.84689529715106,2.0103908251971,1.83294671662152,2.01191232064739,1.84806917887181,2.02560555767268,2.14447442432866,1.8518246001564,1.92735494626686,1.96906400723383,1.96411914899945,2.02832375625148,1.95038691917434,1.98393083093688,1.8211446790956,1.89807340577245,2.14154425384477,1.92549193017185,2.18684213636443,2.15676108309999,2.15682186447084,2.04776950869709,1.90840388853103,2.18378293272108,2.14437419855967,1.87501357570291,2.09507962437347,1.94394793985412,1.98654990326613,2.12786499625072,1.89400322828442,2.14891235763207,2.05817301087081,2.13502473356202,2.10892033074051,1.83059823736548,1.81168609941378,2.13011308405548,2.08194247847423,1.89247161885723,2.17299833018333,1.81810356574133,1.92590791787952,1.90185614591464,1.96302695162594,1.90530575327575,2.19124502325431,1.94039815617725,1.81693494897336,1.91972195813432,2.16306766569614,1.96488151494414,2.03764479830861,2.10192242274061,1.99621721785516,2.11107453191653,2.18926453208551,1.83736445950344,2.1038667566143,2.11219284646213,1.80541565017775,2.10682363854721,1.81749609038234,2.03760634465143,1.84177765101194,2.17318517416716,1.99735846593976,1.82272812947631,2.07223087074235,1.88835134459659,1.90563658382744,1.90575902089477,2.00675186095759,2.19330289112404,1.99198595937341,1.96128013730049,1.87416082508862,1.86248763855547,2.15216710418463,1.81691933656111,2.17838842589408,2.13572666291147,2.10018361294642,2.01021597227082,1.89066311009228,1.925745591335,2.14482795894146,1.91676357872784,2.03432778287679,1.85527530591935,2.15225919038057,2.05828886609524,1.93507705871016,1.97852929551154,2.10093600526452,2.13481257325038,2.00592449819669,1.98826402435079,2.05462548313662,2.13109691739082,2.15859693661332,2.00514825936407,2.15350362854078,1.97615150306374,2.08014900451526,1.85664425315335,1.94945048922673,1.88143791370094,2.07384979547933,2.19168049795553,2.12558952523395,2.18615652965382,2.16733797360212,2.13186996076256,1.86064753681421,2.13307904861867,1.83307346133515,1.84233836531639,2.0216476559639,2.08244367428124,2.05804192097858,2.06053872760385,2.12110237963498,1.85373115995899,1.94690289795399,2.18501185132191,1.82741938885301,1.86910082949325,1.82586255790666,2.02044202014804,2.15056452108547,2.09571974035352,2.17940333196893,1.93726728828624,2.0802316557616,1.97998810429126,1.90357985282317,2.13968296414241,1.94601037427783,2.1721169105731,1.99720415296033,2.05483508473262,1.88585414458066,1.9157520333305,1.96773595260456,1.99494507825002,1.81946386275813,2.06728749666363,2.0790268195793,1.97395913517103,1.86112844543532,2.0772113176994,1.84862346528098,1.86901511671022,1.851850678958,1.9247970671393,2.05229534795508,2.18213445572183,2.06142798289657,2.06773073412478,1.90013538850471,2.15781694240868,1.85062626320869,1.90537285283208,2.1088628959842,1.84222436016425,1.92575342226774,2.0404975745827,1.86882742149755,2.0252063552849,2.14825064530596,1.89197763614357,2.17445088783279,1.90621319646016,1.92316375374794,1.97314483532682,2.01710424842313,2.05233739009127,2.16608365941793,2.01335515948012,1.87443476170301,1.80610771626234,1.87316416520625,2.06042452026159,1.81441925503314,2.18789696544409,2.02151150135323,2.17668049316853,1.93168512731791,2.03961892947555,2.16188546540216,2.05885085677728,1.89343160977587,2.08743100957945,1.95159010356292,2.07929887985811,1.89038724740967,1.97347859870642,2.0231246852316,1.82952172784135,1.90305354231969,1.93339359201491,1.98819194575772,2.16796598443761,2.04860974689946,2.02949589630589,2.06076481407508,1.81373899243772,2.01454503340647,1.84333254694939,1.90503540569916,2.18034027451649,1.85532838450745,2.08463670909405,1.81597669739276,2.00160462940112,1.88911766223609,1.91250911187381,2.05304806735367,2.05948690315709,2.00745671214536,1.80068912180141,1.93697272622958,1.95255327289924,2.03220612788573,1.84401549007744,1.96383881727234,1.929566228576,2.09832846429199,1.92561591640115,1.91387978037819,1.91721677277237,1.96597740706056,2.04919912051409,2.00735551109537,2.06605820637196],"y":[59.3232970155649,62.0470124238799,62.7223650150982,51.0812706480202,57.792142093518,24.2050840549579,58.4824679235144,65.5486362525563,50.6123397981094,46.1055443388724,58.959497168528,55.6817672169934,61.5963696341057,48.7058762348572,44.0239519072813,40.9786803479715,41.5561627916918,42.6124479761828,65.6799156779876,56.5954514334278,46.0247163265014,68.9530280314266,46.600058571875,60.6427216731335,51.0112767666846,51.306876387846,43.4369659393814,46.8681875413508,54.119027376187,58.2192045403988,44.684245353342,77.8459087157233,60.3199767004849,45.1503143654938,33.50115032338,30.4096168001683,59.3989794799624,63.3279023045472,53.8855057618221,54.7387766259985,74.6828507480383,59.2757386775091,69.7494668496896,56.2774788801195,41.6526673388154,49.2661122011897,61.8335497793372,68.1170601263406,65.8810420977537,49.0444361152227,62.6634686089561,47.5080161719613,54.9848283139041,40.9227162413551,50.9468818805633,55.9713564707288,59.7001590441541,56.2651306234764,50.1001499372122,54.791521765202,52.8073084971205,46.4053971936761,43.3338573983132,40.7436927229748,52.0191743362766,31.7593309390562,66.274526299453,65.7919089904336,55.7618422585182,68.0974573123317,49.1792496243595,49.4387226383743,45.8434517698742,77.8442681827855,48.8742601460754,71.4804399370992,59.2422857643597,64.8662314597421,50.0655507572252,44.5424030549729,60.7846674214035,58.8177873148574,61.9570390482191,41.8614548430263,55.6898951059615,47.9004570191576,51.2669960427495,46.1824966534775,59.4988290844428,61.3836508257284,55.7676189378681,61.158259744373,60.0477677391226,50.3144760086114,51.4326621852644,51.8695928873157,59.6590854062588,69.5479588927877,66.7893897878122,50.9347017749029,62.5111593934315,48.7085960504816,72.4079857887089,36.4836568635729,64.1156050414231,60.7191706002648,52.8584613955895,68.1643250292781,56.9308191613008,56.2273543883906,55.6482697363725,47.1470142433931,56.7059199542052,32.5392965080912,34.7689074453395,58.1270572047469,61.3678785270267,52.8235423710894,49.8007974319455,49.1029692477851,39.6736946097674,45.7502718036266,43.0856843250749,35.1994951560491,65.603316463044,74.1796656260543,61.484581773149,50.4660336314899,32.5485010472447,53.0891943328322,45.5437163315726,44.4705803610746,37.909813120533,31.1898939170397,57.1026034494193,58.2796107230929,50.5829714044371,52.7692423265948,56.4772414587902,72.4931388204588,63.0195314305468,65.9237753428125,42.452425426363,50.3936353478637,43.1693278575398,42.5697860544963,34.5786334245869,51.6986393200442,47.9982848446813,54.2754963017401,60.229061987575,45.4912591249763,68.6232639625269,57.4209161374242,72.5980231852044,36.9681547624803,61.3080079852848,55.2932970259702,56.6290374552751,47.5141116697807,60.681899094328,58.5367977167238,67.3761194524629,49.3082047604054,48.3974289753025,53.8877732418167,61.1108919657462,43.7982838367799,65.3511790971099,51.8651738340103,44.6849257517246,50.329840684079,37.0972097561973,53.3073520546312,46.3006284469092,52.4589373500347,54.5593865813085,41.7084976837727,48.5371030768371,56.3692412772575,62.996576159593,55.1848409118493,34.3249991850828,51.0225115057366,65.7257251006702,55.6462105049644,56.7547475297714,64.1400904450003,67.6094824276937,62.7041712750177,49.1783097118301,46.4325855302422,48.9411803194589,43.9542364479464,59.8506058033076,42.3975300767922,69.7887206341218,40.1661036810368,39.8832513335655,65.0860463724127,52.1731669274357,60.5179014894746,49.078710247694,58.3753809744395,54.1410694329306,27.2157629007869,64.1663990383969,56.8228766903454,55.0094292696423,59.9717954870123,38.8225266137207,64.1487400862298,67.4170103536868,58.6813025589746,37.6951397620935,59.8119917640012,58.1526839621835,60.5764178792556,52.4640148691514,42.4985622268364,54.4879311387778,55.9074012566349,66.5798528418457,44.2036183268791,47.8756050736637,69.2286862640933,55.0186927973383,67.5023871238164,59.0451646071944,66.8010261389852,57.6352385337934,52.8373687551018,65.1630479520935,58.4423256013926,67.2507891238831,54.4942076893014,55.4713400512423,57.5784064317438,46.0994672760059,71.27501271407,56.8130431891919,52.0641001570804,61.0744390611452,48.95369567116,71.1081520255361,46.1361956899123,48.2483797797195,66.1711869880248,74.6671142529607,39.1880255088707,49.1621666333193,46.8303267238942,49.8788311472205,53.0148137683229,51.235619493,39.5734774415124,48.6200950768545,55.8112784431699,43.4139867952147,55.5493999969385,53.1218405226362,57.7734239873928,59.192961438998,51.9755712591652,58.8728428211184,59.6823964449591,70.9537888221438,58.0399320474756,53.7603220476104,42.6164218449405,85.3392885608209,80.5191075981226,10.7905912904778,53.7324593500776,38.827965336431,72.5069051470691,60.9502506958558,39.7733563560786,64.4865479069665,46.1149474611005,83.0689807059198,65.9174934067721,58.8968170615751,68.8209738430891,64.1800566150289,74.2749105203193,63.1891599406191,61.0325258079339,50.3880481508301,40.2111530488832,64.7747586989139,53.049617904852,61.7265827707602,63.4401668525257,60.8440029463998,58.6482990591463,52.7342410231635,43.3240020851225,57.1643722646014,53.9004577836023,54.8493387728102,51.9921906877755,76.1413395256501,62.5761729702212,56.8683661574053,49.6478969215104,47.5082223527396,65.2354668894749,31.6155203436696,48.2682605363857,54.8116324500191,54.8704815910262,47.3099589421468,48.5578131805244,71.8872998010086,54.9273341262255,41.9653758203657,68.675181616562,61.0824888603749,36.8027635767685,62.0187477655124,55.3547604084032,58.3264047331658,46.9698992703384,67.30933466746,48.7422275764969,48.5306206143428,35.1300880183543,43.8201605155701,59.9908191809927,53.9472415645694,49.3968320981759,56.999775830458,75.1481073600579,58.9432238486723,61.0962907809223,62.2076525014089,47.7421613031437,52.4254836694953,67.2788022787154,48.5397365225738,57.0996723873929,61.3852151065293,54.8844025917933,49.0082925756674,57.7655752732984,59.9941346130654,45.7590453831646,63.1157554316964,64.0134789985866,57.4607278447799,64.3579194697997,55.4977479505777,40.1275221745971,47.0308147679925,54.0562185483086,43.6108788687828,70.0846878173258,50.3664570260182,59.9761519384762,43.3765284768161,47.3877602635069,50.2861253986795,51.6695499116486,47.928244076725,43.7310565664776,50.0007968699658,48.7492950261453,56.8424401491562,61.5146730457016,46.952500350264,61.3459276946868,42.0554397660588,42.5499173734787,53.6789245134316,46.0928020304213,46.7328825814869,41.2426508987843,55.3179235370556,40.0808716456388,33.7216297667535,68.8824821736627,44.4862127211559,51.1703909841618,37.009103738099,58.9610752866269,52.5295627970849,42.7425504939757,54.2272520544965,41.7731818721505,51.5482084981561,39.7458363447026,55.4819037966458,62.0847325217116,52.7678390079499,58.5988516474045,55.1714118583384,52.8193814445724,41.7995884256485,42.9704630764684,57.2641867416187,53.1501082615327,65.6858526976121,62.4030951698301,46.9942102063916,55.3928491848184,72.3379886614838,71.3539772044699,54.6576823100427,57.2695695086765,70.956869286385,58.1379289407313,63.2642705037403,57.3317374488586,32.7835081299829,61.2229370482507,56.1413377571063,53.3549062326636,54.8782027985905,46.3271501016871,91.5383226642589,65.6453615757837,56.2749213127824,45.2386313713074,48.324269728738,55.1893028543878,52.7920078454061,58.213917150366,64.59726838744,51.7665956122559,44.2219003286043,38.2301221849606,53.1376830989117,75.9846605956824,68.3299500421054,46.0044916014967,63.5923757676696,75.2260081228939,64.6385798239374,48.1757101146743,78.2528130584451,62.9448322519349,55.2174121523765,40.3338693532993,74.1219141883748,41.8672471701971,57.6465929532154,64.4052233426529,60.2362527022626,62.6427825863137,54.3132867344827,57.8231928804619,40.237496308156,50.845621057552,45.6328613585221,66.0243841388562,63.7424734634001,49.4023227353767,58.7592218087206,43.001293115553,54.1230403659215,47.8871738829274,72.9055250131351,49.454767634396,56.8808263542661,54.3845404846844,52.9098523030061,62.5846476958829,57.1279883272587,65.5628868139919,51.5000935032718,56.6648181899508,51.4539890814112,48.5236939563452,64.6835944761806,49.664083741181,69.601714653558,64.1304954012244,67.915016003606,45.2669411976417,53.1440066844341,59.3165586084409,46.5487631606704,58.9750074057627,44.4191070943389,63.8609823251104,65.6904632960339,52.748135838416,33.4485776097717,57.440655110435,34.930875975774,64.584880940615,72.2200096872503,43.2069352253506,66.3778506373015,63.0610967681046,49.49945977465,50.0755794699864,60.6956865225291,56.3276364145066],"text":["pet: dog<br />happiness: 59.32330<br />pet: dog","pet: dog<br />happiness: 62.04701<br />pet: dog","pet: dog<br />happiness: 62.72237<br />pet: dog","pet: dog<br />happiness: 51.08127<br />pet: dog","pet: dog<br />happiness: 57.79214<br />pet: dog","pet: dog<br />happiness: 24.20508<br />pet: dog","pet: dog<br />happiness: 58.48247<br />pet: dog","pet: dog<br />happiness: 65.54864<br />pet: dog","pet: dog<br />happiness: 50.61234<br />pet: dog","pet: dog<br />happiness: 46.10554<br />pet: dog","pet: dog<br />happiness: 58.95950<br />pet: dog","pet: dog<br />happiness: 55.68177<br />pet: dog","pet: dog<br />happiness: 61.59637<br />pet: dog","pet: dog<br />happiness: 48.70588<br />pet: dog","pet: dog<br />happiness: 44.02395<br />pet: dog","pet: dog<br />happiness: 40.97868<br />pet: dog","pet: dog<br />happiness: 41.55616<br />pet: dog","pet: dog<br />happiness: 42.61245<br />pet: dog","pet: dog<br />happiness: 65.67992<br />pet: dog","pet: dog<br />happiness: 56.59545<br />pet: dog","pet: dog<br />happiness: 46.02472<br />pet: dog","pet: dog<br />happiness: 68.95303<br />pet: dog","pet: dog<br />happiness: 46.60006<br />pet: dog","pet: dog<br />happiness: 60.64272<br />pet: dog","pet: dog<br />happiness: 51.01128<br />pet: dog","pet: dog<br />happiness: 51.30688<br />pet: dog","pet: dog<br />happiness: 43.43697<br />pet: dog","pet: dog<br />happiness: 46.86819<br />pet: dog","pet: dog<br />happiness: 54.11903<br />pet: dog","pet: dog<br />happiness: 58.21920<br />pet: dog","pet: dog<br />happiness: 44.68425<br />pet: dog","pet: dog<br />happiness: 77.84591<br />pet: dog","pet: dog<br />happiness: 60.31998<br />pet: dog","pet: dog<br />happiness: 45.15031<br />pet: dog","pet: dog<br />happiness: 33.50115<br />pet: dog","pet: dog<br />happiness: 30.40962<br />pet: dog","pet: dog<br />happiness: 59.39898<br />pet: dog","pet: dog<br />happiness: 63.32790<br />pet: dog","pet: dog<br />happiness: 53.88551<br />pet: dog","pet: dog<br />happiness: 54.73878<br />pet: dog","pet: dog<br />happiness: 74.68285<br />pet: dog","pet: dog<br />happiness: 59.27574<br />pet: dog","pet: dog<br />happiness: 69.74947<br />pet: dog","pet: dog<br />happiness: 56.27748<br />pet: dog","pet: dog<br />happiness: 41.65267<br />pet: dog","pet: dog<br />happiness: 49.26611<br />pet: dog","pet: dog<br />happiness: 61.83355<br />pet: dog","pet: dog<br />happiness: 68.11706<br />pet: dog","pet: dog<br />happiness: 65.88104<br />pet: dog","pet: dog<br />happiness: 49.04444<br />pet: dog","pet: dog<br />happiness: 62.66347<br />pet: dog","pet: dog<br />happiness: 47.50802<br />pet: dog","pet: dog<br />happiness: 54.98483<br />pet: dog","pet: dog<br />happiness: 40.92272<br />pet: dog","pet: dog<br />happiness: 50.94688<br />pet: dog","pet: dog<br />happiness: 55.97136<br />pet: dog","pet: dog<br />happiness: 59.70016<br />pet: dog","pet: dog<br />happiness: 56.26513<br />pet: dog","pet: dog<br />happiness: 50.10015<br />pet: dog","pet: dog<br />happiness: 54.79152<br />pet: dog","pet: dog<br />happiness: 52.80731<br />pet: dog","pet: dog<br />happiness: 46.40540<br />pet: dog","pet: dog<br />happiness: 43.33386<br />pet: dog","pet: dog<br />happiness: 40.74369<br />pet: dog","pet: dog<br />happiness: 52.01917<br />pet: dog","pet: dog<br />happiness: 31.75933<br />pet: dog","pet: dog<br />happiness: 66.27453<br />pet: dog","pet: dog<br />happiness: 65.79191<br />pet: dog","pet: dog<br />happiness: 55.76184<br />pet: dog","pet: dog<br />happiness: 68.09746<br />pet: dog","pet: dog<br />happiness: 49.17925<br />pet: dog","pet: dog<br />happiness: 49.43872<br />pet: dog","pet: dog<br />happiness: 45.84345<br />pet: dog","pet: dog<br />happiness: 77.84427<br />pet: dog","pet: dog<br />happiness: 48.87426<br />pet: dog","pet: dog<br />happiness: 71.48044<br />pet: dog","pet: dog<br />happiness: 59.24229<br />pet: dog","pet: dog<br />happiness: 64.86623<br />pet: dog","pet: dog<br />happiness: 50.06555<br />pet: dog","pet: dog<br />happiness: 44.54240<br />pet: dog","pet: dog<br />happiness: 60.78467<br />pet: dog","pet: dog<br />happiness: 58.81779<br />pet: dog","pet: dog<br />happiness: 61.95704<br />pet: dog","pet: dog<br />happiness: 41.86145<br />pet: dog","pet: dog<br />happiness: 55.68990<br />pet: dog","pet: dog<br />happiness: 47.90046<br />pet: dog","pet: dog<br />happiness: 51.26700<br />pet: dog","pet: dog<br />happiness: 46.18250<br />pet: dog","pet: dog<br />happiness: 59.49883<br />pet: dog","pet: dog<br />happiness: 61.38365<br />pet: dog","pet: dog<br />happiness: 55.76762<br />pet: dog","pet: dog<br />happiness: 61.15826<br />pet: dog","pet: dog<br />happiness: 60.04777<br />pet: dog","pet: dog<br />happiness: 50.31448<br />pet: dog","pet: dog<br />happiness: 51.43266<br />pet: dog","pet: dog<br />happiness: 51.86959<br />pet: dog","pet: dog<br />happiness: 59.65909<br />pet: dog","pet: dog<br />happiness: 69.54796<br />pet: dog","pet: dog<br />happiness: 66.78939<br />pet: dog","pet: dog<br />happiness: 50.93470<br />pet: dog","pet: dog<br />happiness: 62.51116<br />pet: dog","pet: dog<br />happiness: 48.70860<br />pet: dog","pet: dog<br />happiness: 72.40799<br />pet: dog","pet: dog<br />happiness: 36.48366<br />pet: dog","pet: dog<br />happiness: 64.11561<br />pet: dog","pet: dog<br />happiness: 60.71917<br />pet: dog","pet: dog<br />happiness: 52.85846<br />pet: dog","pet: dog<br />happiness: 68.16433<br />pet: dog","pet: dog<br />happiness: 56.93082<br />pet: dog","pet: dog<br />happiness: 56.22735<br />pet: dog","pet: dog<br />happiness: 55.64827<br />pet: dog","pet: dog<br />happiness: 47.14701<br />pet: dog","pet: dog<br />happiness: 56.70592<br />pet: dog","pet: dog<br />happiness: 32.53930<br />pet: dog","pet: dog<br />happiness: 34.76891<br />pet: dog","pet: dog<br />happiness: 58.12706<br />pet: dog","pet: dog<br />happiness: 61.36788<br />pet: dog","pet: dog<br />happiness: 52.82354<br />pet: dog","pet: dog<br />happiness: 49.80080<br />pet: dog","pet: dog<br />happiness: 49.10297<br />pet: dog","pet: dog<br />happiness: 39.67369<br />pet: dog","pet: dog<br />happiness: 45.75027<br />pet: dog","pet: dog<br />happiness: 43.08568<br />pet: dog","pet: dog<br />happiness: 35.19950<br />pet: dog","pet: dog<br />happiness: 65.60332<br />pet: dog","pet: dog<br />happiness: 74.17967<br />pet: dog","pet: dog<br />happiness: 61.48458<br />pet: dog","pet: dog<br />happiness: 50.46603<br />pet: dog","pet: dog<br />happiness: 32.54850<br />pet: dog","pet: dog<br />happiness: 53.08919<br />pet: dog","pet: dog<br />happiness: 45.54372<br />pet: dog","pet: dog<br />happiness: 44.47058<br />pet: dog","pet: dog<br />happiness: 37.90981<br />pet: dog","pet: dog<br />happiness: 31.18989<br />pet: dog","pet: dog<br />happiness: 57.10260<br />pet: dog","pet: dog<br />happiness: 58.27961<br />pet: dog","pet: dog<br />happiness: 50.58297<br />pet: dog","pet: dog<br />happiness: 52.76924<br />pet: dog","pet: dog<br />happiness: 56.47724<br />pet: dog","pet: dog<br />happiness: 72.49314<br />pet: dog","pet: dog<br />happiness: 63.01953<br />pet: dog","pet: dog<br />happiness: 65.92378<br />pet: dog","pet: dog<br />happiness: 42.45243<br />pet: dog","pet: dog<br />happiness: 50.39364<br />pet: dog","pet: dog<br />happiness: 43.16933<br />pet: dog","pet: dog<br />happiness: 42.56979<br />pet: dog","pet: dog<br />happiness: 34.57863<br />pet: dog","pet: dog<br />happiness: 51.69864<br />pet: dog","pet: dog<br />happiness: 47.99828<br />pet: dog","pet: dog<br />happiness: 54.27550<br />pet: dog","pet: dog<br />happiness: 60.22906<br />pet: dog","pet: dog<br />happiness: 45.49126<br />pet: dog","pet: dog<br />happiness: 68.62326<br />pet: dog","pet: dog<br />happiness: 57.42092<br />pet: dog","pet: dog<br />happiness: 72.59802<br />pet: dog","pet: dog<br />happiness: 36.96815<br />pet: dog","pet: dog<br />happiness: 61.30801<br />pet: dog","pet: dog<br />happiness: 55.29330<br />pet: dog","pet: dog<br />happiness: 56.62904<br />pet: dog","pet: dog<br />happiness: 47.51411<br />pet: dog","pet: dog<br />happiness: 60.68190<br />pet: dog","pet: dog<br />happiness: 58.53680<br />pet: dog","pet: dog<br />happiness: 67.37612<br />pet: dog","pet: dog<br />happiness: 49.30820<br />pet: dog","pet: dog<br />happiness: 48.39743<br />pet: dog","pet: dog<br />happiness: 53.88777<br />pet: dog","pet: dog<br />happiness: 61.11089<br />pet: dog","pet: dog<br />happiness: 43.79828<br />pet: dog","pet: dog<br />happiness: 65.35118<br />pet: dog","pet: dog<br />happiness: 51.86517<br />pet: dog","pet: dog<br />happiness: 44.68493<br />pet: dog","pet: dog<br />happiness: 50.32984<br />pet: dog","pet: dog<br />happiness: 37.09721<br />pet: dog","pet: dog<br />happiness: 53.30735<br />pet: dog","pet: dog<br />happiness: 46.30063<br />pet: dog","pet: dog<br />happiness: 52.45894<br />pet: dog","pet: dog<br />happiness: 54.55939<br />pet: dog","pet: dog<br />happiness: 41.70850<br />pet: dog","pet: dog<br />happiness: 48.53710<br />pet: dog","pet: dog<br />happiness: 56.36924<br />pet: dog","pet: dog<br />happiness: 62.99658<br />pet: dog","pet: dog<br />happiness: 55.18484<br />pet: dog","pet: dog<br />happiness: 34.32500<br />pet: dog","pet: dog<br />happiness: 51.02251<br />pet: dog","pet: dog<br />happiness: 65.72573<br />pet: dog","pet: dog<br />happiness: 55.64621<br />pet: dog","pet: dog<br />happiness: 56.75475<br />pet: dog","pet: dog<br />happiness: 64.14009<br />pet: dog","pet: dog<br />happiness: 67.60948<br />pet: dog","pet: dog<br />happiness: 62.70417<br />pet: dog","pet: dog<br />happiness: 49.17831<br />pet: dog","pet: dog<br />happiness: 46.43259<br />pet: dog","pet: dog<br />happiness: 48.94118<br />pet: dog","pet: dog<br />happiness: 43.95424<br />pet: dog","pet: dog<br />happiness: 59.85061<br />pet: dog","pet: dog<br />happiness: 42.39753<br />pet: dog","pet: dog<br />happiness: 69.78872<br />pet: dog","pet: dog<br />happiness: 40.16610<br />pet: dog","pet: dog<br />happiness: 39.88325<br />pet: dog","pet: dog<br />happiness: 65.08605<br />pet: dog","pet: dog<br />happiness: 52.17317<br />pet: dog","pet: dog<br />happiness: 60.51790<br />pet: dog","pet: dog<br />happiness: 49.07871<br />pet: dog","pet: dog<br />happiness: 58.37538<br />pet: dog","pet: dog<br />happiness: 54.14107<br />pet: dog","pet: dog<br />happiness: 27.21576<br />pet: dog","pet: dog<br />happiness: 64.16640<br />pet: dog","pet: dog<br />happiness: 56.82288<br />pet: dog","pet: dog<br />happiness: 55.00943<br />pet: dog","pet: dog<br />happiness: 59.97180<br />pet: dog","pet: dog<br />happiness: 38.82253<br />pet: dog","pet: dog<br />happiness: 64.14874<br />pet: dog","pet: dog<br />happiness: 67.41701<br />pet: dog","pet: dog<br />happiness: 58.68130<br />pet: dog","pet: dog<br />happiness: 37.69514<br />pet: dog","pet: dog<br />happiness: 59.81199<br />pet: dog","pet: dog<br />happiness: 58.15268<br />pet: dog","pet: dog<br />happiness: 60.57642<br />pet: dog","pet: dog<br />happiness: 52.46401<br />pet: dog","pet: dog<br />happiness: 42.49856<br />pet: dog","pet: dog<br />happiness: 54.48793<br />pet: dog","pet: dog<br />happiness: 55.90740<br />pet: dog","pet: dog<br />happiness: 66.57985<br />pet: dog","pet: dog<br />happiness: 44.20362<br />pet: dog","pet: dog<br />happiness: 47.87561<br />pet: dog","pet: dog<br />happiness: 69.22869<br />pet: dog","pet: dog<br />happiness: 55.01869<br />pet: dog","pet: dog<br />happiness: 67.50239<br />pet: dog","pet: dog<br />happiness: 59.04516<br />pet: dog","pet: dog<br />happiness: 66.80103<br />pet: dog","pet: dog<br />happiness: 57.63524<br />pet: dog","pet: dog<br />happiness: 52.83737<br />pet: dog","pet: dog<br />happiness: 65.16305<br />pet: dog","pet: dog<br />happiness: 58.44233<br />pet: dog","pet: dog<br />happiness: 67.25079<br />pet: dog","pet: dog<br />happiness: 54.49421<br />pet: dog","pet: dog<br />happiness: 55.47134<br />pet: dog","pet: dog<br />happiness: 57.57841<br />pet: dog","pet: dog<br />happiness: 46.09947<br />pet: dog","pet: dog<br />happiness: 71.27501<br />pet: dog","pet: dog<br />happiness: 56.81304<br />pet: dog","pet: dog<br />happiness: 52.06410<br />pet: dog","pet: dog<br />happiness: 61.07444<br />pet: dog","pet: dog<br />happiness: 48.95370<br />pet: dog","pet: dog<br />happiness: 71.10815<br />pet: dog","pet: dog<br />happiness: 46.13620<br />pet: dog","pet: dog<br />happiness: 48.24838<br />pet: dog","pet: dog<br />happiness: 66.17119<br />pet: dog","pet: dog<br />happiness: 74.66711<br />pet: dog","pet: dog<br />happiness: 39.18803<br />pet: dog","pet: dog<br />happiness: 49.16217<br />pet: dog","pet: dog<br />happiness: 46.83033<br />pet: dog","pet: dog<br />happiness: 49.87883<br />pet: dog","pet: dog<br />happiness: 53.01481<br />pet: dog","pet: dog<br />happiness: 51.23562<br />pet: dog","pet: dog<br />happiness: 39.57348<br />pet: dog","pet: dog<br />happiness: 48.62010<br />pet: dog","pet: dog<br />happiness: 55.81128<br />pet: dog","pet: dog<br />happiness: 43.41399<br />pet: dog","pet: dog<br />happiness: 55.54940<br />pet: dog","pet: dog<br />happiness: 53.12184<br />pet: dog","pet: dog<br />happiness: 57.77342<br />pet: dog","pet: dog<br />happiness: 59.19296<br />pet: dog","pet: dog<br />happiness: 51.97557<br />pet: dog","pet: dog<br />happiness: 58.87284<br />pet: dog","pet: dog<br />happiness: 59.68240<br />pet: dog","pet: dog<br />happiness: 70.95379<br />pet: dog","pet: dog<br />happiness: 58.03993<br />pet: dog","pet: dog<br />happiness: 53.76032<br />pet: dog","pet: dog<br />happiness: 42.61642<br />pet: dog","pet: dog<br />happiness: 85.33929<br />pet: dog","pet: dog<br />happiness: 80.51911<br />pet: dog","pet: dog<br />happiness: 10.79059<br />pet: dog","pet: dog<br />happiness: 53.73246<br />pet: dog","pet: dog<br />happiness: 38.82797<br />pet: dog","pet: dog<br />happiness: 72.50691<br />pet: dog","pet: dog<br />happiness: 60.95025<br />pet: dog","pet: dog<br />happiness: 39.77336<br />pet: dog","pet: dog<br />happiness: 64.48655<br />pet: dog","pet: dog<br />happiness: 46.11495<br />pet: dog","pet: dog<br />happiness: 83.06898<br />pet: dog","pet: dog<br />happiness: 65.91749<br />pet: dog","pet: dog<br />happiness: 58.89682<br />pet: dog","pet: dog<br />happiness: 68.82097<br />pet: dog","pet: dog<br />happiness: 64.18006<br />pet: dog","pet: dog<br />happiness: 74.27491<br />pet: dog","pet: dog<br />happiness: 63.18916<br />pet: dog","pet: dog<br />happiness: 61.03253<br />pet: dog","pet: dog<br />happiness: 50.38805<br />pet: dog","pet: dog<br />happiness: 40.21115<br />pet: dog","pet: dog<br />happiness: 64.77476<br />pet: dog","pet: dog<br />happiness: 53.04962<br />pet: dog","pet: dog<br />happiness: 61.72658<br />pet: dog","pet: dog<br />happiness: 63.44017<br />pet: dog","pet: dog<br />happiness: 60.84400<br />pet: dog","pet: dog<br />happiness: 58.64830<br />pet: dog","pet: dog<br />happiness: 52.73424<br />pet: dog","pet: dog<br />happiness: 43.32400<br />pet: dog","pet: dog<br />happiness: 57.16437<br />pet: dog","pet: dog<br />happiness: 53.90046<br />pet: dog","pet: dog<br />happiness: 54.84934<br />pet: dog","pet: dog<br />happiness: 51.99219<br />pet: dog","pet: dog<br />happiness: 76.14134<br />pet: dog","pet: dog<br />happiness: 62.57617<br />pet: dog","pet: dog<br />happiness: 56.86837<br />pet: dog","pet: dog<br />happiness: 49.64790<br />pet: dog","pet: dog<br />happiness: 47.50822<br />pet: dog","pet: dog<br />happiness: 65.23547<br />pet: dog","pet: dog<br />happiness: 31.61552<br />pet: dog","pet: dog<br />happiness: 48.26826<br />pet: dog","pet: dog<br />happiness: 54.81163<br />pet: dog","pet: dog<br />happiness: 54.87048<br />pet: dog","pet: dog<br />happiness: 47.30996<br />pet: dog","pet: dog<br />happiness: 48.55781<br />pet: dog","pet: dog<br />happiness: 71.88730<br />pet: dog","pet: dog<br />happiness: 54.92733<br />pet: dog","pet: dog<br />happiness: 41.96538<br />pet: dog","pet: dog<br />happiness: 68.67518<br />pet: dog","pet: dog<br />happiness: 61.08249<br />pet: dog","pet: dog<br />happiness: 36.80276<br />pet: dog","pet: dog<br />happiness: 62.01875<br />pet: dog","pet: dog<br />happiness: 55.35476<br />pet: dog","pet: dog<br />happiness: 58.32640<br />pet: dog","pet: dog<br />happiness: 46.96990<br />pet: dog","pet: dog<br />happiness: 67.30933<br />pet: dog","pet: dog<br />happiness: 48.74223<br />pet: dog","pet: dog<br />happiness: 48.53062<br />pet: dog","pet: dog<br />happiness: 35.13009<br />pet: dog","pet: dog<br />happiness: 43.82016<br />pet: dog","pet: dog<br />happiness: 59.99082<br />pet: dog","pet: dog<br />happiness: 53.94724<br />pet: dog","pet: dog<br />happiness: 49.39683<br />pet: dog","pet: dog<br />happiness: 56.99978<br />pet: dog","pet: dog<br />happiness: 75.14811<br />pet: dog","pet: dog<br />happiness: 58.94322<br />pet: dog","pet: dog<br />happiness: 61.09629<br />pet: dog","pet: dog<br />happiness: 62.20765<br />pet: dog","pet: dog<br />happiness: 47.74216<br />pet: dog","pet: dog<br />happiness: 52.42548<br />pet: dog","pet: dog<br />happiness: 67.27880<br />pet: dog","pet: dog<br />happiness: 48.53974<br />pet: dog","pet: dog<br />happiness: 57.09967<br />pet: dog","pet: dog<br />happiness: 61.38522<br />pet: dog","pet: dog<br />happiness: 54.88440<br />pet: dog","pet: dog<br />happiness: 49.00829<br />pet: dog","pet: dog<br />happiness: 57.76558<br />pet: dog","pet: dog<br />happiness: 59.99413<br />pet: dog","pet: dog<br />happiness: 45.75905<br />pet: dog","pet: dog<br />happiness: 63.11576<br />pet: dog","pet: dog<br />happiness: 64.01348<br />pet: dog","pet: dog<br />happiness: 57.46073<br />pet: dog","pet: dog<br />happiness: 64.35792<br />pet: dog","pet: dog<br />happiness: 55.49775<br />pet: dog","pet: dog<br />happiness: 40.12752<br />pet: dog","pet: dog<br />happiness: 47.03081<br />pet: dog","pet: dog<br />happiness: 54.05622<br />pet: dog","pet: dog<br />happiness: 43.61088<br />pet: dog","pet: dog<br />happiness: 70.08469<br />pet: dog","pet: dog<br />happiness: 50.36646<br />pet: dog","pet: dog<br />happiness: 59.97615<br />pet: dog","pet: dog<br />happiness: 43.37653<br />pet: dog","pet: dog<br />happiness: 47.38776<br />pet: dog","pet: dog<br />happiness: 50.28613<br />pet: dog","pet: dog<br />happiness: 51.66955<br />pet: dog","pet: dog<br />happiness: 47.92824<br />pet: dog","pet: dog<br />happiness: 43.73106<br />pet: dog","pet: dog<br />happiness: 50.00080<br />pet: dog","pet: dog<br />happiness: 48.74930<br />pet: dog","pet: dog<br />happiness: 56.84244<br />pet: dog","pet: dog<br />happiness: 61.51467<br />pet: dog","pet: dog<br />happiness: 46.95250<br />pet: dog","pet: dog<br />happiness: 61.34593<br />pet: dog","pet: dog<br />happiness: 42.05544<br />pet: dog","pet: dog<br />happiness: 42.54992<br />pet: dog","pet: dog<br />happiness: 53.67892<br />pet: dog","pet: dog<br />happiness: 46.09280<br />pet: dog","pet: dog<br />happiness: 46.73288<br />pet: dog","pet: dog<br />happiness: 41.24265<br />pet: dog","pet: dog<br />happiness: 55.31792<br />pet: dog","pet: dog<br />happiness: 40.08087<br />pet: dog","pet: dog<br />happiness: 33.72163<br />pet: dog","pet: dog<br />happiness: 68.88248<br />pet: dog","pet: dog<br />happiness: 44.48621<br />pet: dog","pet: dog<br />happiness: 51.17039<br />pet: dog","pet: dog<br />happiness: 37.00910<br />pet: dog","pet: dog<br />happiness: 58.96108<br />pet: dog","pet: dog<br />happiness: 52.52956<br />pet: dog","pet: dog<br />happiness: 42.74255<br />pet: dog","pet: dog<br />happiness: 54.22725<br />pet: dog","pet: dog<br />happiness: 41.77318<br />pet: dog","pet: dog<br />happiness: 51.54821<br />pet: dog","pet: dog<br />happiness: 39.74584<br />pet: dog","pet: dog<br />happiness: 55.48190<br />pet: dog","pet: dog<br />happiness: 62.08473<br />pet: dog","pet: dog<br />happiness: 52.76784<br />pet: dog","pet: dog<br />happiness: 58.59885<br />pet: dog","pet: dog<br />happiness: 55.17141<br />pet: dog","pet: dog<br />happiness: 52.81938<br />pet: dog","pet: dog<br />happiness: 41.79959<br />pet: dog","pet: dog<br />happiness: 42.97046<br />pet: dog","pet: dog<br />happiness: 57.26419<br />pet: dog","pet: dog<br />happiness: 53.15011<br />pet: dog","pet: dog<br />happiness: 65.68585<br />pet: dog","pet: dog<br />happiness: 62.40310<br />pet: dog","pet: dog<br />happiness: 46.99421<br />pet: dog","pet: dog<br />happiness: 55.39285<br />pet: dog","pet: dog<br />happiness: 72.33799<br />pet: dog","pet: dog<br />happiness: 71.35398<br />pet: dog","pet: dog<br />happiness: 54.65768<br />pet: dog","pet: dog<br />happiness: 57.26957<br />pet: dog","pet: dog<br />happiness: 70.95687<br />pet: dog","pet: dog<br />happiness: 58.13793<br />pet: dog","pet: dog<br />happiness: 63.26427<br />pet: dog","pet: dog<br />happiness: 57.33174<br />pet: dog","pet: dog<br />happiness: 32.78351<br />pet: dog","pet: dog<br />happiness: 61.22294<br />pet: dog","pet: dog<br />happiness: 56.14134<br />pet: dog","pet: dog<br />happiness: 53.35491<br />pet: dog","pet: dog<br />happiness: 54.87820<br />pet: dog","pet: dog<br />happiness: 46.32715<br />pet: dog","pet: dog<br />happiness: 91.53832<br />pet: dog","pet: dog<br />happiness: 65.64536<br />pet: dog","pet: dog<br />happiness: 56.27492<br />pet: dog","pet: dog<br />happiness: 45.23863<br />pet: dog","pet: dog<br />happiness: 48.32427<br />pet: dog","pet: dog<br />happiness: 55.18930<br />pet: dog","pet: dog<br />happiness: 52.79201<br />pet: dog","pet: dog<br />happiness: 58.21392<br />pet: dog","pet: dog<br />happiness: 64.59727<br />pet: dog","pet: dog<br />happiness: 51.76660<br />pet: dog","pet: dog<br />happiness: 44.22190<br />pet: dog","pet: dog<br />happiness: 38.23012<br />pet: dog","pet: dog<br />happiness: 53.13768<br />pet: dog","pet: dog<br />happiness: 75.98466<br />pet: dog","pet: dog<br />happiness: 68.32995<br />pet: dog","pet: dog<br />happiness: 46.00449<br />pet: dog","pet: dog<br />happiness: 63.59238<br />pet: dog","pet: dog<br />happiness: 75.22601<br />pet: dog","pet: dog<br />happiness: 64.63858<br />pet: dog","pet: dog<br />happiness: 48.17571<br />pet: dog","pet: dog<br />happiness: 78.25281<br />pet: dog","pet: dog<br />happiness: 62.94483<br />pet: dog","pet: dog<br />happiness: 55.21741<br />pet: dog","pet: dog<br />happiness: 40.33387<br />pet: dog","pet: dog<br />happiness: 74.12191<br />pet: dog","pet: dog<br />happiness: 41.86725<br />pet: dog","pet: dog<br />happiness: 57.64659<br />pet: dog","pet: dog<br />happiness: 64.40522<br />pet: dog","pet: dog<br />happiness: 60.23625<br />pet: dog","pet: dog<br />happiness: 62.64278<br />pet: dog","pet: dog<br />happiness: 54.31329<br />pet: dog","pet: dog<br />happiness: 57.82319<br />pet: dog","pet: dog<br />happiness: 40.23750<br />pet: dog","pet: dog<br />happiness: 50.84562<br />pet: dog","pet: dog<br />happiness: 45.63286<br />pet: dog","pet: dog<br />happiness: 66.02438<br />pet: dog","pet: dog<br />happiness: 63.74247<br />pet: dog","pet: dog<br />happiness: 49.40232<br />pet: dog","pet: dog<br />happiness: 58.75922<br />pet: dog","pet: dog<br />happiness: 43.00129<br />pet: dog","pet: dog<br />happiness: 54.12304<br />pet: dog","pet: dog<br />happiness: 47.88717<br />pet: dog","pet: dog<br />happiness: 72.90553<br />pet: dog","pet: dog<br />happiness: 49.45477<br />pet: dog","pet: dog<br />happiness: 56.88083<br />pet: dog","pet: dog<br />happiness: 54.38454<br />pet: dog","pet: dog<br />happiness: 52.90985<br />pet: dog","pet: dog<br />happiness: 62.58465<br />pet: dog","pet: dog<br />happiness: 57.12799<br />pet: dog","pet: dog<br />happiness: 65.56289<br />pet: dog","pet: dog<br />happiness: 51.50009<br />pet: dog","pet: dog<br />happiness: 56.66482<br />pet: dog","pet: dog<br />happiness: 51.45399<br />pet: dog","pet: dog<br />happiness: 48.52369<br />pet: dog","pet: dog<br />happiness: 64.68359<br />pet: dog","pet: dog<br />happiness: 49.66408<br />pet: dog","pet: dog<br />happiness: 69.60171<br />pet: dog","pet: dog<br />happiness: 64.13050<br />pet: dog","pet: dog<br />happiness: 67.91502<br />pet: dog","pet: dog<br />happiness: 45.26694<br />pet: dog","pet: dog<br />happiness: 53.14401<br />pet: dog","pet: dog<br />happiness: 59.31656<br />pet: dog","pet: dog<br />happiness: 46.54876<br />pet: dog","pet: dog<br />happiness: 58.97501<br />pet: dog","pet: dog<br />happiness: 44.41911<br />pet: dog","pet: dog<br />happiness: 63.86098<br />pet: dog","pet: dog<br />happiness: 65.69046<br />pet: dog","pet: dog<br />happiness: 52.74814<br />pet: dog","pet: dog<br />happiness: 33.44858<br />pet: dog","pet: dog<br />happiness: 57.44066<br />pet: dog","pet: dog<br />happiness: 34.93088<br />pet: dog","pet: dog<br />happiness: 64.58488<br />pet: dog","pet: dog<br />happiness: 72.22001<br />pet: dog","pet: dog<br />happiness: 43.20694<br />pet: dog","pet: dog<br />happiness: 66.37785<br />pet: dog","pet: dog<br />happiness: 63.06110<br />pet: dog","pet: dog<br />happiness: 49.49946<br />pet: dog","pet: dog<br />happiness: 50.07558<br />pet: dog","pet: dog<br />happiness: 60.69569<br />pet: dog","pet: dog<br />happiness: 56.32764<br />pet: dog"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(0,191,196,1)","opacity":1,"size":7.55905511811024,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)"}},"hoveron":"points","name":"dog","legendgroup":"dog","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":26.2283105022831,"r":7.30593607305936,"b":40.1826484018265,"l":37.2602739726027},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[0.4,2.6],"tickmode":"array","ticktext":["cat","dog"],"tickvals":[1,2],"categoryorder":"array","categoryarray":["cat","dog"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"y","title":{"text":"pet","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[6.75320472178878,95.575709232948],"tickmode":"array","ticktext":["25","50","75"],"tickvals":[25,50,75],"categoryorder":"array","categoryarray":["25","50","75"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.66417600664176,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176,"zeroline":false,"anchor":"x","title":{"text":"happiness","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(51,51,51,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"y":0.913385826771654},"annotations":[{"text":"pet","x":1.02,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"left","yanchor":"bottom","legendTitle":true}],"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"17c51779c03f9":{"x":{},"y":{},"fill":{},"type":"scatter"}},"cur_data":"17c51779c03f9","visdat":{"17c51779c03f9":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
<p class="caption">(\#fig:plotly)Interactive graph using plotly</p>
</div>

<div class="info">
<p>Hover over the data points above and click on the legend items.</p>
</div>

## Quiz {#ggplot-quiz}

1. Generate a plot like this from the built-in dataset `iris`. Make sure to include the custom axis labels.

    <img src="03-ggplot_files/figure-html/quiz-boxplot-1.png" width="100%" style="display: block; margin: auto;" />
    
    
    <div class='solution'><button class=''>Solution</button>
    
    ```r
    ggplot(iris, aes(Species, Petal.Width, fill = Species)) +
      geom_boxplot(show.legend = FALSE) +
      xlab("Flower Species") +
      ylab("Petal Width (in cm)")
    
    # there are many ways to do things, the code below is also correct
    ggplot(iris) +
      geom_boxplot(aes(Species, Petal.Width, fill = Species), show.legend = FALSE) +
      labs(x = "Flower Species",
           y = "Petal Width (in cm)")
    ```
    
    
    </div>


2. You have just created a plot using the following code. How do you save it?
    
    ```r
    ggplot(cars, aes(speed, dist)) + 
      geom_point() +
      geom_smooth(method = lm)
    ```
    
    <pre class="mcq"><select class='solveme' name='q_1' data-answer='["ggsave(\"figname.png\")"]'> <option></option> <option>ggsave()</option> <option>ggsave("figname")</option> <option>ggsave("figname.png")</option> <option>ggsave("figname.png", plot = cars)</option></select></pre>

  
3. Debug the following code.
    
    ```r
    ggplot(iris) +
      geom_point(aes(Petal.Width, Petal.Length, colour = Species)) +
      geom_smooth(method = lm) +
      facet_grid(Species)
    ```
    
    
    <div class='solution'><button class=''>Solution</button>
    
    ```r
    ggplot(iris, aes(Petal.Width, Petal.Length, colour = Species)) +
      geom_point() +
      geom_smooth(method = lm) +
      facet_grid(~Species)
    ```
    
    ```
    ## `geom_smooth()` using formula 'y ~ x'
    ```
    
    <img src="03-ggplot_files/figure-html/quiz-debug-answer-1.png" width="100%" style="display: block; margin: auto;" />
    </div>


4. Generate a plot like this from the built-in dataset `ChickWeight`.  

    <img src="03-ggplot_files/figure-html/unnamed-chunk-14-1.png" width="100%" style="display: block; margin: auto;" />
    
    
    <div class='solution'><button class=''>Solution</button>
    
    ```r
    ggplot(ChickWeight, aes(weight, Time)) +
      geom_hex(binwidth = c(10, 1)) +
      scale_fill_viridis_c()
    ```
    
    
    </div>

    
5. Generate a plot like this from the built-in dataset `iris`.

    
    ```
    ## `geom_smooth()` using formula 'y ~ x'
    ```
    
    <img src="03-ggplot_files/figure-html/quiz-cowplot-1.png" width="100%" style="display: block; margin: auto;" />
    
    
    <div class='solution'><button class=''>Solution</button>
    
    ```r
    pw <- ggplot(iris, aes(Petal.Width, color = Species)) +
      geom_density() +
      xlab("Petal Width (in cm)")
    
    pl <- ggplot(iris, aes(Petal.Length, color = Species)) +
      geom_density() +
      xlab("Petal Length (in cm)") +
      coord_flip()
    
    pw_pl <- ggplot(iris, aes(Petal.Width, Petal.Length, color = Species)) +
      geom_point() +
      geom_smooth(method = lm) +
      xlab("Petal Width (in cm)") +
      ylab("Petal Length (in cm)")
    
    cowplot::plot_grid(
      pw, pl, pw_pl, 
      labels = c("A", "B", "C"),
      nrow = 3
    )
    ```
    
    
    </div>

## Exercises

Download the [exercises](exercises/03_ggplot_exercise.Rmd). See the [plots](exercises/03_ggplot_answers.html) to see what your plots should look like (this doesn't contain the answer code). See the [answers](exercises/03_ggplot_answers.Rmd) only after you've attempted all the questions.


```r
# run this to access the exercise
dataskills::exercise(3)

# run this to access the answers
dataskills::exercise(3, answers = TRUE)
```