---
layout: post
title:  "Physics Simulations in Javascript: Moving a ball with Euler Integration!"
categories: []
tags: [Physics, Tutorial]
---

Having covered the fundamental laws of physics necessary (at least for linear motion) and vector maths, it is time to make a start. But we will start, very very slowly..by making balls move around the screen.

## Relationship Between Position, Velocity and Acceleration

The first step to moving objects around the screen is to review the relationship between Acceleration (a), Velocity (v), and Position (p). Specifically:

Velocity is the “rate of change over time” of Position, or in mathematics speak, “time derivative” or simply the “derivative” of Position. More intuitively, Velocity is a measure of the distance travelled in a step of time.

Acceleration is the "rate of change over time" of Velocity. Or intuitively speaking, a measure of velocity changed in a step of time.

According to the above, if we know the object’s position right now (p1) and 1 second ago (p0), the velocity is the change in position - typically referred to as displacement - (dp = p1 - p0) divided by the change in time (dt) velocity (v) = dp / dt.

Similarly, acceleration (a) = change in velocity (dv) / change in time (dt).

## Euler Integration

For the purposes of building a physics engine, what this means is that we can work backwards, ie if we know an object’s acceleration, we can figure out how much the velocity is supposed to change every second. And if we know the velocity, we can figure out how much the position is supposed to change every second. This is the opposite of "derivative" and is called “integration”, which you may remember from calculus lessons at school.


In terms of psuedo-code, this translates to:

```
acceleration = force / mass
change in position = velocity * dt
change in velocity = acceleration * dt
```

where dt is the time elapsed since the last calculation.


In the theoretical world, t is continuous and dt is very very very short. In the world of physics world, dt is indeed very short but is finite. Strictly speaking, the above holds when acceleration and velocity are constant during dt. Since dt is typically very short this gives a good approximation, but it is exactly that - an approximation. This technique is called "numerical integration", and more specifically, Explicit Euler.

There is in fact another (very similar but different) technique technique called semi-implicit Euler (or Symplectic Euler Integration), which gives better results even when acceleration is changing over dt. Switching from explicit to semi-implicit Euler is as follows:

```
position += velocity * dt;
velocity += acceleration * dt;
```

to:
```
velocity += acceleration * dt;
position += velocity * dt;
```

There are other integration methods such as Verlet integration and Runge-Kutta integration.

For now, I will adopt the semi-implicit, assume that dt = time between each refresh = 1 and mass of all objects is all 1, ie f = m x a => f = a.

Let's get coding!

At the core, the "object" to be moved around a screen will be a point

{% highlight javascript %}

class PhysicsBody {

  constructor(x, y, a, m = 1) {
 
    this.pos = new Vec2(x,y);
    this.angle = a;
    
    this.velocity = new Vec2();
    this.angularVelocity = 0;

    this.mass = m === 0 ? 0 : m;
    this.invMass = this.mass === 0 ? 0 : 1/this.mass;
    
    this.force = new Vec2();

    this.shape;

  }
  
  addShape(shape) {

    this.shape = shape;

  }

  integrateForces(gravity, dt) {

    this.force.copy(gravity);
    this.velocity.addScale(this.force, dt);
     
  }

  integrateVelocities(dt) {

    this.pos.addScale(this.velocity, dt);
    this.angle += this.angularVelocity * dt;
    
  }
  
}

{% endhighlight %}

A point has no "size". We want shapes to move around the screen. So we will start with a circle (best entry point for experimenting with physics simulations) which will be attached to the point.

{% highlight javascript linenos %}
class Circle {

    constructor(r) {

        this.type = "Circle";
        this.radius = r;
     
    }

}
{% endhighlight %}

### integrateForces() method

This method is the first part of moving the ball by numerical integration. It is integrating acceleration to get to velocity (in the example we are simply taking gravity as the only force acting on the body).

#### Inverse Mass

You will notice that I am calculating the inverse of the mass within the constructor function. For the moment, mass is assumed to be 1 so this does not matter, but in future posts I will attempt to take into account the masses of objects in calculating their behaviours, and there will be a lot of "dividing by mass" in physics equations.

I understand division is computationally more expensive than multiplication and dividing by mass will occur a lot in physics simulations. As mass does not change, it therefore makes sense to calculate inverse mass once, and multiply by that, as opposed to divide by mass, whenever necessary.

## CodePen

As this post is getting quite long, here is a demo which has one circle bouncing up and down. In the next post we will have lots of circles bouncing around the screen.

{% include codepen.html hash="ExOKWZe" title="Bouncing Ball" %}
