---
title: Building Occupancy Prediction with Sensors and Online Machine Learning 
layout: default
date: 2026-04-28
keywords: online machine learning, ai, artificial intelligence, streaming data, smart buildings, building occupancy, hvac, co2
published: true
mathjax: no
---
Revisiting my thesis project from 2021-22. I did it in collaboration with computer science master students in which I focused on the dataset engineering and preparation and they focused on the machine learning model development and hyperparameter adjustments. 

# Background

We collaborated with a local real estate management company that managed public properties under the municipality, such as an exhibition centre, but also others such as a science lab and innovation centre. They wanted to purchase sensors to create an Internet of Things (IoT) to make their building “smart”, hence smart buildings. CO2 sensors are a common proxy to indicate occupancy, and are often used to control modern occupancy-responsive HVAC controls like DCV (demand-controlled ventilation). Other sensors explored were occupancy sensors at main entries into spaces to estimate people counts, and existing indicators in the BMS (building management system) such as ventilation flow rates, and temperatures (also an indicator of occupancy but weaker especially in large spaces, occupants, generally being warm-blooded mammals, are a source of internal heat gain). 

<div class='figure'>
    <img src="/assets/sensors.png"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 1.</span> Sensor placement in the exhibition hall (example).
    </div>
</div>

The exhibition centre was transitioning out of COVID times to full scale in-person events. It was often not in-use, at least on a much more reduced scale, during the peak of the pandemic. Generally, ventilation rates in rooms were very large and could vary by the room and limited in terms of how many different rates there could be, and demands for fresh air was always variable depending on event attendance and occupant flows between multiple rooms. The management staff couldn’t be monitoring the occupancy all the time, while they did know the schedule ahead of time, they would often program the HVAC schedule ahead of time and seldomly adjust on the go. Therefore, to be conservative, it was always skewed towards overventilation rather than underventilation – occupants need to be always comfortable amidst uncertainty of how many people in the occupied space at any given time, and this was compounded further with the COVID-19 ventilation needs at the time. 

<div class='figure'>
    <img src="/assets/Sensor-results.png"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 2.</span> Typical hourly logs of CO2 sensor data in exhibition hall, aggregated from different simulatneous placements in large space. The occupied times compared to unnocupied times can be clearly distinguished.
    </div>
</div>

<div class='figure'>
    <img src="/assets/elmia.png"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 3.</span> Photo of what the test exhibition hall looked like when empty and not in use. The space is multi-purpose and could host a variety of activities from trade shows to sporting events. It was sitting empty for much of the COVID-19 pandemic or with significantly reduced in-person attendance.
    </div>
</div>

I originally wanted to pursue a super ambitious project of automating the ventilation system with AI that adjusts with live occupancy status via sensors, but that would have been way above the scope of a standard master’s thesis project even if starting early. For a simplified scope, we set out to develop a proof-of-concept that would be a piece of such a larger system for future students to build off.

The machine learning model was somewhat conceptually unique for this use case, though not by any means state-of-the-art and groundbreaking. Hopefully, this use case will be the future trend in smart buildings, as it does have potential. 

First, in terms of application, the idea was that rather than be reactive, responding to occupancy such as with DCV, the model could be proactive, adjusting to where the occupancy will be soon rather than later, to account for lag times in the airflow in large spaces. Thus, we set out to develop a model that could accurately predict future occupancy status given current status as well as other possible sensor inputs. 

Secondly, a typical machine learning model might take a large set of historical data, split it into a training set and test set, and deploy it to the BMS to then begin making live predictions. The problem is that if the occupancy patterns are highly variable, there is the risk of concept drift, in which the model previously fitted to older occupancy patterns fails to adjust to newer occupancy patterns. One would have to play around with hyperparameters to avoid overfitting to begin with but this gets a bit fiddly, and would require taking the model offline and then training and uploading again, which is time-consuming, and depending on dataset size, could be computationally expensive. Based on feedback from our industry collaborators, this would not be a feasible solution as a real-world application. Enter Online Machine Learning, a set it and forget it (almost) machine learning model that trains and tunes its own hyperparameters “online” and on the go, you deploy it and other than specifying the learning rate, it learns and adjusts to live data over a theoretically infinite stream of data, without having the need to have a specified training and testing phase. For this application we used River, a machine learning library for online machine learning for data streams. 
For more details, my thesis can be accessed on the Research page. 

# What I’ve been working on this time

Since I only managed the datasets last time, I wanted to try to create the machine learning model myself. I tested everything out in JupyterLab based around code snippets provided by the computer science students, though the syntax might have changed a bit since 2022, 4 years is eons in the world of tech. 

The dataset was a bit different this time, as I somehow lost the original dataset for testing this model during the thesis. It was a split between simulated data and real data collected from the sensors. Originally everything was done in increments of minutes but was adjusted to hourly for model training as it was more realistic scale for real application. I believe I saved it on my school OneDrive and since I lost access to that account, I was not able to retrieve it again. Anyway, I decided to create a new dataset that combined all the sources but in a single CSV, hourly.

The dataset size is 9480 features, in which about 720 are real hourly datapoints of event days at the exhibition hall. The stochastic gradient descent (SGD) learning rate is 0.01, which is the same as the thesis. The dataset was relatively simple, the current CO2 value and the future CO2 value, one hour ahead of time. As with the original thesis, everything is fed through the model in one continuous stream rather than being split into training and test sets. We want to simulate a situation in which there is continuous streaming data like with a building under constant operation. 

As added context, atmospheric CO2 is typically around 400 ppm as of the 2020s in Sweden. Depending on occupation density, the concentration will go up to around 700-800 in an occupied space and for health and comfort reasons should be below 1000 ppm (ventilation systems that are demand-controlled or otherwise with CO2 sensors will control to keep the concentration below this level). 

My initial results for this were close to the original, R2 of 0.780876 and MAE of 70.543821. In terms of R2, the fit is still heavily skewed towards unoccupied days, as that is the majority of the time. There is still some degree of overfitting to unoccupied days, but there is faster adaptability to when there are occupied days, as illustrated in the comparisons of predicted vs true CO2 values. In terms of the absolute error, 70-ish is reasonable, and is a bit more than initially produced in the thesis, as the datasets were tested differently. See the blip about common CO2 values above. I would honestly need to read more of the literature on CO2-based HVAC occupancy control to conclude if this is certainly acceptable. My initial thought is that it would be acceptable for a control that can indicate whether the space is occupied or empty to suggest the corresponding HVAC setting. Hour by hour adjustments for a more dynamic control scheme would necessitate greater precision, at first glance on these results. 


<div class='figure'>
    <img src="/assets/Graph-1-first-3-days.JPG"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 4.</span> Graph of the hourly predicted values in orange vs true CO2 values in blue for the first 3 days of the dataset.
    </div>
</div>

<div class='figure'>
    <img src="/assets/Graph-2-final-3-days.JPG"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 5.</span> Graph of the hourly predicted values in orange vs true CO2 values in blue for the final 3 days of the dataset.
    </div>
</div>

# Next steps

<div class='figure'>
    <img src="/assets/future-work1.png"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 6.</span> Scope of my original thesis relative to potential future work.
    </div>
</div>

<div class='figure'>
    <img src="/assets/future-work2.png"
        style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 7. Big picture: how the machine learning model could be expanded or integrated into a full AI agent HVAC controller for the exhibition hall zone.</span> 
    </div>
</div>

