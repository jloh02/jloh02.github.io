---
layout: post
title:  "FIRST Global Challenge 2019"
date:   2019-12-30
excerpt: "Team Singapore's Rapid Fire Flywheel"
categories: [Robotics]
image: "/images/robotics/first-global-challenge/robot.png"
---
## Overview
So we had the honour of representing Team Singapore in the FIRST Global Challenge 2019 in October. This year's challenge was to essentially deposit balls into a high goal, roughly 3-4 times the height of the robot. We decided to build a flywheel.

I was simply a secondary school freshman when our club last did a flywheel during the VEX Robotics 2015-2016 Season (Nothing But Net). In other words, I had no knowledge on PID. If you do not know what PID is, I suggest you read my [introduction to PID](https://jloh02.github.io/robotics/introduction-to-pid/) first.

## Implementation
Initially, when we reached Dubai, we had scraped the hope of having rapid fire mechanism due to the limited time to program. In due time, we realised having one was paramount if we intended to do well. As of what I have read from multiple sources, a velocity control PID is integrative - the result is added to the previous power. Using a P loop (`power += kP*error`), we found that having a high P was required to recover from the drop in velocity due to the energy lost to the compression and shooting of each ball. However, a low P was also required to allow the velocity to stabilise at the desired setpoint.

After much trial and error implementation, the code followed a decently simple logic. Use a low PD loop to get to the desired shooting velocity. When a button was pressed, kP was increased and the intake would move continuously for a set amount of time. This allowed versatilility in our PD loops, overcoming both limitations.

## Post Competition Review
Of course, such a solution was not optimal. We needed a much better solution which was a problem for another day, considering that I would almost never velocity control in robotics tasks. Never did I think I would use [velocity control in VEX Robotics]().
