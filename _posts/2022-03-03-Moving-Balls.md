---
layout: post
title:  "Physics Simulations in Javascript: Moving a ball with Euler Integration!"
categories: []
tags: [Physics, Tutorial]
---

Having covered the fundamental laws of physics necessary (at least for linear motion) and vector maths, it is time to make a start. But we will start, very very slowly..by making balls move around the screen.

Moving objects: understanding Position, Velocity and Acceleration

The first step to moving objects around the screen is to review the relationship between Acceleration (a), Velocity (v), and Position (p). Specifically:

Velocity is the “rate of change over time” of Position, or in mathematics speak, “time derivative” or simply the “derivative” of Position. More intuitively, Velocity is a measure of the distance travelled in a step of time.

Acceleration is the "rate of change over time" of Velocity. Or intuitively speaking, a measure of velocity changed in a step of time.

According to the above, if we know the object’s position right now (p1) and 1 second ago (p0), the velocity is the change in position - typically referred to as displacement - (dp = p1 - p0) divided by the change in time (dt) velocity (v) = dp / dt.


Similarly, acceleration (a) = change in velocity (dv) / change in time (dt).

{% include codepen.html hash="ExOKWZe" title="Bouncing Ball" %}
