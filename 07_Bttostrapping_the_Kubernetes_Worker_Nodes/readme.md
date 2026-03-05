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