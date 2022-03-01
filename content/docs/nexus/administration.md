---
title: "Administration"
description: ""
lead: ""
date: 2022-01-18T19:58:14+01:00
lastmod: 2022-01-18T19:58:14+01:00
draft: true
images: []
toc: false
weight: 30
---

## Administration

Access to `nexus.cs.ucl.ac.uk` is via SSH.

The nodejs process is managed via [pm2](https://pm2.keymetrics.io/). The relevant commands are:

* `pm2 list` (Shows running processes)
* `pm2 log` (Shows the logs)
* `pm2 flush` (Clears the logs)
* `pm2 restart` (Restarts the app, e.g. after 
an update)

pm2 is responsible for restarting the nodejs process after a server restart. To save the state of the tasks that it will try to restore, give the command `pm2 save`.

nodejs and pm2 run under the `node` account. All maintenance should be performed as `node`. The username/password is node/node. It is not possible to SSH directly as node; login with your CS credentials, then change user with `su` (i.e.` $ su node`).

All VECG members who request access will be given sudo permission. All members will be collaborators on the Github repository. Any member can add new members.

## Git

The node account has been given access to the GitHub repository through a [Deploy Key](https://docs.github.com/en/developers/overview/managing-deploy-keys). This is a single-use SSH key associated with the repository.

# New

When cloning a new copy for a branch, you must specify the folder as git will always clone into the repository name by default. For example: `git clone --depth 1 git@github.com:UCL-VR/ubiq.git ubiq-master`

You can specify the branch name for the clone command (`git clone --depth 1 --branch master git@github.com:UCL-VR/ubiq.git ubiq-master`), or checkout the appropriate branch after.

The `--depth 1` command downloads only the `HEAD`, which is all that is needed to run the server.

After cloning, navigate to `~/ubiq-[branch name]/Node/` and issue the commands:

1. `npm install`
2. `pm2 start app.js --name "ubiq-[branch name]"`

The first installs the nodejs dependencies and the second creates the pm2 job with a unique name to identify the instance.

## Updating

To update a checkout, enter the repository and issue `git pull`. `node` cannot write to the repository.