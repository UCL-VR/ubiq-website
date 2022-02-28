---
title: "Setting Up Ubiq for XR"
description: 
lead: 
date: 2022-02-05T20:40:38+01:00
lastmod: 2022-02-05T20:40:38+01:00
draft: false
images: []
menu:
  docs:
    parent: "getting-started"
weight: 515
toc: true
---

## Oculus or Windows Mixed Reality (Desktop)

1. In Unity, open the project settings window (Edit/Project Settings...) and go to the XR Plug-in Management menu.

2. Enable the plug-in here - just tick either the Oculus or Windows Mixed Reality box. Also tick the box for "Initialize XR on Startup".

![xr-plugin-management](xr-plugin-management.png)

## OpenVR (Desktop)

1. Note that while rendering and tracking works well, this subsystem is currently missing input from the hand controllers. Unfortunately, this is a limitation with the plugin and a fix does not seem to be on the horizon.

2. Follow the instructions to download the OpenVR Unity XR plug-in here: [https://github.com/ValveSoftware/unity-xr-plugin/releases/tag/installer](https://github.com/ValveSoftware/unity-xr-plugin/releases/tag/installer)



## Oculus Quest

1. Install android build tools. In Unity Hub, click Installs on the left-hand menu. Click the three dots in the top right corner of the box for your Unity 2019.4.x installation and select Add modules from the dropdown. Select Android Build Support and both subsequent options (Android SDK & NDK Tools and OpenJDK). Wait for installation to complete, then re-open your project.

![add-modules](add-modules.png)

2. In Unity, open the project settings window (Edit/Project Settings...) and go to the XR Plug-in Management menu.

3. Click the Android tab and check the boxes for Oculus and "Initialize XR on Startup".

![xr-plugin-management2](xr-plugin-management2.png)

4. (Optional) To create your first build for the Quest, follow the Oculus Enable Device for Development and Testing guide: [https://developer.oculus.com/documentation/unity/unity-enable-device/](https://developer.oculus.com/documentation/unity/unity-enable-device/)


