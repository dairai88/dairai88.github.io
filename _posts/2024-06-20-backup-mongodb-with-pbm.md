---
title: Backup MongoDB with PBM
date: 2024-06-20 21:35:00
categories: [MongoDB, Percona]
tags: [mongo, percona]
---

### Download and install package

```console
wget https://downloads.percona.com/downloads/percona-backup-mongodb/percona-backup-mongodb-2.5.0/binary/debian/jammy/x86_64/percona-backup-mongodb_2.5.0-1.jammy_arm64.deb

sudo dpkg -i percona-backup-mongodb_2.5.0-1.jammy_arm64.deb 
```

### Create the `pbm` user

On primary node

```console
# Create role 
db.getSiblingDB("admin").createRole({ "role": "pbmAnyAction", "privileges": [ { "resource": { "anyResource": true }, "actions": ["anyAction"] }], "roles": [] })

# Create user
db.getSiblingDB("admin").createUser({ user: "pbmuser", "pwd": "secretpwd", "roles": [ { "db": "admin", "role": "readWrite", "collection": "" }, { "db": "admin", "role": "backup" }, { "db": "admin", "role": "clusterMonitor" }, { "db": "admin", "role": "restore" }, { "db": "admin", "role": "pbmAnyAction" }] })
```

Set MongoDB connection URI for pbm-agent

```console
sudo vim /etc/default/pbm-agent

PBM_MONGODB_URI="mongodb://pbmuser:secretpwd@localhost:27017/?authSource=admin"
```

Set MongoDB connection URI for pbm client

```console
vim ~/.bashrc

export PBM_MONGODB_URI="mongodb://pbmuser:secretpwd@localhost:27017/?authSource=admin&replSetName=rs0"
```

### Configure remote backup storage

To config a filesystem based backup stoarge. Make sure pbm-agent has write access to the storage.

```console
sudo mkdir -p /data/local_backups

sudo chown -R mongod:mongod /data/local_backups/
```

```console
vim pbm_config.yaml

storage:
  type: filesystem
  filesystem:
    path: /data/local_backups

```
```console
pbm config --file pbm_config.yaml
```

### Start pbm-agent

```console
sudo systemctl start pbm-agent
```