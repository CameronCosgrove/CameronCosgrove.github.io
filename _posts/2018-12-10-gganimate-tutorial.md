---
title: "Animated Graphs"
author: "Cameron"
comments: "true"
---

# Intro to animated graphs:  gganimate
<center> <img src="logo.png"> </center> 
(logo by:thomasp85)

_By Cameron Cosgrove on 07-12-2018_

In this tutorial we will be exploring how to make animated graphs with [`gganimate`](https://github.com/thomasp85/gganimate). We will build on `ggplot2` data visualisation skills taught in the [data vis 1](https://ourcodingclub.github.io/2017/01/29/datavis.html), and the [data vis 2](https://ourcodingclub.github.io/2017/03/29/data-vis-2.html) tutorial, and use data handling skills taught [in the piping tutorial](https://ourcodingclub.github.io/2017/01/16/piping.html). By the end of this tutorial you should have a good grasp of `gganimate` syntax and grammar, know how to turn a ggplot graph into an animation, be comfortable exploring the package help files, and know how to save and export an animation as a gif.

### Tutorial Aims

#### <a href="#section1"> 1. What is gganimate, and why use it?</a>
#### <a href="#section2"> 2. Creating basic animations</a>
#### <a href="#section3"> 3. Creating animated maps </a>
#### <a href="#section4"> 4. Tweening and smooth transitions  </a>
#### <a href="#section5"> 5. Saving your animations </a>
#### <a href="#section6"> 6. Coding challenge </a>

### You can get all of the resources for this tutorial from [this GitHub repository](https://github.com/EdDataScienceEES/tutorial-assignment-CameronCosgrove). Clone and download the repo as a zip file, then unzip it in a location of your choice.

___
#### It should be noted that `gganimate` updated its syntax in late 2017, so there is a lot of outdated information and tutorials online. This tutorial works with v 0.9.9.9999 and v 1.0 should be on [CRAN](https://cran.r-project.org/) soon.
___

<a name="section1"></a>

## 1. What is `gganimate`, and why use it?

`gganimate` is an extension to ggplot2 that allows you to animate your graphs. In essence, `gganimate` emulates the same animation process that has been employed by Disney for over [100 years](https://www.youtube.com/watch?v=M2ORkIrHUbg). Just like in old cartoons, the animation is composed of different frames. The only difference being our frames are still snapshots of graphs, not hand drawn cartoons. The output of `gganimate` is a short-looped gif, and can be easily viewed on most devices.

Animations are obviously limited to electronic mediums and cannot be added to print reports [(_for now_)](https://thefutureofthings.com/3081-the-future-of-electronic-paper/). Because of this constraint, most of us won't be that  familiar with animated graphs and their uses. Before we wade into the code, let's spend some time thinking about what sort of data can be made more effective and informative through animation.

I see three main data situations animated graphs can help with:

1.  If your data varies over time you can show temporal trends and interactions more easily through animation;

2. You can synthesise panel graphs with the same kind of data into one animated graph, squeezing in an extra variable in your data visualisation;

3. Animated graphs can be eye catching and aesthetically impressive.

As with any data visualisation technique, there are also downsides to animated graphs. It can be hard to extract specific data from animations because of the rate of information turnover and unnecessary animation can also distract from your main message. This [article](https://earthobservatory.nasa.gov/blogs/elegantfigures/2013/03/14/is-animation-an-effective-tool-for-data-visualization/) neatly discusses the effectiveness of animated graphs. It is worth considering if animated graphs would be effective for your data before you invest time making them. That being said, you and your data don't have to be constrained by 2 dimensions! So, let's get started. 

<a name="section2"></a>

## Basic animations

Before we wade in too deep, lets focus on making simple animated graphs first. 

Open `RStudio`, create a new script by clicking on `File/ New File/ R Script` set the working directory and load the packages we'll need.

If you haven't installed these packages before, you will need to install them. `gganimate` needs to be downloaded from github using devtools. Do this by running: 

```r
# install.packages('devtools')
devtools::install_github('dgrtwo/gganimate')
```
Then load the required libraries:
```r

# Libraries - if you haven't installed them before, run the code install.packages("package_name")
library(ggplot2)
library(gganimate)
library(tidyverse)
``` 
Now we are all set up, let's add some data. 
```r
basic_animation_data <- read.csv("~/basic_animation_data.csv")
View(basic_animation_data)

#Define a and b as character values for later 
basic_animation_data$a <- as.character(basic_animation_data$a)
basic_animation_data$b <- as.character(basic_animation_data$b)

#What structure is the data in? 
str(basic_animation_data)
```
We have two different types of data. x and y are continuous, and <b>a</b> and <b>b</b> are categorical. We need to account for this in our visualisation.

Feel free to change the aesthetic theme of the plots you are about to make, it shouldn't affect the animation. I have kept the code as bare as possible in this tutorial to make the animation aspects clear and understandable.

#### Animating continuous data
Let's plot two simple x=y animations to get started. 
First, let's make a line graph. We start with just a normal ggplot function, plotting a line with our x, and y variables. We then add the `gganimate` term. For line graphs we use `transition_reveal()`, which reveals more of your data every frame. `transition_reveal()` needs an `id` term which is the data we want to reveal every frame and an `along` term which specifies what dimension the data should be revealed along (most of the time `along` = your x-axis data). In this case we can use x or y for `id` and x for `along`.

```r
#The line graph.
ggplot(basic_animation_data, aes(x, y)) +
    geom_line() +
    #Here's the animation bit
    transition_reveal(x, x) 
```
It will take ~20 seconds to render and your console will track the progress. You should end up with this graph in the `Viewer panel`: 


<center> <img src="basic_line.gif"> </center> 

Notice how it only took one line of extra code to make an animation! 

Let's try animating a scatter graph now. This time we use a term called `transition_manual()`. This is more straight forward as you just insert the variable you want shown sequentially in the frame. Again, in this case we can use x or y.


```r
ggplot(basic_animation_data, aes(x, y)) +
        #set point size and dont add a legend
        geom_point(aes(size = 3), show.legend = F) +
        #now animate
        transition_manual(x)
```

<center> <img src="basic_scatter.gif"> </center> 


Let's take a moment to think about the function `transition_*()`. We have used two different ones so far, but why? The `transition_*()` tells the data how it should spread out and how it should change over time. Basically, it defines how the data should change each frame. `gganimate` gives lots of transition options and one of the challenges of animation with `gganimate` is picking the right one. Each `transition_*()` function requires a different type of data and I would advise you explore the gganimate help document for specifics. 

_<b>Top tip: If you highlight a function you don't know, press `F1` and it will open the help page in the viewer panel.</b>_

#### Animating categorical data
What about our categorical data, what animation options are available? We could look at change in the number of data points per category over time. This could be done with a bar chart, with the columns changing size as the frames progress.

But we have a problem. Let's look at the form of our data again:
```r
View(basic_animation_data)
```
We have two columns for <b>a</b> and <b>b</b> but `gganimate` can only animate a variable if all the data is in one column. Before we had an x term and a y term but bar graphs only take an x variable. We need to combine our two <b>a</b> and <b>b</b> columns together, define <b>a</b> and <b>b</b> as levels and ask `gganimate` to transition frames using the level of the variable. We can use our [data wrangling](https://ourcodingclub.github.io/2017/01/16/piping.html) skills to do this.

```r
#Lets put our a and b column into longform using some pipes(%>%)

basic_data_long <- basic_animation_data %>%
  #add a and b to a column called measurment and add their values to another column called value
  gather(measurment, value, a:b) %>%
  #and keep only the values we want
  select("measurment", "value")

#and make a and b factors
basic_data_long$measurment <- as.factor(basic_data_long$measurment)

View(basic_data_long)
str(basic_data_long)
```
Now our data is in the right form. We can tell `gganimate` to display the value of our measurements in two frames defined by the factor of measurement.

```r
ggplot(basic_data_long, aes(value)) + 
    geom_bar() + 
    #now the animation bits 
    transition_states(measurment, 0, 1)
```

<center> <img src="basic_bar_bad.gif"> </center> 

So, we have a bar graph that switches the data every frame. It is doing what we wanted it to do but the animation is very uninspiring. We can't even tell which frame is <b>a</b> or <b>b</b>. Let's change that. 

Highlight `transition_states()`, press `F1`, and read the description and usage. This function 'tweens' between states and has arguments that change the length of transition. Change the `transition_length` to 2 and the `state_length` to 7 and see what happens.

Let's add a changing title too. Use `{}` to call the name of a frame. The name is different for each `transition_*()` so look in the description of the function for the label variable names. "closest_state" is the most appropriate label for this graph. 

```r
ggplot(basic_data_long, aes(value)) + 
    geom_bar() + 
    #now the animation bits 
    transition_states(measurment, 2, 7) +
    labs(title = '{closest_state}')
```
<center> <img src="basic_bar_good.gif"> </center>

That's a bit better! We will explore tweening soon in section 3. Now the basics are out of the way, let's get onto some more effective and beautiful data animations.


<a name="section3"></a>

## 3. Animated Maps 

Let's use what we have just learnt and apply it to a more complicated 'big data' example. Species distribution maps are discussed [here](https://ourcodingclub.github.io/2017/03/20/seecc.html), and we are going to take the puffin occurrence map created in this tutorial a step further. Feel free to explore the rgbif data but the focus of this section is on the animation rather than the data manipulation - so don't worry if some of it is beyond your skill level for now.

Install and load the following packages: 

```r
library(rgbif)
library(maps)
library(ggthemes)
```
We want to map UK puffin observations throughout the year. Let's filter our data to get what we need: UK only observations and Puffin(_Fratercula arctica_) records between 2006 - 2016.

```r
#code adapted from the CC tutorial: Working efficiently with large datasets

UK_code <- isocodes[grep("United Kingdom", isocodes$name), "code"]
occur <- occ_search(scientificName = "Fratercula arctica", country = UK_code, hasCoordinate = TRUE, limit = 3000, year = '2006,2016', return = "data")
View(occur)
occur$month
```
Check out the month column, the months are just given to us in numbers. Let's add in the month names, this will be useful for labelling our animation later. 
```r
occur_mn <- occur%>%
  mutate(Month = month.name[month]) #add a new column with numbers replaced with their character month name

#Let's assign an order to this variable to override R's defalt alphabetical ordering. 
occur_mn$Month <- factor(occur_mn$Month, levels = c("January","February", "March","April","May", "June","July","August","September","October", "November","December"))

```
That's the data wrangling done. Before we dive into the animation, let's think about our visualisation. We are going to plot puffin populations over the months of the year, so our frames will be `months`. It will be plotted on a map so the x and y values will be our latitude and longitude coordinates and we need to load a base map for context. We can also visualise the puffin population numbers in the size of each point. 

```r 
(puffin_map <- ggplot(occur_mn, aes(x = decimalLongitude, y = decimalLatitude, size = individualCount)) + 
    # Specify the UK and set the boarder size and colour
    borders(database = "world", regions = "UK", colour = "gray40", size = 0.3) +  
    theme_map()+ 
    #make the title bigger
    theme(plot.title = element_text(size=20))+
    #define the scale for the point sizes
    scale_size(range = c(5, 15))+
    # Change the colour and transparency of the plotted occurrence points 
    geom_point(alpha = 0.6, colour = "#FFA500")+
    #remember how we added a changing title earlier
    labs(title = 'Month:{current_frame}')  +
    #now the animation part
    transition_manual(Month))
```


<center> <img src="fast_puffin.gif"> </center>

_How effective do you think this animation is? Can you interpret it easily? What months are most puffins recorded and what ecological reasons might be behind this?_

If we wanted to slow down the animation so people could have more time looking at the data we can use the `animate()` function. Simply assign your animation as an object and use `animate()` to define the number of frames, fps(frames per second), and the  duration.

```r
animate(puffin_map, 12, 0.5, 30)
```
<center> <img src="slow_puffin.gif"> </center>

This gif has now slowed down and loops every 30 seconds. 

<a name="section4"></a>
## Tweening and smooth transitions

Let's think back to our first basic animations. The points jumped every frame and were not aesthetically pleasing. We can use a technique called tweening to join up our animations and make them smooth. Tweening, short for 'inbetweening' fills in the spaces between our data to create intermediate 'fake' frames. We can easily add in tweened data to our animations by adding an `ease_aes()` term. This tells `gganimate` to join up the data via a specified [easing](https://easings.net/) equation. The type of easing can make points transition between frames in a number of ways, such as smoothly (`'linear'`), exponentially (`'exponential-out'`) or like a rubber ball (`'bounce-in'`). 

Load in the `plant_height_data.csv` and test out some transitions. 
```r
plant_height_data <- read.csv("~/plant_height_data.csv")
View(plant_height_data)
```
It would be interesting to see the distribution of plant height across different habitats, this might tell us something about canopy structure. Lets' make a violin plot to visualise this and have the frame set to the habitat.
```r
ggplot(plant_height_data) +
  #Lets look at the distrubution of heights across habitats
  #Set x=1 as no x-axis is needed for this animation
  geom_violin(aes(x= 1, y= avg_height), fill = "#218C25C4", scale = "count") +
  #Add in the animation code
  transition_states(habitat, transition_length = 5, state_length = 5) +
  labs(title = 'Habitat: {closest_state}', x = '', y = 'Tree Height') +
  #Now the tweening function
  ease_aes('linear')
```
<center> <img src="plant_height.gif" /> </center>

Explore the `ease_aes()` help page to discover more transition types and test them out. Go back to the first basic animations you made and add in some tweened transitions there too. 

_What happens if you add ease_aes('linear') to the puffin map? Is it appropriate to use tweener on all types of data?_

<a name="section5"></a>
## Saving your animations

Saving animations is very similar to saving regular ggplot2 objects but instead of `ggsave()` we use `anim_save()`. 

Let's use the plant height animation as an example. There are two ways to save this animation:

1 - After you have just rendered the animation use the `last_animation()` function to call the rendered object
```r
 anim_save(animation = last_animation(), filename = 'plant_height.gif')
```

2 - If you have assigned your animation as an object, you need to `animate()` it before it can be saved
```r
plant_height <- ggplot(...) + ...
.
.
.

plant_height_rendered <- animate(plant_height, 70, 15)

anim_save(animation = plant_height_rendered, filename = 'plant_height.gif')
```


<a name="section6"></a>
## Coding challenge

Let's test what we have just learnt and apply it to a much larger data set. The `gapminder` data set contains a range of information on countries around the world. Your challenge is to show on one graph, how the gdp, population and life expectancy in every African country has changed over the years. All of these variables can fit on one animated graph. 

Here is where you get the data:
```r 
library(gapminder)
View(gapminder)
```
Here are some hints if you get stuck:
```
aes(size = pop)

scale_x_log10()

#The graph can take a long time to render (~10mins).
#Remove the ledgend to speed it up.
show.legend = F
```
Good luck! 

___

### End of the tutorial. 

#### Here is a summary of what we learned:

##### - Animated graphs can be effective if used well but they are not suitable for every situation.
##### - `gganimate` works by adding on functions to a ggplot object. Care is needed to select the best `transition_*()` for your data 
##### - The help files for `gganimate` and other packages are very useful for problem solving issues that arise in your animation
##### - Tweening is the process of adding in inbetween frames in an animation and can be done easily with `gganimate`
##### - Animations can be easily saved as a gif

___

#### Acknowledgments
I would like to thank Edgar Hunter and Maggie Doyle who tested the tutorial code and gave me feedback on this tutorial and to [Thomas Lin Pedersen](https://github.com/thomasp85) who has been developing the `gganimate` package. 

___

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/).
