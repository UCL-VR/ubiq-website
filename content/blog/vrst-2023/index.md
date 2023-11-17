---
title: "Content Creation Using Record and Replay"
description: "At VRST 2023, we showed how to use record and replay to enable a single user to create multi-user content in VR"
lead: "Read about how record and replay can be used by a single user to create content with multiple virtual characters."
date: 2023-11-17T00:00:00+00:00
lastmod: 2023-1-17T00:00:00+00:00
draft: false
weight: 50
images: []
contributors: ["Klara Brandstätter"]
---

We presented our paper [Dialogues For One: Single-User Content Creation Using Immersive Record and Replay](https://discovery.ucl.ac.uk/id/eprint/10177963/) at [VRST](https://vrst.acm.org/vrst2023/) 2023.

With the help of indicators showing when a recorded avatar would speak or grab an object, a single user could create believable conversations and object interactions with their previously recorded self.
Watch the video to see how we created single-user dialogues and object interaction!
{{< youtube id="KpJstuMh9xo" >}}

In the past, we implemented a [record and replay tool in Ubiq]({{< ref "/publication/ubiq-exp/index.md" >}} "ubiq-exp") with which we could easily populate a virtual space with many characters by recording over previous replays.  It was also possible to record and replay sessions together with other users. The record and replay tool intercepts and replays incoming and outgoing Ubiq network messages. This means everything that is networked can also be recorded.
{{< youtube id="LHXlRSrV97E" >}}

This dancing crowd was created by two users recording and replaying themselves multiple times. For this paper, we added more functionality to the existing record and replay tool.  

The idea behind our “Dialogues For One” paper was to facilitate content creation in VR for a single user, in particular the creation of non-player characters (NPCs). NPCs can make the virtual environment appear more populated, especially when fewer users are online, and could make it possibly more attractive to users. Creating NPCs that behave naturally can be a painstaking process. Animations are often motion-captured and require time-consuming post-processing. However, with consumer VR headsets we can already capture head and hand motion. Our idea was to use this as a straightforward method of motion capture.

A drawback of recording and replaying was that early recorded characters could not react to later recorded characters since they did not exist yet. Also, recording simulated conversations with a previously recorded avatar was difficult. Naturally, passing objects between recorded avatars was not doable since the first recorded avatar would not have a counterpart to pass the object to.

To help with these issues, we implemented audio indicators that would display the waveform of a speaking avatar for the whole recording. We also added a semi-transparent script canvas that the user could place freely within the virtual environment. The script canvas contained pre-scripted conversations the user could perform with their recorded self.

To facilitate object interaction between a user and their recorded avatar, we implemented *recordable interactable objects* (RIOs). An RIO is an object that does not get created during a replay (unlike avatars) but remains in the environment for the recorded avatars to interact with. During a recording, only the initial position of the RIO and interactions with it are recorded.

We ran two user studies, one online and one in VR, to evaluate whether participants could tell the difference between dialogues recorded by a single user and dialogues recorded conventionally by two users. The results showed that participants were not able to tell the difference between a single-user and a multi-user recording and even slightly preferred the single-user recordings.

## Code

You can find the Unity project and the dialogues we used for our user study [here](https://rdr.ucl.ac.uk/articles/code/Dialogues_For_One_Single-User_Content_Creation_Using_Immersive_Record_and_Replay/23947278).


 



