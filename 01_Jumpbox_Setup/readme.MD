# Setup the Jumpbox
From within the jumpbox (inside the VM) edit the file /etc/ssh/sshd_config and set the following: <br/>
```bash
PermitRootLogin yes
PasswordAuthentication yes
```
Save file and restart SSH:
```bash
sudo systemctl restart ssh
```
Check status:
```bash
sudo systemctl status ssh
```
Inside VM check port forwarding:
```bash
ss -tlnp | grep ssh
```
For convenience the host ip is kept blank which listens on 0.0.0.0/0<br/>

Now from powershell the following allows access to jumpbox:
```bash
ssh -p 2222 root@127.0.0.1
```
## Install command line utilities
After logging in the jumpbox as root user the following will be installed:
```bash
apt-get update
apt-get -y install wget curl vim openssl git
```
Files which are going to be used for building Kubernetes cluster will be downloaded from git repository:
```bash
git clone --depth 1 \
  https://github.com/kelseyhightower/kubernetes-the-hard-way.git
```
## Download Binaries
The following command will be used to download required binaries inside the downloads folder:
```bash
wget -q --show-progress \
  --https-only \
  --timestamping \
  -P downloads \
  -i downloads-$(dpkg --print-architecture).txt
```
Extract the component binaries from the release archives and organize them under the downloads directory.
```bash
{
  ARCH=$(dpkg --print-architecture)
  mkdir -p downloads/{client,cni-plugins,controller,worker}
  tar -xvf downloads/crictl-v1.32.0-linux-${ARCH}.tar.gz \
    -C downloads/worker/
  tar -xvf downloads/containerd-2.1.0-beta.0-linux-${ARCH}.tar.gz \
    --strip-components 1 \
    -C downloads/worker/
  tar -xvf downloads/cni-plugins-linux-${ARCH}-v1.6.2.tgz \
    -C downloads/cni-plugins/
  tar -xvf downloads/etcd-v3.6.0-rc.3-linux-${ARCH}.tar.gz \
    -C downloads/ \
    --strip-components 1 \
    etcd-v3.6.0-rc.3-linux-${ARCH}/etcdctl \
    etcd-v3.6.0-rc.3-linux-${ARCH}/etcd
  mv downloads/{etcdctl,kubectl} downloads/client/
  mv downloads/{etcd,kube-apiserver,kube-controller-manager,kube-scheduler} \
    downloads/controller/
  mv downloads/{kubelet,kube-proxy} downloads/worker/
  mv downloads/runc.${ARCH} downloads/worker/runc
}
rm -rf downloads/*gz
```
Make the binaries executable:
```bash
chmod +x downloads/{client,cni-plugins,controller,worker}/*
```
## Install kubectl
```bash
cp downloads/client/kubectl /usr/local/bin/
```
Check verison:
```bash
~/kubernetes-the-hard-way/downloads/client version --client
```

After this step go to Provisioning Compute Resource

