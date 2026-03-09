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
