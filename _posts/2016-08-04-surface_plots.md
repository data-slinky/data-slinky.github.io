---
layout: post
title: "Surface plots... in R!"
date: 2016-08-04
description: It's 3D this time and not d3. Hah.
---

Part of project: [Slinky Projects](http://data-slinky.com/project/4_Slinky_projects/)

At one point in my life, I was a huge fan of Matlab for visualization. Then, I saw the 
light at the end of the tunnel and repented for the errors of my ways. Okay, too dramatic. 

A friend of mine in economics gave me some Matlab code to make 3d plots for one of his 
paper and I instantly thought that Matlab, as a language, was horrendous. Note, I still 
greatly appreciate it as a software package since I relied heavily on it for my undergrad 
and masters theses (those 500+ lines of code to solve some PDEs seems like gibberish now).

Anyhow, rather than deciphering it and to make it work on my machine (disclaimer: Matlab 
via a virtual machine took like 10 minutes to open so I gave up), I decided it would be 
must faster to use R. Apparently, the result looks the same, the lines of R code is 
seems slightly less, and the code is easier to understand and play with.

<figure>
 <a href="/img/surface_plots.png" data-lightbox="appfoundry_image_set" data-title="Source: 
 Some economics data">
  <img src="/img/surface_plots.png" alt="Bump chart" style="max-width:100%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>
 
There are numerous packages in R to do surface plots. You can do interactive 3d plots with 
[plotly](https://plot.ly/r/3d-surface-plots/), 
[plot3D](https://cran.r-project.org/web/packages/plot3D/vignettes/plot3D.pdf) which is an 
extension of the base _persp_ function or 
[lattice](https://cran.r-project.org/web/packages/lattice/index.html) which is another 
extension of R's base graphics capabilities. I decided to go with lattice since I never 
used it before.

The dataset is some random economics data which I have no clue about. I just know that 
there is an x,y, z1, and z2 axis. After reading in the data, we can plot with the 
following snippet.

```R
library(lattice)
library(gridExtra)

trellis.par.set("axis.line", list(col=NA,lty=1,lwd=1))
jobsat_plot <- wireframe(z1 ~ x*y, data=some_economics_data, 
          xlab = "custom X label",
          ylab = "custom y label",
          main = "Jobsat",
          drape = TRUE,
          colorkey = TRUE,
          scales = list(arrows=FALSE,cex=.5, tick.number = 10, z = list(arrows=F), distance =c(1.5, 1.5, 1.5)),
          light.source = c(10,0,10),
          col.regions = rainbow(100, s = 1, v = 1, start = 0, end = max(1,100 - 1)/100, alpha = .8),
          screen = list(z = -60, x = -60)
)
```
Surface plots with lattice can be done with the _wireframe_ function. The first line after 
loading the package is to remove the border around the plot. Everything else is pretty 
explanatory. The option _drape_ is a boolean to drape the plot or not, the _colorkey_ is 
another boolean which determines if you want a key showing the density on the side. 
_scales_ is used to create the ticks. Note, if _arrows_ is set to true, then you have no 
ticks. I like how we can easily determine the margin between the axis label with a 
_distance option_. You can play around with the aesthetics with _light.source_ and 
_col.region_. I decided to go with the typical rainbow color to better show the depths. 
You can pass a list of colors you want if you need to customize.

One final note is that I decided to use the _grid.arrange_ function from the package 
_gridExtra_ to print side-by-side plot. I think you go with the usual pars option to do 
the same thing but this one-liner is much neater.

```R
grid.arrange(jobsat_plot, orgpractices_plot, ncol=2, clip=TRUE)
```