---
title: "Meta Avatars in Ubiq"
description: "The Meta Avatars also work in Ubiq!"
lead: "The Meta Avatars also work in Ubiq! Check out this very quick how-to guide."
date: 2025-02-06T00:00:00+00:00
lastmod: 2025-02-06T00:00:00+00:00
draft: false
weight: 50
images: []
contributors: ["Klara Brandstätter"]
---

Most of my PhD work revolves around content creation in VR using record and replay techniques. Record and replay facilitates the animation of virtual characters enormously by letting the user record their own movements and replay them on a virtual character.

In the past, I have often worked with simple avatars (no legs or arms), but for my latest project I wanted to have full-body avatars, ideally with the possibility to add hand and face tracking at a later stage.
Because I like the look of the Meta Avatars, I wanted to give it a go and use them in Ubiq.
{{< youtube id="oYy7aJxRF0Y" >}}

The Meta Avatars are quite a complex piece of software but they come with examples that can be helpful for getting started.
Basically, every scene using Meta Avatars needs an [OvrAvatarManager](https://developers.meta.com/horizon/documentation/unity/meta-avatars-manager-prefab) prefab. The samples that come with the SDK already include a recommended prefab variant which you can put in your scene. This prefab is responsible for loading avatars (among lots of other things). The SDK provides 33 avatar presets that you can load locally from disk, so you do not need to have your own Meta Avatar.

Ubiq's input handling and avatar management make it easy to add custom avatars. Ubiq's `AvatarManager` component uses an avatar prefab from which it spawns the local avatar at runtime. If we want Ubiq to control the Meta Avatars we have to create our own prefab add Ubiq's `Avatar` component and extend some of the functionality that is provided by default with the SDK.

Below, I provide a short description of how to integrate the Meta Avatars in Ubiq. I used the [Meta Avatars SDK](https://developers.meta.com/horizon/documentation/unity/meta-avatars-overview/) (35.0.0-pre.2) and Ubiq (1.0.0-pre.8) packages and Unity (2022.3.42). Make sure you also download the Meta Avatars SDK Samples for the necessary scripts.

### Quick How-To

To send input from Ubiq to a Meta Avatar we can create our own class (e.g. `UbiqInputManager`) and inherit from `OvrAvatarInputManager`. We then override `OnTrackingInitialized()` to supply the `_inputTrackingProvider` with our custom Ubiq input `UbiqInputTrackingDelegate` which inherits from `OvrAvatarInputTrackingDelegate`. Override `GetRawInputTrackingState()` in the delegate class and set the `inputTrackingState` using the information provided by Ubiq's local `Avatar`. Add your input manager to your avatar prefab.

By default, each Meta Avatar needs an [OvrAvatarEntity](https://developers.meta.com/horizon/documentation/unity/meta-avatars-ovravatarentity) component that provides the functionality for loading and tracking avatars. The samples come with a `SampleAvatarEntity` component (which inherits from `OvrAvatarEntity`) which you can add to your Meta Avatar prefab or you make your own which you might need for more custom functionality. This component requires a reference to the input manager it should use, so we set this to our `UbiqInputManager`.
 
Make sure you have the OvrAvatarManager in your scene (remove the input manager on this prefab if it has one as we have made our own) and add your custom Meta Avatar prefab to Ubiq's `AvatarManager`. Your prefab should have a `SampleAvatarEntity` component and your `UbiqInputManager` component. 

By default, local Meta Avatars are rendered only with their upper body and no head. When you make your own AvatarEntity class, you can use `SetActiveView` to switch between different avatar representations.

If you want leg animation for your remote avatars (according to [this post](https://developers.meta.com/horizon/downloads/package/meta-avatars-sdk/), animations are now also possible on local avatars) you can add the `OvrAvatarAnimationBehaviour` component to your custom prefab.

 



