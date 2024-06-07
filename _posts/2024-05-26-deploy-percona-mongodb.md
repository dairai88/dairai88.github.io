---
title: Deploy Percona MongoDB
date: 2024-05-26 20:20:00
categories: [MongoDB, Percona]
tags: [mongo, percona]
---

## Prerequisites

A working Ubuntu 22.04 server. I created one using Multipass utility.

```console
multipass launch -n perconamongo1 jammy
```

## Installation

### Update packages

```console
sudo apt update
```

### Download artifacts

Download MongoDB Shell

```console
wget https://downloads.percona.com/downloads/percona-server-mongodb-7.0/percona-server-mongodb-7.0.8-5/binary/debian/jammy/x86_64/percona-mongodb-mongosh_2.1.5.jammy_arm64.deb
```

Download MongoDB Server

```console
wget https://downloads.percona.com/downloads/percona-server-mongodb-7.0/percona-server-mongodb-7.0.8-5/binary/debian/jammy/x86_64/percona-server-mongodb-server_7.0.8-5.jammy_arm64.deb
```

### Install artifacts

Install MongoDB Shell

```console
sudo dpkg -i percona-mongodb-mongosh_2.1.5.jammy_arm64.deb
```

Install MongoDB Server

```console
sudo dpkg -i percona-server-mongodb-server_7.0.8-5.jammy_arm64.deb
```

### Check mongod status

```console
sudo systemctl status mongod
```

### Start Stop Restart mongod service

```console
sudo systemctl start mongod
```

```console
sudo systemctl stop mongod
```

```console
sudo systemctl restart mongod
```
