---
title: "Single Actor, Multiple Roles"
description: ""
lead: "Discover how one actor can jump among multiple avatars in the virtual environment to enhance social presence."
date: 2024-01-05T00:00:00+00:00
lastmod: 2024-01-05T00:00:00+00:00
draft: false
weight: 50
images: []
contributors: ["Jingyi Zhang"]
---

We presented our paper [Supporting Co-Presence in Populated Virtual Environments by Actor Takeover of Animated Characters](https://doi.org/10.1109/ISMAR59233.2023.00110) at [ISMAR](https://ismar23.org/) 2023.


The advancement of consumer virtual reality (VR) systems and high-speed internet services has enabled a broad range of new social virtual reality (SVR) applications. To create experiences that appear to be densely populated we can augment users with avatars representing autonomous agent simulations. Such agents might have a similar appearance as user avatars and thus the user might expect them to have at least some reactive and communicative behaviours. However, gathering enough personnel to control the necessary number of avatars for creating a realistic scene is usually difficult. Additionally, current technology is not capable of fully simulating avatars with behaviours, especially when interaction with users is required.

Inspired by the idea from Neal Stephenson’s book “The Diamond Age” [1] where characters are played by hired “ractors”, we expect that an actor might be able take over an agent to fill in the gaps in its reactive behaviours. Thus, as an intermediate solution to enhance the immersion of users in social VR, we developed a takeover system in which agents could be controlled by a human when needed whilst pre-recorded motions were played autonomously at other times. In the scenario where only one participant was present in the scene, we could allow a single actor to jump amongst multiple agents. 

The takeover system was built on the Ubiq social VR platform. We created a cafe scenario populated with three agent avatars. Both actor and participant could enter this cafe using Meta Quest 2. The actor was able to see all agent avatars available for takeover. Each agent avatar displayed with a future keyframe selected from the pre-recorded loopable clips. The actor was asked to mimic the motion cued by the keyframe of the agent avatar. A linear blending would produce animation from the keyframe avatar to the actor’s current position and posture to compensate for the difference when takeover happened. The actor’s avatar was normally invisible to the participant and could only be “seen” when one of the existing agent avatars had been taken over. In this way, the participant was unaware of the transfer of control and an illusion that all characters in the scene have human intelligence was produced.

In an experiment, we asked participants to perform a sequence of tasks in a cafe scenario while the actor took over different agent avatars and interact with participants. The results proved that our system improved the perceived plausibility of the virtual environments inhabited by pre-recorded agent avatars.

Below we shown how one actor can jump among avatars in the scene.
{{< youtube id="5je4EwTVOi8" >}}

## Code

The source code we use for our study is available on [Github](https://github.com/Zongmushuwu/TakeOverAvatar).


## Reference
[1] N. Stephenson, The Diamond Age, Bantam Books, 1995.


