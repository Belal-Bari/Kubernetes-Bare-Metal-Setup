# Bootstrapping the Kubernetes Worker Nodes
The following components will be installed: runc, container networking plugins, containerd, kubelet, and kube-proxy.
**Prerequisites**
The commands in this section must be run from the jumpbox.
Copy the Kubernetes binaries and systemd unit files to each worker instance:
```bash
for HOST in node-0 node-1; do
    SUBNET=$(grep ${HOST} machines.txt | cut -d " " -f 4)
    sed "s|SUBNET|$SUBNET|g" \
        configs/10-bridge.conf > 10-bridge.conf
    sed "s|SUBNET|$SUBNET|g" \
        configs/kubelet-config.yaml > kubelet-config.yaml
    scp 10-bridge.conf kubelet-config.yaml \
    root@{HOST}:~/
done
```
```bash
for HOST in node-0 node-1; do
    scp \
        downloads/worker/* \
        downloads/client/kubectl \
        configs/99-loopback.conf \
        configs/containerd-config.toml \
        configs/kube-proxy-config.yaml \
        units/containerd.service \
        units/kubelet.service \
        units/kube-proxy.service \
        root@${HOST}:~/
done
```
```bash
for HOST in node-0 node-1; do
    scp \
        downloads/cni-plugins/* \
        root@${HOST}:~/cni-plugins/
done
```
The commands in the next section must be run on each worker instance: node-0, node-1. Login to the worker instance using the ssh command. Example:
```bash
ssh root@node-0
```
## Provisioning a Kubernetes Worker Node
Before working in nodes, make sure to setup NAT interface for access to internet.
Install the OS dependencies:
```bash
{
    apt-get update
    apt-get -y install socat conntrack ipset kmod
}
```
The socat binary enables support for the kubectl port-forward command.</br>
Disable Swap</br>
Kubernetes has limited support for the use of swap memory, as it is difficult to provide guarantees and account for pod memory utilization when swap is involved.</br>
Verify if swap is disabled:
```bash
swapon --show
```
Swap not empty, so do the following:
```bash
swapoff -a
swapon --show
```
To ensure swap remains off after reboot consult your Linux distro documentation.</br>
Do the following to make sure swap remains off after reboot:
```bash
nano /etc/fstab
cat /etc/fstab
```
Reload systemd (optional but good practice):
```bash
systemctl daemon-reload
```
Reboot the system and check after reboot:
```bash
reboot

ssh root@node-0

swapon --show
```
Now continue with the process…</br>
Create the installation directories:
```bash
mkdir -p \
    /etc/cni/net.d \
    /opt/cni/bin \
    /var/lib/kubelet \
    /var/lib/kube-proxy \
    /var/lib/kubernetes \
    /var/run/kuberetes
```
Install the worker binaries:
```bash
{
    mv crictl kube-proxy kubelet runc \
        /usr/local/bin/
    mv containerd containerd-shim-runc-v2 containerd-stress /bin/
    mv cni-plugins/* /opt/cni/bin
}
```
## Configure CNI Networking
Create the bridge network configuration file:
```bash
mv 10-bridge.conf 99-loopback.conf /etc/cni/net.d/
```
To ensure network traffic crossing the CNI bridge network is processed by iptables, load and configure the br-netfilter kernel module:
```bash
{
    modprobe br-netfilter
    echo "br-netfilter" >> /etc/modules-load.d/modules.conf
}
```
```bash
{
    echo "net.bridge.bridge-nf-call-iptables = 1" \
        >> /etc/sysctl.d/kubernetes.conf
    echo "net.bridge.bridge-nf-call-ip6tables = 1" \
        >> /etc/sysctl.d/kubernetes.conf
    sysctl -p /etc/sysctl.d/kubernetes.conf
}
```
## Configure containerd
Install the containerd configuration files:
```bash
{
    mkdir -p /etc/containerd/
    mv containerd-config.toml /etc/containerd/config.toml
    mv containerd.service /etc/systemd/
}
```
## Configure the Kubelet
Create the kubelet-config.yaml configuration file:
```bash
{
    mv kubelet-config.yaml /var/lib/kubelet/
    mv kubelet.service /etc/systemd/system/
}
```
## Configure the Kubernetes Proxy
```bash
{
    mv kubelet-config.yaml /var/lib/kublet/
    mv kubelet.service /etc/systemd/system/
}