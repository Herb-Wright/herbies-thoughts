---
title: Why I Believe in Probabilistic Approaches to Robotics
author: "[Herbie Wright](https://herbiewright.com/)"
date: 2025 April 28
bibliography: posts/probabilistic_approaches_robotics/refs.bib
base-header-level: 2
---

## Intro

This is my inaugural blog post, so I figured I'd do something short that should probably be much longer: I'd like to sketch out a few reasons why you might still want to use probabilistic robotics methods in the age of VLAs. I am obviously biased, as my only published paper, [@wright2024v], is a Bayesian method and I plan to continue to develop probabilistic methods in the future. And, of course many probabilistic methods still have their shortfalls (speed being a common one), but I really do think that some things are better done probabilistically.

## Uncertainty

Perhaps the biggest benefit of probabilistic approaches is their ability to capture *uncertainty*. The real world is uncertain, and robots in the real world have to contend with that. A robot has to make inferences about the state and dynamics of the world based on limited sensor data that can only ever provide part of the story. This naturally creates uncertainy, and probabilistic methods can capture that uncertainty.

Being able to quantify and reason about uncertainty can make robots much safer to deploy as well as unlock some pretty cool abilities; a robot could use uncertainty information to explore more efficiently by prioritizing areas of high uncertainty. This is called *active learning*, and can really help make some tasks more efficient by pin-pointing what data you need. An example of active learning could be a robot selecting the next best view to build a NeRF or opening a drawer which it is uncertain about (perhaps the robot hasn't looked in that specific drawer yet). 

As I mentioned, uncertainty can also be useful for safety. Specifically, the ability to detect when things are out-of-distribution can help prevent a robot doing something silly. It is well known that LLMs can hallucinate, and some vision models can make very strong predictions when confronted with what looks like random noise [@nguyen2015deep]. If these are part of a broader robotic system, such errors could cause problems. However, if we have ways to discern when an observation or prediction falls outside of the distribution such models were trained on, we could prevent such problems.

## Capturing Multiple Solutions

Some problems in robotics or computer vision have multiple valid solutions. One example is 3D reconstruction from a partial view. Consider a mug with the handle occluded; if you were asked to reconstruct the backside, there are multiple valid ways to add a handle to the backside. As such, methods to deterministically predict a single reconstruction from a single partial view won't be able to capture the range of solutions. Using a probabilistic method, like diffusion, will be able to capture the range of solutions by modeling them as a probability distribution. Of course, diffusion policies [@chi2023diffusion] use diffusion for a similar reason—sometimes you need to learn multi-modal policies.

Of course diffusion has its limitations, but there are also other ways to capture irregular distributions that come with their own trade offs. But you know what can't capture a distribution of solutions? A deterministic approach.

## Justifiable Philosophy

Another benefit of using some probabilistic approaches, such as Bayesian approaches, is that they give you a sort of philosophical grounding to your approach. Maybe this doesn't mean much, because in the end what matters most is that your approach works, but there is a benefit to having a consistent way to think about the problem. It could also potentially help convince your theory-loving friends (or paper reviewers) that your method is acceptable.

As for me, I really love the ability of iterative Bayesian methods to neatly describe how you correctly incorporate new information into an existing model. It just makes things neat and understandable.

## The End

So that's that. It's really just a couple of thoughts. Maybe I will revisit the topic and flesh it out more in time, but this is really just to get me in the practice of writing more (and hopefully better).

## References {-}