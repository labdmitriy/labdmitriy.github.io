---
title: "How to visualize running routes using Python"
excerpt: Parse GPX files from different services to visualize your running.
date: 2022-05-12 14:00:00 +0300
toc: true
categories:
- Blog
tags:
- Python
- Visualization
- Sport
---

## Motivation
When I quit smoking 2.5 years ago I decided to start running.  

To track the progress I chose among several apps, and after using [Runkeeper](https://runkeeper.com) for a while I switched to [Strava](https://www.strava.com).  
When enough data accumulated I became interested in how the routes from multiple applications will be displayed on the map.  
Given that after a 1 year break without running I began to use [Nike Run Club](https://www.strava.com), the issue of a unified approach became even more relevant.

## Research
I found that all 3 applications have the ability to export data to [GPX](https://en.wikipedia.org/wiki/GPS_Exchange_Format) format, which you can get using their own or third-party services.  

Also I was inspired by this awesome article:  
["How tracking apps analyse your GPS data: a hands-on tutorial in Python"](https://towardsdatascience.com/how-tracking-apps-analyse-your-gps-data-a-hands-on-tutorial-in-python-756d4db6715d)  

Ultimately I decided to find a simple interactive visualization solution which could also be easily used by other people.

## Solution
I've prepared [repository](https://github.com/labdmitriy/running) with the script, which can be used from console.  
Installation instructions and usage example are in README.md file in the repository.  

Although I used only latitude and longitude for visualization, also elevation in meters and date/time in UTC format are available and parsed by script for possible further analysis steps.

## Example
I will provide the visualization example of my 229 runs with a total distance of 1057 km which was created by this script:
- Data from all 3 applications (Runkeeper, Strava, Nike Run Club) is used
- Each color - one route
- You can zoom in/out and drag the map
- Some routes have weird geometry, perhaps there was a problem with GPS tracking in those days, but I decided to keep it "as is"
<iframe type="text/html" src="/assets/blog/2022-05-12-running-routes-visualization/map.html" width="100%" height="600" frameborder="0"></iframe>


