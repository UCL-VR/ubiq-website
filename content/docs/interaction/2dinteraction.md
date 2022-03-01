---
title: "2D Interaction"
description: 
lead: 
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu:
  docs:
    parent: "interaction"
weight: 30
toc: true
---

The Ubiq XR interaction integrates with Unity's UI system. Players can raycast from their hands to interact with Unity Canvases and controls.

To enable Ubiq XR interaction with a Canvas, add the `XRUICanvas` component to it. Once this Component is added users can interact with the Unity controls using raycasts from the controllers, or the mouse cursor on the desktop.

When using the `XRUICanvas` an `EventSystem` is no longer required. Cameras are not required either on World Space Canvases, allowing them to be declared in Prefabs and instantiated dynamically.

## Raycasters

The 2D and 3D interaction mechanisms are separate. UI interaction is performed through UIRaycasters. There are XR and Desktop raycasters (`XRUIRaycaster` and `DesktopUIRaycaster`). Instances of both are attached to the sample Player Prefab.