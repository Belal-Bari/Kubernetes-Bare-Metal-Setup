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


