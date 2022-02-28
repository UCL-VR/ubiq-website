---
title: "3D Interaction"
description: 
lead: 
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu:
  docs:
    parent: "interaction"
weight: 20
toc: true
---

Interacting with 3D objects is action based. Users can Use or Grasp objects. What these actions do is entirely up to user code.

For example, users could Use a button which spawns an object or turns on a light. They could Grasp a box which attaches to their hand, or a door which swings around an axis.

To implement these behaviours, Components implement IUsable or IGraspable. They will then recieve callbacks to Use()/UnUse() and Grasp()/Release(), respectively.

In XR, Players Use or Grasp objects by putting their controllers on an object and using the Trigger and Grip buttons. On the Desktop, users can use the cursor to click on objects with the Left or Middle mouse buttons.

Components implementing IUsable and IGraspable must be attached to objects with Colliders, though they do not need a RigidBody.

## Hands

The methods of IUsable and IGraspable are passed Hand instances. Hand represents an entity in 3D space (a MonoBehaviour) that can interact with other 3D objects.

Other than existing in 3D, the Hand type is very abstract. It mainly exists to provide a 3D anchor, and to allow Components to distinguish between different controllers. A Hand does not have to have any physical presence (a RigidBody, or Collider).

Implementations of IUsable and IGraspable should be prepared to be used or grasped by multiple Hand instances simultaneously.

## Granspers and Users

Calls to the IUsable and IGraspable implementations are made by the UsableObjectUser and GraspableObjectGrasper Components. These rely on the HandController Component, which implements the XR controller tracking and input processing based on the Unity XR namespace. There are desktop equivalents of UsableObjectUser and GraspableObjectGrasper.

These references are to the concrete types and so it is not currently possible to use UsableObjectUser or GraspableObjectGrasper with custom hand implementations. Though it is possible to re-implement them and pass a custom Hand subclass to the IUsable and IGraspable implementations.