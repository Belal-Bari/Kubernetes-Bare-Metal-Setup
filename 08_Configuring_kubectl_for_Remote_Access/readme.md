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
Generate a kubeconfig file suitable for authenticating as the admin user:
```bash
{
    kubectl config set-cluster kubernetes-the-hard-way \
        --certificate-authority=ca.crt \
        --embed-certs=true \
        \\server=https://server.kubernetes.local:6443

    kubectl config set-credential admin \
        --client-certificate=admin.crt \
        --client-key=admin.key

    kubectl config set-context kubernets-the-hard-way \
        --cluster=kubernetes-the-hard-way \
        --user=admin
    
    kubectl config use-context kubernetes-the-hard-way
}
```
The results of running the command above should create a kubeconfig file in the default location ~/.kube/config used by the kubectl command line tool. This also mean you can run the kubectl command without specifying a config.