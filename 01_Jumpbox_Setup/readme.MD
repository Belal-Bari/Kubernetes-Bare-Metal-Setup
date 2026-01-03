# Setup the Jumpbox
From within the jumpbox (inside the VM) edit the file /etc/ssh/sshd_config and set the following: <br/>
`PermitRootLogin yes` <br/>
`PasswordAuthentication yes` <br/>
Save file and restart SSH: <br/>
`sudo systemctl restart ssh`<br/>
Check status:<br/>
`sudo systemctl status ssh`<br/>
Inside VM check port forwarding:<br/>
`ss -tlnp | grep ssh`<br/>
For convenience the host ip is kept blank which listens on 0.0.0.0/0<br/>
Now from powershell the following allows access to jumpbox:<br/>
`ssh -p 2222 root@127.0.0.1`
## Install command line utilities
After logging in the jumpbox as root user the following will be installed:<br/>
`apt-get update`<br/>
`apt-get -y install wget curl vim openssl git`<br/>
Files which are going to be used for building Kubernetes cluster will be downloaded from git repository:
```bash
git clone --depth 1 \
  https://github.com/kelseyhightower/kubernetes-the-hard-way.git
```
## Download Binaries


