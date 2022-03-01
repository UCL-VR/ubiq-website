---
title: "Overview"
description: ""
lead: ""
date: 2022-01-18T19:58:14+01:00
lastmod: 2022-01-18T19:58:14+01:00
draft: true
images: []
toc: false
weight: 20
---

The UCL VECG hosts multiple instances of the rendezvous server on nexus.cs.ucl.ac.uk. Different branches of this repository are checked on nexus and run on different ports.

The checkouts are in `/home/node` and follow the format `ubiq-[branch name]`.

Currently `ubiq-master` is running on `8004`. This is the primary, public server.

It is expected and encouraged that feature branches are created, run on nexus temporarily for development, then removed when no longer needed.

The following sections describe how Nexus is maintained by the VECG team. You do not need to follow this pattern to maintain your own server, but it may be instructive.

