---
title: "New Desktop Control Scheme"
description: "An introduction to the XRIT Desktop Control Scheme"
lead: "In pre8 we released the XRIT Desktop Controls, which are built into the XRI Demo Scene."
date: 2024-08-19T09:19:42+01:00
lastmod: 2024-09-19T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["Sebastian Friston"]
---

The motivation for the new controls are to make it easier to build cross-platform applications using the XR Interaction Toolkit. For example, an application targetting WebXR that is meant to be used in both 2D mode and VR mode.

The Desktop Controls replace the XR Device Simulator as the default 2D controls for the samples, so it's no longer necessary to import the XR Device Simulator if you don't want to use it.

The control scheme is not a complete replacement for the XR Device Simulator however: naturally a keyboard and mouse have different affordances to a pair of XR controllers - or hand tracking. Applications intending to support 2D and 3D have to be designed for this from the start, for the user to have a good experience.

Where the goal is for this to happen though, the new XR Desktop Controls should make the process much easier!


## Usage

The latest XRI Demo scene already contains the controls. When in 2D, you will see a set of hints along the bottom. The mappings are designed to work with both one-button and two-button mice.

![XRI Demo Scene with Hints](demo-scene.png)

The desktop controls work alongside the XR controls. There is no need to activate, deactivate or configure them. In 3D the XR controls will work, and in 2D the desktop controls will work.

![XR Interaction Setup Hierarchy Annotated](hierarchy-annotated.png)

If you want to hide the Hints by default, disable the `Hints` GameObject. If you want to disable the 2D controls entirely, remove the `Desktop Controller` GameObject.

![New Scene Setup](setup.png)

If you want to use the Desktop Controls in a scene with a different *XR Interaction Setup*, simply instantiate
 the `Desktop Controller` Prefab from `Samples/Ubiq-x.x.x/Demo (XRI)/Assets/Desktop Controller` under the `Camera Offset` GameObject in your scene.


### Supported Actions

The desktop controls support all the events and behaviours enabled by the Ray Interactor, including `Hover`, `Select` & `Activate`, on the XR `Grab`, `Poke` and `Gaze` Interactables. The scheme also allows interacting with UI elements, and locomotion.

We do not yet support Climbing though.

Set up the XR Interaction Toolkit Starter Assets Demo Scene as above to try out all these interactions in one place.

![XR Interaction Tookit Demo Scene](xrit-demo-scene.png)

*Above: the XR Interaction Toolkit DemoScene Gaze section. The Mouse is used to aim the camera over the Gaze Hover target, and the Cursor aims the Ray Interactor over the Gaze Select & Interactable Override Items.*



## Implementation

The control scheme is implemented with, and designed for, the XR Interaction Toolkit. It won't work with other frameworks such as VRTK.

To implement the controls, we create a dedicated `Ray Interactor` that is controlled by the Main Camera and the Cursor. We also instantiate a Continuous Move Provider for locomotion. Mouse Yaw is applied to the XR Origin, and Pitch to the Camera Offset.

When the user puts on the HMD, the Pitch is reset. Otherwise though the scene remains the same allowing users to manipulate the same space in both 2D and 3D.

Users putting on the HMD is signalled via the `XRNotifications.OnHmdMounted` event. Each XR Subsystem should invoke the appropriate event when this occurs. Integrations are provided for WebXR and PC.



### Action Mappings

The keyboard & mouse actions are mapped via *Unity Action Mappings*. There is a dedicated Action Mappings Asset for the desktop controls. You can modify or clone this as you wish to change the behaviour of the control scheme.

An introduction to Action Mappings is available here, but in brief Action Maps are Assets that associate an abstract input value (such as a float or toggle event) with a specific key or hardware input, decoupling these associations from the code.

![Desktop Controls Action Mappings](action-mappings.png)

An Actions Asset declares the Actions which can be referenced by the code. For the Desktop Controls, we have declared eight actions:

1. Move (Translate the XR Origin)
2. Look (Pitch and Yaw the Camera)
3. Activate (Perform the Activate action of the Ray Interactor)
4. Select (Perform the Select action of the Ray Interactor)
5. Cursor Position (Move the cursor around the screen to direct the Ray)
6. Translate Anchor (Pull or Push a grasped XR Grab Interactable to move it closer or further away)
7. Look Enable (the Look action is enabled)
8. Look Enable Override (an alternative way to enable the Look action)

You can adjust these mappings as you see fit for your own application. For example, above the Look Enable Override is bound to the Space Bar. You can change this by updating the Binding, or add additional bindings alongside it.

Additionally, the `Ray Interactor` Component on the Ray Interactor GameObject can be adjusted. For example, you may want to set the Grabble to Hold, instead of Toggle, here.

Everything except the notifications are implemented in the Prefab. This means its easy to create a Prefab Variant and heavily modify the control scheme to your liking.


## Let us know what you think!

We have play-tested the current scheme, but the only way to know for sure they work well is to try them out in a real application, so if you do end up trying out the controls or making adjustments, please let us know how you find them via [GitHub](https://github.com/UCL-VR/ubiq/discussions), e-mail or [Discord](https://discord.gg/cZYzdcxAAB)!