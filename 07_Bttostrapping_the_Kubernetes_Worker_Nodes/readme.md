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
