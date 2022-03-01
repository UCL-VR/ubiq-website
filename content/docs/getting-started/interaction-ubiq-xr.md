---
title: "Interaction with Ubiq XR"
description:
lead: 
date: 2020-11-12T20:12:04+01:00
lastmod: 2020-11-12T20:12:04+01:00
draft: false
images: []
menu:
  docs:
    parent: "getting-started"
weight: 560
toc: true
---

## Making an Object Graspable

If you want to have your object being grabbable with the user's hands, you will have to inherit from IGraspable and implement Grasp(...) and Release(...).

## Making an Object Usable

If you want your object to do something when the user presses the trigger button while holding it, you need to inherit from IUsable and implement Use(...) and UnUse(...).

