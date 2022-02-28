---
title: "XR Interaction"
description: 
lead: 
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu:
  docs:
    parent: "interaction"
weight: 10
toc: true
---

Ubiq includes a straightforward XR interaction framework. This supports high level actions such as Using and Grasping 2D and 3D objects, as well as interacting with the Unity UI system.

Ubiq is not dependent on its own interaction system, and it is expected users may utilise the Unity XR Interaction Toolkit, MRTK, VRTK or another system for advanced functionality.

The Ubiq system is intended to support common XR requirements however, while being very simple to use, and transparently cross-platform. All the Ubiq Samples are created with the Ubiq interaction system.

## Cross Platform Support

The Ubiq interaction system is designed to work on both the desktop and in XR. This is achieved by maintaining two sets of input processing Components, that only respond to keyboard & mouse interactions, and XR controller interactions, respectively.

These Components are designed to co-exist on one set of GameObjects, allowing the same Player Prefab to be used for desktop and XR applications with no change.

For the interactables - 3D objects and 2D controls - identical events are recieved regardless of the source, so user code will work both for XR and the desktop transparently.