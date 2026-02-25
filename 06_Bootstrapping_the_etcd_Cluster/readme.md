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
## Bootstrapping the Kubernetes Control Plane
The following components will be installed on the server machine: Kubernetes API Server, Scheduler, and Controller Manager.
### Prerequisites
Connect to the jumpbox and copy Kubernetes binaries and systemd unit files to the server machine:
``` bash
scp \
    downloads/controller/kube-apiserver \
    downloads/controller/kube-controller-manager \
    downloads/controller/kube-scheduler \
    downloads/client/kubectl \
    units/kube-apiserver.service \
    units/kube-controller-manager.service \
    units/kube-scheduler.service \
    configs/kube-scheduler.yaml \
    configs/kube-apiserver-to-kubelet.yaml \
    root@server:~/
```
The commands next must be run on the server machine. Login to the server machine using the ssh command. Example:
```bash
ssh -i ~/.ssh/my-key-pair root@server
```
## Provision the Kuberetes Control Plane
Create the Kubernetes configuration directory:
```bash
mkdir -p /etc/kubernetes/config
```
## Install the kubernetes Controller Binaries
Install the Kubernetes binaries:
```bash
{
    mv kube-apiserver \
        kube-controller-manager \
        kube-scheduler kubectl \
        /usr/local/bin/
}
```
## Configure the Kubernetes API Server
```bash
{
    mkdir -p /var/lib/kubernetes/

    mv ca.crt ca.key \
        kube-api-server.key kube-api-server.crt \
        service-accounts.key service-accounts.crt \
        encryption-config.yaml \
        /var/lib/kubernetes/
}
```
Create the kube-apiserver.service systemd unit file:
```bash
mv kube-apiserver.service \
    /etc/systemd/system/kube-apiserver.service
```
## Configure the Kubernetes Controller Manager
Move the kube-controller-manager kubeconfig into place:
```bash
mv kube-controller-manager.kubeconfig /var/lib/kubernetes/
```
Create the kube-controller-manager.service systemd unit file:
```bash
mv kube-controller-manager.service /etc/systemd/system/
```
## Configure the Kubernetes Scheduler
Move the kube-scheduler kubeconfig into place:
```bash
mv kube-scheduler.kubeconfig /var/lib/kubernetes/
```
Create the kube-scheduler.yaml configuration file:
```bash
mv kube-scheduler.yaml /etc/kubernetes/config/
```
Create the kube-scheduler.service systemd unit file:
```bash
mv kube-scheduler.service /etc/systemd/system/
```
## Start the Controller Service
```bash
{
    systemctl daemon-reload

    systemctl enable kube-apiserver \
        kube-controller-manager kube-scheduler
    
    systemctl start kube-apiserver \
        kube-controller-manager kube-scheduler
}
```
Allow up to 10 seconds for the Kubernetes API Server to fully initialize.</br>
You can check if any of the control plane components are active using the systemctl command. For example, to check if the kube-apiserver fully initialized, and active, run the following command:
```bash
systemvtl is-active kube-apiserver
```
For a more detailed status check, which includes additional process information and log messages, use the systemctl status command:
```bash
systemctl status kube-apiserver
```
## Verification