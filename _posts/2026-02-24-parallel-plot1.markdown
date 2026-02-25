---
title: Parallel Plots for Early-Stage Energy-Efficient Building Design 
layout: default
date: 2026-02-24
keywords: parallel plot
published: true
mathjax: yes
---
I first heard of parallel plot charts when I was working as a building enclosure consultant in 2019, when the head of our energy and sustainability department introduced me to this neat online tool called Building Pathfinder. We used it to inform clients on code energy requirements and what design options they could consider at a very high-level in the early stages, without the need to set up an energy model. I thought it was a neat and colourful graph, and it was interesting how one could edit what data you want to visualize and focus on instantaneously. Since then, I always thought of it as Building Pathfinder, or just Pathfinder. 
Building Pathfinder was developed to give design teams a dynamic high-level understanding of how their design could perform in terms of energy efficiency (and a few years later, embodied carbon). With adjustable parameters like enclosure thermal performance, mechanical efficiencies, and various commonly used HVAC systems, the user could quickly get a clear ballpark idea of where their design stood in terms of performance. Or, looking at it the other way, one could see what design options are on or off the table if they wanted to meet a certain performance threshold but didn’t yet know their enclosure or mechanical system.

You can find out more about the OG Building Pathfinder at this link: https://buildingpathfinder.com/

Fast forward to my time working fulltime as an Energy and Climate Consultant in Toronto with a new group of smart people, one of the senior energy specialists in our department was also implementing our own type of “Building Pathfinder” but for a different set of building types and slightly different parameters, as well as a much wider swatch of locations in Canada and even the US, aside from just Vancouver and some select areas of BC. 

Like with my previous company, the tool would be especially useful in the feasibility to schematic to design development phases when the client often hasn’t nailed down exactly their design direction but want to consider as much as possible in meeting their sustainability targets while the ability to make changes and make a meaningful impact is at its highest. 

A typical energy model could take as little as a few hours for a house to weeks for larger buildings such as hospitals, labs, commercial buildings, even for early-stage design. As a consulting business, the ideal is to have a good balance between bringing insightful value to design decisions early on while also not having to charge such high fees from re-iterating on design changes so early, before the project has even been greenlit in some cases.

To avoid further digression, let me get into what I made. 




## What I’ve been working on

<div class='figure'>
    <img src="/assets/Plot-full.png"
         style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 1.</span> My parallel plot chart for 100 parametric runs of the ASHRAE baseline hospital. 
    </div>
</div>
I just so happened to be interviewing for a position and got to talking about E+ for their energy modeling workflows. The topic of the internal project from my previous company came up as it related to their early-stage parametric design methods too. I guess everyone is doing something similar these days?

I used EnergyPlus (E+) to run quick and dirty parametric options for a code baseline hospital in New York City (ASHRAE 90.1, Appendix G [I forgot which year]). The nice thing is with E+ I could generate some somewhat realistic parametric runs rather than random numbers to demonstrate the parallel plot tool, which, since I couldn't just take what I was working on at my old company, I had to make from scratch (shown above). Granted it’s very rough but the following is to show a proof of concept for what I’m going for.

Below is a use case in which R-31 walls and roofs are chosen (IP units), for any orientation of the buidling, with the lowest performing windows. The chart highlights the pathways possible and the corresponding energy use intensities. The roughness shows - some irrelevant pathways are still shown and the colours need to be clearer to show the different path options. 

<div class='figure'>
    <img src="/assets/Plot-prescriptive.png"
         style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 2.</span> Parallel plot with prescriptive options and highlighted pathways.
    </div>
</div>

I thought I would have to program this over weeks of extensive coding or even using a vibe coding tool to accelerate the process. Turns out, as seems to always be the case, someone else in the world did the exact same thing or similar and posted about it on the Internet. Using plotly for Python and Pandas, I was able to develop in about a day a functionally equivalent parallel plot chart which could load in the same type of csv with columns of parametric results. It doesn’t come with any bells and whistles at the moment. Features such as buttons to select the location, building type, size, and so on. It was honestly harder to get the right version of Python set up and using command prompt to install all the packages. Overall, a lot more straightforward than I expected. 

The code was simply this:

```python
# Import the library
import plotly.graph_objects as go
import pandas as pd

# load dataset
df = pd.read_csv("yourdata.csv")


# create chart

fig = go.Figure(data=
    go.Parcoords(
        line = dict(color = df['tedi'],
                   colorscale = 'Earth',
                   showscale = False, #set to "True" to show color scale
                   cmin =your minimum,
                   cmax =your maximum,
        dimensions = list([
            dict(tickvals = [0,90,180,270,360],
                 label = 'Orientation w.r.t North', values = df['orientation']),
            dict(tickvals = [5.678,14.195,22.712,31.229,39.746,48.263],
                 ticktext = ['R-6', 'R-14', 'R-23', 'R-31', 'R-40', 'R-48'],
                 label = 'Wall R-Value (ft²·°F·hr/BTU)', values = df['wallr']),
            dict(tickvals = [5.678,14.195,22.712,31.229,39.746,48.263],
                 ticktext = ['R-6', 'R-14', 'R-23', 'R-31', 'R-40', 'R-48'],
                 label = 'Roof R-Value (ft²·°F·hr/BTU)', values = df['roofr']),
             dict(tickvals = [0.088059176, 0.17611835153223,0.264177527, 0.352236703, 0.440295879,0.528355055],
                 ticktext = ['U-0.09', 'U-0.18', 'U-0.26', 'U-0.35', 'U-0.44', 'U-0.53'],
                 label = 'Window U-value (BTU/ft²·°F·hr)', values = df['windowu']),
            dict(range = [4.3,4.4],
                 visible = True,
                 label = 'Cooling Demand Intensity (kwhe/sf)', values = df['cooling']),
            dict(range = [6,9],
                 label = 'Heating Demand Intensity(kwhe/sf)', values = df['heating']),
            dict(range = [36.8,39],
                 label = 'Total Energy Use Intensity (kwhe/sf)', values = df['tedi'])])
    )
)
fig.show()    
    ...
```
Going back to the E+ part, to do the parametric runs, I used jEPlus, which is an external program that basically takes your parameters in a json file and injects them into, say in this case, 100 E+ .idf files and compiles all of them into a results csv, which I could upload into my parallel plot with Pandas. 

Below is a figure showing the inverse use case - trying to reach a target below a certain threshold and seeing the available design options.

<div class='figure'>
    <img src="/assets/Plot-lower-than-target.png"
         style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 3. Available pathways for a usage intensity below a certain threshold</span> 
    </div>
</div>

## What I’m thinking of working on next

My main project idea is for Passive House, which uses a different software called PHPP, basically a huge Excel sheet. With PHPP, I still haven’t figured out how to parameterize it for 1000s of different runs yet. What this trial project showed too is that 100 still looks pretty neat on the parallel plot. Maybe I can realistically set that as a minimum. 

Embodied carbon is something more and more important these days. The OG Building Pathfinder has an embodied carbon pathfinder as well. If I could do it parametrically in tandem with PHPP iterations that would certainly be useful.

There’s the whole putting it all on the web aspect as a functional tool. I know nothing about web development. I am also not sure if I’ll stay within Python with this and might redo all of it in Javascript so it’s more straightforward to put online. I will have to look more into it.

Speaking of the web. 

For some reason, when I use Pathfinder on my browser on Chrome and Firefox it is very slow to load all that data. I remember back in the day I could load things relatively quickly especially once narrowing down some options, now it seems to be slow in general. Not sure if it’s only on my end. For usability, this should be faster and more responsive. Part of my project is to be able to have a similar number of features while maintaining responsiveness as the whole idea is to be able to manipulate parameters and brainstorm ideas on the fly such as live during meetings. Those of us who presented lots of things on Zoom or Teams know how much of a resource hog they are, let alone if one needed to share their screen. 

I want to improve some visuals with the graph, such as making it more intuitive to select options than the default with Plotly, more like how it’s done with the original Pathfinder. With all features to be implemented I’d like to keep it running smoothly as well. Again, not sure if that’s an issue with my device or a browser issue. 

I think in general it would be a great tool in the toolkit for building performance consultants to deploy at client meetings, particularly kickoffs and design charettes where it’s more of an exploratory and brainstorming theme. 

