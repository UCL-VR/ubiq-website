---
title: "Legacy Versions"
description: ""
lead: ""
date: 2022-01-18T19:58:14+01:00
lastmod: 2022-01-18T19:58:14+01:00
draft: true
images: []
toc: false
weight: 50
---

When breaking changes are made, legacy versions of the server will be checked out with their last supported version number, e.g. `ubiq-0.0.6`, and instances of these will be left running. The ports that they listen on will be incremented with each breaking change.

Old clients will therefore continue to work, though old and new versions cannot communicate with eachother.

Not all versions include breaking changes, so the sequence of legacy versions running is not continuous. The latest version is always `ubiq-master`.

Currently `ubiq-0.0.6` is running on `8001`.