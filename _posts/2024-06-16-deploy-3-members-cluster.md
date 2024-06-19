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

### Restart each server

Restart each mongod instance server.

```console
sudo systemctl start mongod
```

Initiate on perconamongo1 server

```console
mongosh

rs.initiate()
```

perconamongo1 server becomes primary instance.

### Create a user to manager cluster

Once mongod server enable authentication via security keyfile, a user capable of managing cluster is required to modify the cluster

```console
rs0 [direct: primary] test> admin = db.getSiblingDB("admin")
admin
rs0 [direct: primary] test> admin.createUser(
... {
...   user: "dalei",
...   pwd: passwordPrompt(),
...   roles: [
...    { role: "userAdminAnyDatabase", db: "admin" },
...    { role: "clusterAdmin", db: "admin" }
...   ]
... })
Enter password
oarnud9I
********{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1718717893, i: 2 }),
    signature: {
      hash: Binary.createFromBase64('e0NXGPYHKZRTz4z5/k+oIiVNY/k=', 0),
      keyId: Long('7381782062824423430')
    }
  },
  operationTime: Timestamp({ t: 1718717893, i: 2 })
}
```

Exit mongosh and Re-Login with auth

```console
rs0 [direct: primary] test> admin = db.getSiblingDB("admin")
admin
rs0 [direct: primary] test> admin.auth("dalei", passwordPrompt())
Enter password
oarnud9I
********{ ok: 1 }
```

Add perconamongo2 and perconamongo3

```console
rs0 [direct: primary] test> rs.add("perconamongo2:27017")
{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1718718135, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('M/HBRs93S/J4LBbeU85I7o6EPdY=', 0),
      keyId: Long('7381782062824423430')
    }
  },
  operationTime: Timestamp({ t: 1718718135, i: 1 })
}
rs0 [direct: primary] test> rs.add("perconamongo3:27017")
{
  ok: 1,
  '$clusterTime': {
    clusterTime: Timestamp({ t: 1718718142, i: 1 }),
    signature: {
      hash: Binary.createFromBase64('pEfFyiBb/OGqQYre7qQve2xgPQ8=', 0),
      keyId: Long('7381782062824423430')
    }
  },
  operationTime: Timestamp({ t: 1718718142, i: 1 })
}
```

Verify cluster

```console
rs.status()
```

Verify on perconamongo2 and perconamongo3 server

```console
mongosh --authenticationDatabase "admin" -u dalei -p
```

### Create an user to read write any database

```console
use admin

db.createUser({
    user: "demo",
    pwd: "demo",
    roles: [
        { role: "readWriteAnyDatabase", db: "admin" }
    ]
})
```