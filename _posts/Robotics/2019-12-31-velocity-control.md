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
The feedforward loop is simple. Take a naive approach. Let's assume power is directly proportional to velocity (obviously it is not). Whenever we set a new velocity setpoint, we should set the power as `kF * setpoint`. kF can easily be measured. First, let's set a power where our wheels do not slip - `maxPower`. We then run the base at this power and get the maximum velocity - `maxVel`. Since we are going to set `power = kF * targetVel`, `kF = maxPower/maxVel`.

## Feedback Loop (P)
The feedback loop is the proportional component of a PID loop. We can set 
