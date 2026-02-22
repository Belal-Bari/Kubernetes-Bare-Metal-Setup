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
ssh root@server

## Bootstrapping an etcd Cluster
### Install etcd Binaries
Extract and install the etcd server and the etcdctl command line utility:
```bash
{
    mv etcd etcdctl /usr/local/bin/
}
```
### Configure the etcd server
```bash
{
    mkdir -p /etc/etcd /var/lib/etcd
    chmod 700 /var/lib/etcd
    cp ca.crt kube-api-server.key kube-api-server.crt \
        /etc/etcd/
}
```
Each etcd member must have a unique name within an etcd cluster. Set the etcd name to match the hostname of the current compute instance:</br>
Create the etcd.service systemd unit file:
```bash
mv etcd.service /etc/systemd/system/
```
### Start the etcd Server
```bash
{
    systemctl daemon-reload
    systemctl enable etcd
    ststemctl start etcd
}
```
### Verification
List the etcd cluster members:
``` bash
etcdctl member list
```