---
title: Quaternions!
author: "[Herbie Wright](https://herbiewright.com/)"
date: 2025 May 19
# bibliography: posts/quaternions/refs.bib
---

## 1 Intro & History

The world needs one more Quaternions explainer. Ok maybe not, but I'm going to make one anyway. Specifically, I want to talk about how quaternions are used to store and compute rotations in 3D—pretty useful in robotics. It's all pretty similar to how complex numbers can describe 2D rotations. Complex numbers served as the starting point for the invention (discovery?) of quaternions. Quaternions were first thought up on October 16, 1843 by William Rowan Hamilton, who was trying to extend complex numbers to "triples" (or one real number with two imaginary components). His insight, which occurred to him on a walk with his wife, was to skip right past "triples" and go to "quadruples" where two extra imaginary numbers were added. The famous equation, which he etched onto a bridge, looks like this:
$$ i^2 = j^2 = k^2 = ijk = -1 $$
And that is how we got quaternions. But... what are they?

## 2 What are Quaternions?

Technically, Quaternions—denoted $\mathbb H$—form a *non-commutative* *associative* *division ring*. If you know what that means, you probably don't need to be reading this. An element of $\mathbb H$ can be described by four numbers, $q_1, q_2, q_3, q_4$:
$$ q_1 + q_2 \mathbf i + q_3 \mathbf j + q_4 \mathbf k \in \mathbb H. $$
We can use the rules from the very first equation to multiply two quaternions, $q, p \in \mathbb H$:
$$ qp = (q_1 p_1-q_2 p_2-q_3 p_3 - q_4 p_4) + (q_1 p_2 + q_2 p_1 + q_3 p_4 - q_4 p_3)\mathbf i + (q_1 p_3 + ...) \mathbf j + (...) \mathbf k$$
I'm not writing it all out because it's a lot. Feel free to look at the [Wikipedia page](https://en.wikipedia.org/wiki/Quaternion) for the whole product. There are a few things to note about the product though, and they relate to the long complicated name I opened the section with. The first thing to note is that *order matters*:
$$ q p \neq p q. $$
In fact, $q p = - p q$. That's where the *non-commutative* part comes in. The second thing is that *associativity* works fine:
$$ (q p) r = q (p r), \forall q, p, r \in \mathbb H. $$
Of course, this also combines nicely with quaternion addition and scalar multiplication, which are defined as (for $a \in \mathbf R$):
$$ q + p = (q_1 + p_1) + (q_2 + p_2) \mathbf i + (q_3 + p_3) \mathbf j + (q_4 + p_4) \mathbf k $$
$$ a q = a q_1 + a q_2 \mathbf i + a q_3 \mathbf j + a q_4 \mathbf k $$
I say it combines nicely because you can have properties such as $q(p + r) = qp + qr$, etc.
There are also a couple of specific quaternions that ought to be remembered: $1, 0 \in \mathbb H$, where the real part is either 1 or 0 and all imaginary coefficients are set to 0.
It should also be noted that quaternions are equipped with a norm. That norm is defined like so:
$$ \| q \| = \sqrt{q_1^2 + q_2^2 + q_3^2 + q_4^2}. $$
Which, in my opinion, is pretty straightforward. The addition and scalar multiplication are defined like the traditional $\mathbb R^4$ vector space, so the relevant properties for the norm hold. What is interesting, is that when you look at the set of *unit quaternions*—$\{q \in \mathbb H: \|q\|=1\}$—they are able to describe the $SO(3)$ lie group.

## Unit Quaternions & Rotations

As mentioned in the previous paragraph, unit quaternions are the set of quaternions with a norm of 1. These unit quaternions can be used to describe the [lie group of 3D rotations](https://en.wikipedia.org/wiki/3D_rotation_group), denoted $SO(3)$. Any two quaternions with unit length, when multiplied, will result in another unit length quaternion—this allows us to *compose* or multiply rotations. But if we have a unit quaternion, $q \in \mathbb H$, how would we rotate a 3D point, say $(x_1, x_2, x_3) \in \mathbb R^3$? Well, we first make a quaternion using our 3D vector like so:
$x = 0 + x_1 \mathbf i + x_2 \mathbf j + x_3 \mathbf k.$
While this is not necessarily a *unit* quaternion, it is a quaternion. We can rotate by simply multiplying:
$$ x' = q x. $$
Easy peasy. We can now store 3D rotations with only 4 numbers instead of a whole 3x3 rotation matrix, and we don't have to worry about [Gimbal lock](https://en.wikipedia.org/wiki/Gimbal_lock) that comes with Euler angles. 

## Conclusion

Because of their compactness in describing 3D rotations, quaternions show up everywhere in robotics. It is pretty much the standard way for simulators to store the rotation information of free bodies. Storing rotations also finds its way into some learning-based parts of robotics. A neural network might output a rotation for registering two point clouds, or maybe predict an $SE(3)$ grasp pose. In these cases, loss functions tend to be expressed in terms of distance in the *lie algebra*, $so(3)$, which comes with its own set of fun math. But anyways, I don't think anyone wants to be reading me ramble about quaternions, seeing as there are probably hundreds of better explainers out there, so I'll just call it a day.

