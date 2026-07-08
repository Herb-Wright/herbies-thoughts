---
title: Simulation Matters.
author: "[Herbie Wright](https://herbiewright.com/)"
date: 2026 June 9
bibliography: posts/simulation_matters/refs.bib
link-citations: true
---

## 1 Introduction

On February 16, 2026, I had the opportunity to participate in a structured debate—something I had never done before. The debate was centered around the role of *simulation* in robotics. See, there are a lot of problems in robotics that feel like they would just go away if simulation was better. But simulation in robotics, while being quite impressive, still lacks some things we really wish it had. Things like photo-realism, modeling certain non-rigid dynamics, and the whole sim-to-real gap in general come to mind. Despite this, I am optimistic; I don't think we should abandon simulation to solely focus on real-world behavior cloning. Even now, our methods for simulating things lets you do a lot of useful stuff in robotics. Conversely, I also think there is quite a bit of room to improve current simulators. In this post, I want to talk about it.

## 2 Ways To Use Your Simulator

Building good simulation has been a long-standing pursuit in robotics, and for good reason. Simulators have proven to be useful for things like evaluation, data generation, and model-based policy synthesis, among others.

**Evaluation:** If you have multiple policies and want to compare them on a certain task, it can be quite costly to compare them in the real world. You need to obtain and set up an expensive robot and painstakingly reset the task until you have completed enough trials to make confident statements about which policy is better—not to mention safety concerns, and all the other added difficulty that comes from working with hardware. Simulation is a very attractive alternative. Modern physics simulation has been used to cheaply evaluate various vision-language-action models. For example, [@lievaluating], where they find a very strong correlation between simulation performance and real-world performance on their benchmark. Similarly, the TRI team used high-quality simulation to evaluate their large behavior models in [@barreiros2025careful]. In both of these cases, simulation offered a cheap, useful alternative to the headaches of real-world robotic evaluations. Simulation is also much easier to standardize and benchmark than real-world setups. That's why the most popular robot learning benchmarks are simulation-based (e.g. LIBERO [@liu2023libero])

![Images from LIBERO [@liu2023libero].](image.png)

**Cheap data:** Robotics has a bit of a data problem—there doesn't seem to be enough of it [@goldberg2025good]. A promising direction to get orders of magnitude more data is through simulation. This is the insight behind the concept of *sim-and-real co-training*, explored in [@maddukuri2025simandreal]. They used simulation to generate much more data than they had from the real world, and trained policies by randomly sampling from both their sim and real datasets. They found that co-trained policies performed significantly better than those trained on only real world data—even when the sampling percentage was 99% from simulation data. Another good paper on co-training can be found in [@wei2025empirical]. One big benefit of using simulation to generate data is that it can be procedurally done via leveraging classical methods and privileged information; when you are in simulation, you don't have to deal with the pesky uncertainty present in the real world.

**Model-based methods:** A simulator is a model, and robotics has many techniques to turn a model into a policy. These include reinforcement learning (RL) techniques (like proximal policy optimization [@schulman2017proximal]), sampling-based model predictive control methods (like predictive sampling [@howell2022predictive] and model predictive path integral [@williams2016aggressive]), and even methods that use easier-to-optimize simulation formulations used to build controllers (like differential simulation [@xu2022accelerated] or linear complementarity systems [@aydinoglu2024consensus;@bui2025push;@wright2026uncertainty]). These techniques are made possible because of the progress in robotic simulation methods.

## 3 The Sim-to-Real Gap

The usual argument against simulation for robotics comes from the gap between the simulation dynamics and reality, commonly called the *reality gap*. This gap propagates through to policies trained in simulation into the *sim-to-real gap*. The sim-to-real gap is the performance drop of such policies when moved from simulation to reality. This gap, critics argue, will always be a bit too large [@levine2025sporks]. I'm not so sure, and here I want to explain why. In short, I think it has been shown that you don't need a *perfect* model to achieve sim-to-real transfer, you simply need a *useful* model; we already have examples of sim-to-real working well in robotics, even with imperfect models.

*In Locomotion:* Perhaps the most notable example is locomotion. Sim-to-real reinforcement learning has produced some incredibly robust locomotion policies. Locoformer [@liu2025locoformer] is a particular flashy example, where they can *saw off* the legs of a quadroped, and show that their controller can adapt to the new morphology.

![Images from Locoformer [@liu2025locoformer]](image-1.png)

*In Manipulation:* While perhaps less established, we also see inklings of sim-to-real reinforcement learning being possible in manipulation (e.g. ManipGen [@dalal2025local]). Additionally, an example of using incorrect-yet-useful models effectively in manipulation can be found in model-based control methods (e.g. Push Anything [@bui2025push])

However, that is not to dismiss the sim-to-real gap entirely. The reality gap still very much exists [@aljalbout2025reality], and it is a worthwhile aim to reduce it.

## 4 Towards Better Simulators

There are multiply ways that the *reality gap* manifests, and as such, there are multiple research directions aimed at closing various reality gaps in robotics simulation. A pretty easy split is rendering/observation gaps vs a physics/dynamics gaps.

**Closing the Observational Gap:** One of the most well-known gaps between current robot simulators and the real world is RGB rendering. It can be quite obvious what is a simulator and what is the real world by simply looking at an image—no actual motion required. Domain randomization is one technique to help overcome the visual gap, yet it is far from a perfect solution currently. Other work has explored leveraging Gaussian splatting [@kerbl20233d] for more photorealistic rendering [@dan2025x]. There was recent work at ICRA 2026 which used Unreal engine in combination with Mujoco for photorealism [@embley2026unreal]. However, I am somewhat of the opinion that when it comes to visuals, domain randomization or inverse rendering for real-to-sim is the better way to close the observational gap, an idea that aligns with recent work from Ai2 [@deshpande2026molmob0t]

**Closing the Dynamics Gap** The models used for simulation need to compress the true physics of the world into a slightly less accurate version. This means that there is a gap in the dynamics of the simulator and the real-world dynamics. One modern way to overcome this gap is to allow for black-box learning as part of the simulation (e.g: residual learning [@saveriano2017data], learned deformable dynamics [@zhang2025particle]). There are also efforts to improve classical simulation by developing new contact models or simulators (e.g. hydroelastic contact [@elandt2019pressure]). These efforts aim to reduce the sim-to-real gap by more closely aligning the simulation dynamics with the dynamics observed in the real world.

![Image of the hydro-elastic contact model from [@elandt2019pressure]](image-2.png)

## 5 Conclusion

All in all, I think improving simulation and real-to-sim methods constitute very fruitful research directions with many open problems. In my mind, better simulation is perhaps the biggest key to unlocking new robotics techniques and capabilities, and I am excited to see how research in robotic simulation evolves.

## References


