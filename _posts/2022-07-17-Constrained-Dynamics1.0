---
title:  "Constrained Dynamics in Javascript: Introduction"
mathjax: true
layout: post
categories: []
tags: [Physics, Tutorial]
---

In this post I start to look into the deep world of Constraints and Constraint Solvers. You will appreciate why I say deep, if you do a google search for constrained dynamics or constraint solvers. You will most likely be inundated with papers with lots of maths involving very large matrices. I have had to sift through a lot of papers before getting even a basic understanding. In my experience, rather than dive straight into the maths (which lot of papers tend to do), it helps to gain an intuitive understanding of what constrained dynamics is all about, then get into the maths. 

In particular, I will document my exploration into the formulation of constraint equations / functions and solving such constraint equations.

## What is constrained dynamics?

### Unconstrained dynamics

Where you have rigid bodies moving around the screen with no restrictions of any kind, that is unconstrained dynamics. In constrained dynamics, a rigid body in 3 dimensional space has 6 degrees of freedom, as defined by the 3 components of translation (x, y, z) and 3 components of rotation. In 2 dimensions, a rigid body has 3 degrees of freedom, as defined by 2 components of translation (x, y) and rotation (θ) about an axis perpendicular to the xy plane.

### COnstrained dynamics

In constrast, constrained dynamics is where you have some kind of restriction on the rigid bodies' degrees of freedom.

A simple example is a ball falling under the force of gravity and hitting the ground. When the ball hits the ground, the ball must stop (or bounce) - the ability of the ball to keep moving vertically downwards is restricted. Specifically the y-component of the body's position is not allowed to go below zero (assuming the ground is at y = 0). This is an example of an "Inequality constraint", in that the constraint is satisfied so long as y >= 0, but is breached if y < 0. 

Typically, constraints deal with a pair of bodies.

Another example is the distance constraint, where “the distance between these two particles should not be fixed at a prescribed distance". This is an example of a "Equality Constraint".

Another common example is the non-penetration constraint, or contact constraint, whereby when rigid bodies collide, they are not allowed to penetrate each other, and they are made to stop or bounce off each other. This is another example of an "Inequality constranit".

## Solving Constraints

Constraint solving is the process by which we compute the corrective displacement, corrective impulses or corrective forces that, when applied to the bodies, will pull them together or push them apart, so their movement will be restricted and the rules imposed by the constraints will remain satisfied.

In the ball vs ground example, as the ball this resolving a broken constraint (ie ball's y-position is below 0) involves:
pushing the y componentn of the position back to zero
resetting the y-component of the velocity to zero, or the ball is to bounce, set the y-component to restitution coefficient * pre-collision y-component.

What I am about to describe is a generic framework for:
1. expressing a constraint as a constraint equation,
2. solving the constraint equation, in order resolve the constraint

### Constraint function

A constraint is defined in terms of a constraint function C, which takes the state of a pair of bodies as parameters (e.g. position and orientation) and outputs a scalar number.

We would specifically express C as a linear combination of positional properties (linear position and angular position, ie rotation, etc, ie the "state" of the body);

When the value of this function is in the acceptable range, the constraint is satisfied. If the "state" of the constraint function is composed of position properties, and we solve this "position constraint function", it means that we are solving the constraint by directly manipulating the position properties of the bodies - this is what we did in collision resolution.

Another way of solving constraints is come up with a constraint function that takes velocity components as input (ie linear velocity, angular velocity) and then solve that. This means that we are solving the constraint by manipulating the velocity of the bodies - similar to how we have come up with collision response.

### Position constraint function

In order to come up with the necessary impulse, the first thing we must do is to come up with the position constraint function. For the moment, we will keep this discussion generic, and say that the position constraint function is expressed as follows.
$$ C(x) = 0 $$
where x is the positional properties of the bodies (i.e. the position and rotation of the pair of bodies).

If we now take the time derivative of the above, we arrive at the velocity constraint function.

### Velocity constraint function

The direct manipulation of position to satisfy a constraint. However, we are trying to resolve constraints at the velocity level.

Again, for the moment we will keep things very generic and say that we want to express the velocity constraint as follows, where J is a matrix called the Jacobian matrix and v is the relative velocity of the objects expressed as a vector (we gradually explain why).

[XXX]

This constraint function is NOT satisfied if it does NOT equal zero. We are trying to find Δv (or the impulse to change the velocity by Δv) so that the velocity constraint function does equal zero, ie:

[XXXX]

Note that derivative of C will always be some linear function, i.e. the above derivative of C can be expressing the manipulation of the relative velocity in terms of matrix maths. This is the first step to understanding the world of constraint function and constraint solvers using matrix maths!

It may also help to say that the Jacobian Matrix contains the "constraint axes" along which the impulse will be applied.

### Solving the Constraint function

That is all very well and interesting but how do we actually calculate the impulse necessary to solve the velocity constraint function?

The velocity constraint function was expressed as the relative velocity multiplied by the Jacobian matrix, and that the whole point of this framework is to express velocity constraint as a linear function of velocities. You can then intuitively understand that the Jacobian is the "axis" along which the impulse will be applied - for that reason, the Jacobian is sometimes referred to as expressing the "constraint axis". Following on from this, we can say that the impulse is some multiple of the Jacobian, and therefore, the velocity delta is the impulse multiplied by the inverse mass. Again, leaving aside the detail for now, take it for granted that Δv is expressed as below.

[XXX]

where:

J is a matrix referred to as the Jacobian, and
XXXXXXX
M is the mass matrix, expressed as below:
and the inverse of the mass Matrix is:
XXXX
Reinserting the Δv magic equation back into the velocity constraint function, we get
XXX

Rearranging we can arrive at a formula for lambda (a scalar) as follows:
XXX

Recall (again) that when the velocity constraint is satisfied:
XXX
ie, 
XXXX


or
XXXX



Note that this is a general form for solving a system of linear equations, Ax = b where:

A = JM-tJT

x = lambda

b = -Jv

We are trying to obtain the impulse along the collision normal, ie λ・n.

Since we now have the velocity constraint expressed in the above form, there are numerous methods for solving such system of linear equations. The most direct method of which is the following matrix multiplication.




Hence we could solve all the constraints simultaneously by expressing all the constraint equations in one giant matrix A However this is not the typical approach adopted in games physics engines. More typically individual constraints are solved one-by-one, sequentially. You can guess that this will have the effect of invalidating a constraint after on constraint is solved. However, we will discuss in further detail in a separate post.


We have not covered where we get J from - we will cover this with an actual example in a subsequent post, but assuming we have J, we can calculate lambda.


And once we have lambda, we can substitute this back into the following to calculate the post collision velocity.

## Adjusting for position error

Solving the velocity constraint equation given above is not always enough, however. The Δv  may resolve the velocity constraint. However, this will not always fix the position constraint, as we would have only eliminated the velocity break along the constraint axis. The bodies must be given a little bit of extra "oomph" along the constraint axis to satisfy not only the velocity constraint but also the position constraint. For that, we need to introduce what is referred to as bias velocity; an extra bit of impulse/velocity, if you will. The velocity constraint function then becomes:
XXXX

where b is the bias impulse, and this is based on the output from the position constraint function C(x).

If we want this error to be reduced to zero in the next timestep ∆t, the velocity needed to correct for this error is C(s) ∆t. However, we do not want the error to be removed in a single timestep. Instead, the velocity needed to correct for the position in small steps for stability reasons. The bias term is usually expressed as following:
XXXX

where β is a value between 0 and 1 called the bias factor. The bias factor describes the amount of error that is corrected at each timestep.

This type of error correction is called Baumgarte stabilization

If you work through the same calculations as above, you will get the following expression for lambda.
XXXX


With this adjusted alpha, you can calculate the impulse to get the collision response required.


Preparing for the next post

Sounds like smoke and mirrors? And why so complicated? We did not actually cover how to get J..


But assuming we can...the above allows us to encode different types of constraints as a set of matrix applications in a generic form. As long as we can come up with the constraint position function, and from there, come up with the Jacobian, solve for and scale the result by the correct mass matrix, we can solve the constraint.
