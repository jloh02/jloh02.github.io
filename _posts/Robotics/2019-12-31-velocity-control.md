---
layout: post
title:  "Velocity Control"
date:   2019-12-31
excerpt: "Feedforward and Feedback Loops for Velocity Control in VEX Robotics"
categories: [Robotics]
image: "/images/robotics/velocity-control/velocity.png"
---
## Overview
In an effort to replicate the [2018 VEX Robotics World Champions 5225A](https://www.youtube.com/watch?v=sux3YbbbkYY), we decided to try motion profiling. You can read more about motion profiling [here](). Essentially, velocity control would be required to control the base. After much research, we decided to use a PF loop (PIDF loop without I and D).

## Feedforward Loop (F)
The feedforward loop is simple. Take a naive approach. Let's assume power is directly proportional to velocity (obviously it is not). Whenever we set a new velocity setpoint, we should set the power as `kF * setpoint`. kF can easily be measured. First, let's set a power where our wheels do not slip - `maxPower`. We then run the base at this power and get the maximum velocity - `maxVel`. Since we are going to set `power = kF * targetVel`, `kF = maxPower/maxVel`. We can then set `power = kF * targetVel` everytime the setpoint changes. This serves as an estimate.

## Feedback Loop (P)
The feedback loop is the proportional component of a PID loop. This is integrative - keep adding to the existing power. After using F to set the initial power, we now take `error = actualVel-targetVel`. We then set the motor power as follows `power += actualVel-targetVel`. This would slowly move our power towards the desired velocity. Just like a normal position PID loop, we increase P slowly until results are reasonably stable.

## Conclusion
This article may be short and simple. However, tuning a velocity PF loop is harder than a PD loop (in my opinion, probably because I've had more experience tuning PD loops). It is still a useful skill as velocity PID loops are used everywhere in real life, one simple example is the aircon or fan you are sitting under.

## Alternative
Another solution not discussed was a Takeback Half Algorithm (TBH). This approach is relatively less documented than PID but still widely used, especially in flywheel control.

## Resources
<https://github.com/FRC-Team-955/Team955RobotLib/wiki/PIDF-Loop>