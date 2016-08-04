---
layout: post
title: "Bump charts"
date: 2016-07-31
description: I like bump charts. Here's a simple but nice looking one using R + ggplot.
---

Part of project: [College Rankings](http://data-slinky.com/project/3_College_rankings/)

I remain highly engrossed with data visualizations lately so I've been reading about 
different charts. One of my favorite chart is a sort of line chart where the x and y-axis 
are discrete. These sort of charts are common in showing changes in ranks 
over time. I did not know what they were called until I stumbled on Edwin Tufte's 
[books](http://www.edwardtufte.com/bboard/q-and-a-fetch-msg?msg_id=0003nk) which 
reference them as __bump charts__.

A bump chart is a perfect visualization for college rankings. I decided to make one in R 
before trying any fancy stuff. The end product is this.

<figure>
 <a href="/img/Times_Top20.png" data-lightbox="appfoundry_image_set" data-title="Source: 
 Times Higher Education World University Rankings">
  <img src="/img/Times_Top20.png" alt="Bump chart" style="max-width:100%;"/>
</a>
 <div class="col caption">Click image to view full-size. </div>
 </figure>

I'll walkthrough the code and then provide a brief discussion future extension.

# Making a bump chart in R

The first part of the code involves loading the required packages and processing the data.

```R
library(ggplot2)
library(ggrepel)
library(scales)
library(RColorBrewer)

timesData <- read.csv("data/timesData.csv", sep=",") # Load in the data
timesData$world_rank <- as.numeric(as.character(timesData$world_rank))
timesData_top <- subset(timesData, world_rank <= 20)[c("year","university_name", "world_rank")] 
timesData_top$university_name <- abbreviate(timesData_top$university_name,25)
timesfinalYear <- timesData_top[which(timesData_top$year == 2016),]
timesfirstAppearance <- timesData_top[!duplicated(timesData_top$university_name) & timesData_top$year != 2016,]
```

We will use `ggplot2` for the plotting, `ggrepel` to create non-overlapping labels, 
`scales` to create even axes, and `RColorBrewer` to define a custom color palette.

I first load the data and cast the ranks as a numeric instead of a factor data type in 
order to better process later. I subset the top 20 colleges for every year and then use
`abbreviate` to abbreviate the names since some were way too long (I later decided to 
change the names manually since the abbreviations made it difficult to decipher the 
school). For labeling, I want to label when the school first appears in the top 20 and 
the most recent year. The reason for the former is that some schools like Northwestern 
appears in the top 20 just once and other like Duke did not appear until later years. As 
for the latter, it is good to know which schools are currently in the top 20.

```R
pal1 = c("#c57c3c", "#e392c2", "#a5e7a8", "#bea3ea", "#d7e298", "#81a4e3", "#a6b16a", "#a7baf2", "#e4c587", "#5ab6e6",
         "#d6a16d", "#62d9f3", "#eb9189", "#3ec1c8", "#e1a6b6", "#7fe3c5", "#e5b4e2", "#8bba83", "#cd5136", "#84bb9c",
         "#e1ceeb", "#72b7b0", "#cd9e8c", "#93e7e2", "#ecc0b1", "#7bb1c6", "#d8e8c5", "#acbadd", "#b2b593", "#acd8eb")
```

While ggplot has its own default palette which you can change with some pre-defined sets,
I decided to pick my own since. You can define your own palette by storing a list of hex 
color codes and then passing them to ggplot. I recommend checking out 
[iWantHue](https://github.com/medialab/iwanthue) which uses k-means to generate a set
of nice colors. Now, lets make the plot.

```R
g1 <- ggplot(data=timesData_top, aes(x=year, y=world_rank)) + 
  geom_line(aes(colour=university_name), size=1.5) + 
  geom_point(shape = 21, stroke = 2, size=5, fill = "white", aes(colour=university_name)) + 
  geom_label_repel(data = timesfirstAppearance, aes(label=university_name), size=3, fontface = "bold", color='#2f2f2f') +
  geom_label(data = timesfinalYear, aes(x=year, y = world_rank, label=university_name), size=3, fontface = "bold", color='#2f2f2f', hjust=0) +
  scale_y_reverse(lim=c(20,1), breaks = scales::pretty_breaks(n = 20)) +
  scale_x_continuous(expand = c(.12, .12), breaks = scales::pretty_breaks(n = 5)) +
  ggtitle('Times World Universities Ranking') +
  xlab(NULL) +
  ylab("World Rank") +
  theme_minimal() +
  theme_bw() +
  scale_colour_manual(values=pal1) + 
  theme(panel.background = element_rect(fill = '#ffffff'),
    plot.title = element_text(size=14), legend.title=element_blank(),
    axis.text = element_text(size=11), axis.title=element_text(size=11), 
    panel.border = element_blank(), legend.position='none', 
    panel.grid.minor.x = element_blank(), panel.grid.minor.y = element_blank(),
    axis.ticks.x=element_blank(), axis.ticks.y=element_blank())
g1
```

I love ggplot in that defining layer elements are very straightforward. I first create the
plot instance in which the x-axis is year and the y-axis is world ranking. I want to have 
points for each of the schools in the rankings as well as lines which are colored based on
the school name.  I used `geom_label_repel` from the `ggrepel` package to create 
non-overlapping labels for the first appearance. Some schools are tied so you would get 
overlaps with `geom_label`. I used `geom_label` for the ending points since there are no
ties for 2016 with left-alignment with `hjust=0`.

I reversed the y-axis since it would make more sense with the top schools at the top of 
the chart. I also scaled the x-axis by a bit to fit the 2016 labels in the graph. There are 
other ways of fitting the labels such as setting off chart clipping but this results in very 
slow rendering. Both the y-axis and x-axis was break with `pretty_breaks` which creates 
evenly spaced bins. The rest of the code is just changing some of the aesthetics of the 
charts including using `scale_colour_manual` to load my custom palette.

# Combining different rankings?

As you can see, it is relatively easy to create a bump chart using R. However, since I have 
three different college rankings, it would be nice if I can display all three rankings in
one visual instead of three separate bump charts. I'm still researching of some type of
visualization that will allow me to combined the rankings. If nothing comes up, I can 
conjure up a d3.js-based bump chart that will allow data selection. More visualizations 
for this project are currently in the works so stay tuned!
