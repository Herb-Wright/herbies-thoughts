---
title: Understanding Rigid Body Physics Simulation
author: "[Herbie Wright](https://herbiewright.com/)"
date: 2025 May 5
bibliography: posts/rigid_body_simulation/refs.bib
---

## 1 Intro

Simulating rigid body physics is incredibly useful in robotics—I'm also told it is useful in video games. Regardless, being able to simulate rigid body physics shows up in robotics in a couple of ways. One way is to use simulated data for some sort of learned robotics method; A lot of methods train in simulation, then deploy in the real world—so many, in fact, that pretty much everyone knows the term *sim-to-real gap*. Another way simulators are used is as a model for some sort of model based control, or potentially as a dynamics model for certain planning algorithms. Now, in a sense, both of the uses I mentioned are both on the same spectrum of using a simulator as a model of robot-object dynamics; perhaps the real difference is whether you carry the simulator with you during "inference" or try to *learn* the relevant dynamics in your neural network so you can toss out the simulator at inference. It doesn't matter. The point is: *Simulating rigid body physics is important to robotics*, and while I'm not an expert on physics simulation, I want to try to sketch out the basics of what goes into simulating rigid body dynamics (as I understand it).

I should note, I will be talking about a certain type of formulation of rigid body physics. The kind that is used in *most* popular physics simulators. There is also position based dynamics, where contacts are basically modeled as damped springs, but those tend to be less accurate and perhaps less stable.

## 2 Basic Physics

You probably remember the incredibly complicated $\mathbf{F} = m\mathbf{a}$ equation from physics. Well, rigid body physics is basically just that with contact and a bunch of other steps. Here is a pretty basic equation that is used (with perhaps slight variation) in rigid body simulators:
$$ \mathbf M(\mathbf{q}) \Delta \mathbf v = \tau \Delta t + \sum_{i=1}^{n_c} \mathbf J_i (\mathbf q)^\top \lambda_i. $$
In the above equation, $\mathbf q$ is the *generalized coordinates* of the system, $\mathbf v$ the velocities, $\mathbf M(\mathbf q)$ is the inertial matrix, $\tau$ is dependent on the state and represents all of the external forces (gravity, Coriolis, applied), $\mathbf J_i(\mathbf q)$ is the *contact Jacobian* for the $i$-th contact, and $\lambda_i$ is the corresponding impulse for that contact. Then, of course, $\Delta \mathbf v$ and $\Delta t$ are changes in velocity and time repectively. If you squint, it kind of resembles that $\mathbf F=m\mathbf a$ equation. 

Rigid body physics simulators basically use the above equation, but—and this is important—even with the above equation, you don't really know what your contact impulses are (the $\lambda_i$'s). Instead, contact impulses are usually chosen by solving an optimization problem subject to some constraints. Constraints like no interpenetration, only points in contact can have an impulse, and Colomb friction are commonly used. In order to express these constraints, the signed distance of a contact between bodies is often used, denoted $\phi_i$.

Instead of looking into exactly what constraints or optimization methods are used to find the contact impulses, I actually want to just stick on the above equation. In the rest of this post, I want to dig into what each of the variables mean; what does the state look like; and what the deal is with those contact points.

## 3 Representing the State

In the previous section, I mentioned that $\mathbf q$ was the *generalized coordinates* of the system. $\mathbf q$, along with $\mathbf v$ make up the *state* of the system. I think it is worth explaining what the generalized coordinates even are, and how they would potentially be stored. The generalized coordinates give the configuration of each part of the system (each object, each robot, etc.); this is usually a pose for free objects, and joint angles, etc. for robot joints. A pose is defined by both a position and a rotation—it is an element of $SE(3) = \mathbb R^3 \times SO(3)$. Thus, $\mathbf q$ can be stored as an array of positions and quaternions for each rigid body object. That also means that the velocity, $\mathbb v$ is stored as a bunch of *spatial vectors* of six dimensions each. Of course, if you want to go the quasi-dynamic route, you can simply ignore velocities altogether.

This also helps explain what $\mathbf M(\mathbf q)$ is doing; there are inertial quantities for both the position as wel as the orientation. The matrix is able to capture all of that.

## 4 Contact Points

Perhaps the most mysterious part of Section 2 is how we just happen to know where all the contact points are. In practice, we have to compute them. Things are made easier if you have convex shapes because finding the signed distance between them, $\phi_i$, is a convex problem. If you have a non-convex mesh, per se, you have to first do a *convex decomposition*. There are algorithms out there for this, but once you have done the decomposition, you are left with a bunch smaller convex meshes—easy money.

The other thing that might be worth mentioning are those contact jacobians, $\{\mathbf J_i \}$. Basically, these act to map between coordinate frames of contact and the generalized frame.

## 5 Further Reading & Conclusion

Both before and as part of writing this, I have read some pretty good sources that dive into different parts of the math mentioned above. I wanted to make a brief list for interested readers.

- [@todorov2012mujoco] is the paper for [Mujoco](https://mujoco.org/), a popular physics simulator, and is worth taking a look at.
- [@stewart2000implicit] and [@anitescu1997formulating] are a couple older papers that are highly relevant.
- [@pang2023global] is an interesting paper that utilizes a smoothed version of quasi-dynamic rigid body physics for differentiable simulation.
- [@pfrommer2021contactnets] has a simple general formulation similar to section 2, but more importantly, they propose *learning* different components of the rigid body contact dynamics.
- [@warp2022] is an NVIDIA project that includes a differentiable *position-based* physics formulation (different from what I talked about). It is also just a cool project.

There is also a bunch of other great resources if you keep digging. But also... who knows if anyone is even going to read this, so I'll end the list there.

## References
