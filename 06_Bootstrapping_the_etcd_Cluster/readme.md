# Bootstrapping the etcd cluster
## Prerequisites
Copy etcd binaries and systemd unit files to the server machine:
```bash
cd kubernetes-the-hard-way
scp \
    downloads/controller/etcd \
    downloads/client/etcdctl \
    units/etcd.service \
    root@server:~/
```
The commands in this lab must be run on the server machine. Login to the server machine using the ssh command. Example:
Ssh root@server
