# Configuring kubectl for Remote Access
Generate a kubeconfig file for the kubectl command line utility based on the admin user credentials.</br>
Run the commands from the jumpbox machine.

## The Admin Kubernetes Configuration File
Each kubeconfig requires a Kubernetes API Server to connect to.</br>
You should be able to ping server.kubernetes.local based on the /etc/hosts DNS entry.
```bash
curl --cacert ca.crt \
    https://server.kubernetes.local:6443/version
```