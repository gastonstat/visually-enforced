---
layout: post
title: "Density Curves"
date: 2014-08-16
category: how-to
tags: [density, plot]
image: density-plot-post.png
---

The default graphical display of most plotting functions in R is very limited (and usually not very pretty). But that doesn't mean that we should conform with those crude figures.

<!--more-->

<img src="{{ site.url }}/images/blog/density-plot-post.png">


### A Basic Plot Example

For this post I'll use a simple example by plotting density curves of some random generated variables. For instance, let's generate three normal distributions with different parameters, and their corresponding estimated densities:


{% highlight r %}
# setting seed for random numbers
set.seed(1111)

# random normal variables
var1 = rnorm(500)
var2 = rnorm(500, 1, 1.5)
var3 = rnorm(500, -1, 1.5)

# densities
den1 = density(var1)
den2 = density(var2)
den3 = density(var3)
{% endhighlight %}



We can plot each of the densities with the function ```plot()``` which displays a very raw figure. These displays are good for quick visual inspection, but they are very limited for anything else (especially if we plan to include them in some slides for presentation purposes)


{% highlight r %}
# individual density plots
plot(den1)
plot(den2)
plot(den3)
{% endhighlight %}



<img src="{{ site.url }}/figs/2014-08-16-Density-Curves/plot-density1.png" />


<img src="{{ site.url }}/figs/2014-08-16-Density-Curves/plot-density2.png" />


<img src="{{ site.url }}/figs/2014-08-16-Density-Curves/plot-density3.png" />



### Improved Plots

Of course, we can write some lines of code to substantially improve our graphics. First, let's define some colors for each distribution using the function ```hsv()``` 


{% highlight r %}
# colors for each distribution
col1 = hsv(h = 0.65, s = 0.6, v = 0.8, alpha = 0.5)
col2 = hsv(h = 0.85, s = 0.6, v = 0.8, alpha = 0.5)
col3 = hsv(h = 0.55, s = 0.6, v = 0.8, alpha = 0.5)
{% endhighlight %}


And now let's combine them in a single graphic tweaking the right parameters to produce a visually aesthetic plot:

{% highlight r %}
# set graphic margins
op = par(mar = c(3, 3, 2, 2))
# call new plot
plot.new()
# plot window
plot.window(xlim = c(-6, 6), ylim = c(0, 0.4))
# add axes
axis(side = 1, pos = 0, at = seq(from = -6, to = 6, by = 2), col = "gray20", 
    lwd.ticks = 0.25, cex.axis = 1, col.axis = "gray20", lwd = 1.5)
axis(side = 2, pos = -6, at = seq(from = 0, to = 0.4, by = 0.1), col = "gray20", 
    las = 2, lwd.ticks = 0.5, cex.axis = 1, col.axis = "gray20", lwd = 1.5)
# density 1
polygon(den1$x, den1$y, col = col1, border = col1)
# density 2
polygon(den2$x, den2$y, col = col2, border = col2)
# density 3
polygon(den3$x, den3$y, col = col3, border = col3)
# add legends
text(1.3, 0.35, labels = "Normal(0, 1)", col = "gray30", cex = 0.9)
text(3.8, 0.15, labels = "Normal(1, 1.5)", col = "gray30", cex = 0.9)
text(-3.6, 0.15, labels = "Normal(-1, 1.5)", col = "gray30", cex = 0.9)
# turn off par
par(op)
{% endhighlight %}



<img src="{{ site.url }}/figs/2014-08-16-Density-Curves/density-plot.png" />



### Using ggplot2

We can also use the package ```"ggplot2"``` to obtain a similar graphic. Here's how:


{% highlight r %}
# data frame with normal variables
distribs = data.frame(
  values = c(var1, var2, var3),
  type = gl(n = 3, k = 500))

# load ggplot2
library(ggplot2)

# plot
ggplot(data = distribs, aes(x = values, group = type)) +
  geom_density(aes(fill = type, color = type), alpha = 0.5)
{% endhighlight %}

<img src="{{ site.url }}/figs/2014-08-16-Density-Curves/density-ggplot.png" />




