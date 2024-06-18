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

## Modify hosts

Modify `/etc/hosts` to enable these servers communicating with each other via domain names.

```console
192.168.64.2 perconamongo1
192.168.64.3 perconamongo2
192.168.64.4 perconamongo3
```

## Create keyfile used to authenticate among servers

```console
openssl rand -base64 756 > rskeyfile

chmod 400 rskeyfile
```

Copy keyfile to each server

```console
multipass transfer rskeyfile perconamongo1:.
```

In each server, copy rskeyfile to a folder owned by user `mongod`

```console
sudo cp rskeyfile /var/lib/mongodb/
sudo chown mongod:mongod /var/lib/mongodb/rskeyfile
```

### Modify `/etc/mongod.conf` on each server

```console
# network interfaces
net:
  port: 27017
  # bindIp: 127.0.0.1
  bindIpAll: true

security:
  keyFile: /var/lib/mongodb/rskeyfile

#operationProfiling:

replication:
  replSetName: rs0
```