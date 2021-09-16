---
layout: blogpost
title: "mavsh: experiments, firmware, and inspiration"
date: 2021-09-02 00:01:00 +0001
categories: mavsh
---

"I want to build a drone that can hack something."

# What does that even mean? <br> What would that even look like?
<p>How can a drone hack something? At the time I had this idea, I had absolutely no clue how a drone could hack something, so naturally I started to look for inspiration.

I came across a few projects from organizations/individuals in my search, but two of them particularly stood out to me: BishopFox's DangerDrone, and the "WatchDogs inspired drone". The DangerDrone was exactly what I wanted to learn how to build. It had a Raspberry Pi+flight controller hat as the main processor for the drone which allowed you to connect arbitrary USB peripherals. You can plug in a cellular data stick to have ssh access to the pi (this also means your flight range is limited only by battery capacity), connect arbitrary RF antennas, and have the quad hover around anywhere to allow you to conduct whatever attacks you prepared the quad to conduct.

The WatchDogs drone doesn't have the DangerDrone's capabilities, but it's substantially smaller and carries a WiFi Pineapple onboard. It's size means that its less noticeable, and will cause way less collateral damage if it crashes, but the tradeoff is that it only has one purpose: to act as a rogue access point.

Both of these projects served as good inspiration, but neither of them are sufficient for me.
</p>

# How would I even begin to do that? 

<p>Turns out the answer to this question is pretty long... Although I did have some ideas, and so I began to experiment.

I started by deliberating about my hardware setup, concluding that at a bare minimum, I would need a Flight Controller, 4 ESCs (Electronic Speed Controllers), 4 motors, 1 camera/VTX (Video Transmitter),a frame (inb4 the cat drone), and ideally a small linux computer with USB ports for peripheral hardware. A Raspberry Pi seemed like an obvious choice here, so I started  searching for a Raspberry Pi based flight controller, and came across the Navio2 pi hat.

The Navio2 has an accel/gyro, barometer, mmcx gps connector, and other sensors I might need to build a quad, so I purchased one and once it came in, I built the first prototype (below). Since this quad was never really meant to fly, building it was the easy part. Configuring it on the other hand... was not as simple. Why? Every drone I had built thus far used Betaflight firmware, but this quad would require me to use Ardupilot.</p>

![first_prototype](/assets/images/projects/mavsh/valkyrie_v1.png)

# Ardupilot vs Betaflight

<p>
Betaflight and Ardupilot are two very different flight control software projects, with very different use cases in mind. When you think of racing drones, or freestyle pilots doing cool tricks, you're thinking of a Betaflight drone. Betaflight is used by these pilots because it's new features/releases focus on optimizing flying in the acro mode (full 4 axis control). One good example of this is upgrades to firmware to support the Bidirectional DSHOT protocol. A very high level explanation of DSHOT is that its a digital protocol which handles sending throttle inputs the flight controller recieves to the ESC which can then control the motors (if you're curious and want more info check this out: https://brushlesswhoop.com/dshot-and-bidirectional-dshot/). Bidirectional DSHOT tells the ESC to respond to the flight controller with rpm data so that the flight controller can use the data for Dynamic RPM filtering, allowing the flight controller to discard bad rpm frames (which are pretty common for your flight controller to see after you try to powerloop and damage your props because shits hard...) That Betaflight feature is a HUGE game changer for making your racing quads fly better (lets be real... this hobby is AT LEAST 50% repairing your quads until you get really good, so having the software compensate for your inevitably damaged props is amazing), but a quad designed for autonomous flight probably wouldn't care about that...

That gets us into Ardupilot... such an interesting project 

Ardupilot is designed to primarily support waypoint navigation/autonomous flights rather than freestyle flying. This difference is quite apparent when preparing for your first flight with ArduCopter as you can configure your copter to utilize 30+ flight modes. The recommended mode to begin flying in is the "Stabilize" mode which attempts to aggresively compensate for all pitch and roll inputs instead of giving the pilot full control over the copter. 



In Progress
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Downsizing a quad wasn't my first priority during this phase, but it was something I still had to think about during the entire design process. Nonetheless, I built the first prototype to learn a bit about the Ardupilot ecosystem and realized just how large that ecosystem was...
</p>