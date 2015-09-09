---
layout: post
title:  "keeping tabs on The Big One"
date:   2015-09-07 19:25:51
categories: data_science
---
Prediction can be really, *really* hard. 

[Chaos theory][ChaosTheory] has destroyed more prediction models than any government conspiracy or act of corporate espionage. Trying to predict what will happen within a given time-frame for natural systems is highly dependent on initial conditions and changes to state (if any), requiring powerful computers to get even close approximations.

Because we'll never *truly* have the [initial state of anything,][hsp] perfectly accurate predictive systems are currently impossible. But for larger scale phenomena like the weather or **Earthquakes**, we've gotten really good at creating accurate, reliable forecasts. The major difference between a forecast and a prediction is a prediction states that a particular event will happen at time X assuming Y conditions are met, whereas a forecast just gives a probability of such an event taking place.
Forecasting is much easier than prediction, as you can use simple statistical analysis of glacial temperature trends to get a pretty good estimate of what the weather will be like, however the economic value is much less than an accurate prediction. Weather and near term earthquake forecasting has created huge socioeconomic benefit to people in areas that are most effected by adverse phenomena, but thre is still much left to do.

I've thrown my hat into the fray of near term earthquake prediction & forecasting, and over the next week or so I'll be discussing different topics that are important when trying to tackle such a challenging problem.

## Table of Contents ##
1. [how to track earthquakes](#howToTrackQuakes)
2. [core variables](#coreVariables)
3. [CUDA and machine learning](#ml)
4. [GPU instances are great!](#inst)
5. [We're just getting warmed up!](#ongoing)


<a name="howToTrackQuakes"></a>

### how to track earthquakes ###
Earthquakes have plagued our species for our entire existence, those who live far away from active fault-lines may take them for granted, but ask  anyone on the continental west coast of the USA about earthquakes and they would tell you how important early warning systems are for their business and personal well-being.

near term forecasts of large earthquake events is crucial to many peoples lives, and earthquake forecasts are becoming closer and closer to reality. The biggest boons to seismologists in recent decades are ever increasingly powerful supercomputers (like [titan][titan]), and a substantially better understanding of core variables.


<a name="coreVariables"></a>

### core variables ###
1. [Localized Surface Ionization](#localizedSurfaceIonization)
2. [Planetary Activity Index](#KIndex)
3. [historic global earthquakes](#globalQuakes)


<a name="localizedSurfaceIonization"></a>

#### localized surface ionization ####
in High-grade igneous & meta-morphic rocks, there are a number of minerals (namely quartz, feldspar, pyroxenes, etc) that occasionally contain an oxygen anion called a peroxy:

instead of O<sub>3</sub>Si-O-SiO<sub>3</sub>

we get O<sub>3</sub>Si-**OO**-SiO<sub>3</sub>

When a rock containing peroxy is deformed, the oxygen anion is free to travel within the deformation path to reach the point of lowest energy, creating a voltage potential on the surface which locally ionizes the air, something that can be detected using seismic instruments.

Abrupt changes in ionization around known or suspected fault-lines is an important indicator of a potentially large quake event hapening in the near future, as large pressure swings can be a sign of tectonic movement.

The first variable is *localized surface ionization*.

<a name="KIndex"></a>

#### K-Index ####
The second core variable that seismologists have been using is the **K-Index**, also known as the planetary activity index. The [K-index][Klink] is a value between 0-9 that specifies the current behaviour of earth's geomagnetic field, with 1 being relatively calm, and 5 indicating a [geomagnetic storm][gstorm].

How the core and the mantle effect the generation of earthquakes is still being figured out, but the signal created by a rapid increase in the K-index is another important clue into how soon, and at what magnitude the next big quake will be.

The second variable is *K-Index*.

<a name="globalQuakes"></a>

#### historic global Earthquakes ####
`History doesn't repeat itself, but it does rhyme. - Mark Twain`

If you ask any analyst what they need to forecast a future event, the very first piece of data most of them will ask for ask for will be all of the historic data containing events similar to what is being forecast.
Thankfully the US National Centres for Environmental Information (NOAA) have some very nice [databases][noaa] containing a wealth of accurate earthquake events, this is arguably the most critical variable as fault-lines and at-risk areas would be pretty hard to discover without them.

The third variable is *historic earthquake data*
<img src="https://postmediavancouversun.files.wordpress.com/2011/03/5700.japan_earthquake_heat_map_-_earthquake.jpg">


<a name="ml"></a>

### CUDA and the power of the cloud ###
If there was one particular technological innovation that has left a huge mark over the past couple of decades, its the explosive growth of server farms and the advent of cloud computing. Machine learning requires a lot of compute power to create even minutely accurate models of natural phenomena, however general purpose GPU computing technologies have come to the rescue on that front, as the thousands of processors on a GPU device make tough and repetitive math intensive calculations such as [Dijkstra][dijkstra] or [Bayesian networks][bnet] much faster to calculate.

Unfortunately 1 GPU, no matter how powerful, isn't going to cut it for most complex tasks it just takes too long. Predicting earthquakes for example requires dozens of inputs, a number hidden neurons and many long-short term memory neurons for state to be stored over long test cycles, which require huge amounts of compute power and large data sets. This is where Amazon's web services help in a big way.

<a name="inst"></a>

#### GPU instances are great! ####
When I first started working on this project (I got the idea, data and concepts from this [topcoder challenge][tpChallenge]), I knew I wouldn't have the compute capacity necessary to create an accurate model within a reasonable amount of time, so I started looking around at a variety of cloud based GPU instances, and I found amazon's G2 instances a blast. Being able to rapidly scale up my algorithms, and run it over all of the trial data simutaniously (900 GBs worth split into 75 separate quake events) was something that I could never have dreamed of, particularily at the price I'm paying.

Spot instance pricing for G2 instances is hit or miss, but if you get them at the right time you can pay $0.07-0.2 USD per hour for some of the most advanced hardware on the market, it takes a while to learn how to setup automatic deployment & regional cost management, but third party projects such as [CloudMan][cloudman] are a massive help.

<a name="ongoing"></a>

### We're just getting warmed up! ###
The first of many posts, I'm just getting used to github pages & jekyll however I plan on working my way through all the juicy technical innards of my algorithm, and any results that are worth sharing!

[gstorm]:      	https://en.wikipedia.org/wiki/Geomagnetic_storm
[Klink]:	https://en.wikipedia.org/wiki/K-index
[noaa]:		https://www.ngdc.noaa.gov/hazard/earthqk.shtml
[dijkstra]:	https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm
[bnet]:		https://en.wikipedia.org/wiki/Bayesian_network
[tpChallenge]:	https://community.topcoder.com/longcontest/?module=ViewProblemStatement&rd=16510&pm=13913
[cloudman]:	https://wiki.galaxyproject.org/CloudMan/AWS/GettingStarted
[ChaosTheory]:	https://en.wikipedia.org/wiki/Chaos_theory
[hsp]:		https://en.wikipedia.org/wiki/Uncertainty_principle
[titan]:	https://www.olcf.ornl.gov/titan/
