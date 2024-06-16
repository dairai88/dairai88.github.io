---
title: Deploy 3 members cluster
date: 2024-06-15 20:55:00
categories: [MongoDB, Percona]
tags: [mongo, percona]
---

This post is about deploying a cluster with 3 members.

## Server instances

```console
➜  ~ multipass list | grep perconamongo
perconamongo1           Running           192.168.64.2     Ubuntu 22.04 LTS
perconamongo2           Running           192.168.64.3     Ubuntu 22.04 LTS
perconamongo3           Running           192.168.64.4     Ubuntu 22.04 LTS
```

## Install MongoDB on each instance.

Install MongoDB on each instance using instruction in [Deploy Percona MongoDB](/posts/deploy-percona-mongodb/).

> Ensure mongod service on each instance is `stopped` with command `sudo systemctl stop mongod`