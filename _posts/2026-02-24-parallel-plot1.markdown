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

I made my own parallel plot tool (above) for this very purpose from scratch. Granted it’s very rough but the following is to show a proof of concept for what I’m going for. Below is a use case in which R-31 walls and roofs are chosen (IP units), for any orientation of the hospital, with the lowest performing windows. The chart highlights the pathways possible and the corresponding energy use intensities.

<div class='figure'>
    <img src="/assets/plot-prescriptive"
         style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 2.</span> Parallel plot with prescriptive options and highlighted pathways.
    </div>
</div>

In words, once the encoder has processed the input sentence, the cross attention mechanism feeds the final encoding to each decoder block of the decoding stack.

## What I’m thinking of working on next

The cross attention mechanism is nearly identical to the self attention mechanism, the main difference now is that in Cross Attention, we port the Key and Value matrices over from the Encoder and the decoder uses its own Query matrix on these encoder matrices. This is illustrated in the diagram below:

<div class='figure'>
    <img src="/assets/self-attention-vs-cross-attention.jpeg"
         style="width: 100%; height: 100%; display: block; margin: 0 auto;"/>
    <div class='caption'>
        <span class='caption-label'>Figure 3.</span> The cross attention mechanism in detail (Benveniste, 2024).
    </div>
</div>

The math is entirely the same, the magic occurs just by the fact that we are reusing the Key and Value matrices from the Encoder and the Query matrix from the Decoder and performing this fancy rerouting.

## But why?

Doing this allows each decoder block to pay attention to the final encoding of the input sentence. This is useful because it allows the decoder to use the information from the input sentence to help it generate the next token, and also prevents the decoder from "forgetting" the input sentence (vanishing gradient problem).

## Conclusion

That's it, that's really all there is to the Encoder-Decoder Transformer.

## References

Benveniste, D. (2024). What is the difference between self-attention and cross-attention? [LinkedIn post]. LinkedIn. https://www.linkedin.com/posts/damienbenveniste_what-is-the-difference-between-self-attention-activity-7211029906166624257-m0Wn/

Choi, J. (2024, March 2). Where the term “cross-attention” is first used? (couldn’t find the term in Attention is all you need paper) [Question on the Data Science Stack Exchange]. Data Science Stack Exchange. https://datascience.stackexchange.com/questions/128123/where-the-term-cross-attention-is-first-used-couldnt-find-the-term-in-attention-is-all-you-need-paper

Murel, J., & Noble, J. (2024). What is an encoder-decoder model? [Article]. IBM Think. https://www.ibm.com/think/topics/encoder-decoder-model
