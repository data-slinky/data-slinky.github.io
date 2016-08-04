---
layout: post
title: "Integrated bump charts web app"
date: 2016-08-01
description: A web app using R-shiny. Select your own dataset and set your own parameter!
---

Part of project: [College Rankings](http://data-slinky.com/project/3_College_rankings/)

<p align="center">
 <a href="https://data-slinky.shinyapps.io/BumpCharts/" target="_blank">
  <img src="/img/BumpChartsApp.png" alt="Bump chart" style="max-width:80%;"/>
</a>
 <div class="col caption">Click image to go to the application. </div>
</p>

In my previous [post]({% post_url 2016-07-31-bump_charts %}), I talked about how to create 
a simple bump chart using R with ggplot. I was looking at how to best visualize different 
ranking sources in one chart but never settled on a proper chart. I decided to let the 
type of ranking be selected by the user in addition to the number of colleges to display.

The web application was created using R with shiny. I really like how shiny allows you to 
customize the UI using css. A screenshot is shown above and you can access it 
[here](https://data-slinky.shinyapps.io/BumpCharts/).

Shiny is an R package to make web applications. It essentially consists of the interaction 
of two different files, __ui.R__ and __server.R__. Lets discuss the code in brief.

# ui.R

The interface of the app is created with this script. I like the option of using your own 
css file with `includeCSS`. You can get some nice bootstrap themes from 
[bootswatch](https://bootswatch.com/).

```R
library(shiny)

shinyUI(fluidPage(
  includeCSS("bootstrap.css"),
  hr(),
  # Output the plot from the server
  plotOutput("plot1", width = "1200"),
  hr(),
  
  fluidRow(
    
    column(3, titlePanel("College Rankings Bump Charts"), 
           mainPanel("by", a("data-slinky", href="http://data-slinky.com/project/3_College_rankings/"))),
    
    column(4, selectInput("data", "Dataset:",
                c("Times Higher Education" = "timesData",
                  "Center for World University Rankings" = "cwurData",
                  "Shanghai Rankings" = "shanghaiData"), selected = "timesData")
    ),
    column(5, sliderInput("var1", "Top N schools:", min=1, max=20, value=10)
    )
  )
))
```

In this UI, I am using a grid format in which I want the plot output to be in its own row. 
The options that the users select are below that in two separate columns. The `selectInput` 
statement creates a dropdown listing the different datasets the user can select. The 
`sliderInput` function creates a slider to select the number of schools the user wants to 
display. These parameters are passed as variables to _server.R_.

# server.R

This script does all of the computations to pass to the UI. I essentially use the same 
function from the bump plot script except I pass the parameter for the dataset to be 
`input$data` and the parameter for the number of colleges to be `input$var1`. It is necessary 
to use `input$var1` to specify the y-axis and the number of breaks since the some datasets 
such as Shanghai does not have 2016 data. Everything else is the same as the previous script 
except for some stylistic changes.