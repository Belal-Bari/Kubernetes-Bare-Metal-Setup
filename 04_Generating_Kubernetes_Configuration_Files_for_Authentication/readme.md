# Generating Kubernetes Configuration Files for Authentication
Kuberbetes client configuration files typically know as kubeconfigs will be generated which configure Kubernetes clients to connect and authenticate to Kubernetes API Servers.

## Client Authentication Configs
In this section kubeconfig files will be generated for the kubelet and the admin user.
## The kubelet Kubernetes Configuration File
When generating kubeconfig files for Kubelets the client certificate matching the Kubelet's node name must be used. This will ensure Kubelets are properly authorized by the Kubernetes Node Authorizer.<br/>
Generate a kubeconfig file for the node-0 and node-1 worker nodes:
``` bash
cd kubernetes-the-hard-way
for host in node-0 node-1; do
    kubectl config set-cluster kubernetes-the-hard-way \
    =ca.crt >   --certificate-authority=ca.crt \
        --embed-certs=true \
        --server=https://server.kubernetes.local:6443 \
        --kubeconfig=${host}.kubeconfig

    kubectl config set-credentials system:node:${host} \
        --client-certificate=${host}.crt \
        --client-key=${host}.key \
        --embed-certs=true
        --kubeconfig=${host}.kubeconfig
    
    kubectl config set-context default \
        --cluster=kubernetes-the-hard-way \
        --user=system:node:${host} \
        --kubeconfig=${host}.kubeconfig
    
    kubectl config use-context default \
        --kubeconfig=${host}.kubeconfig
done
```
## The kube-proxy Kubernetes Configuration File
Generate a kubeconfig file for the kube-proxy service:
```bash
cd ./kubernetes-the-hard-way
{
    kubectl config set-cluster kubernetes-the-hard-way \
        --certificate-authority=ca.crt \
        --embed-certs=true \
        --server=https://server.kubernetes.local:6443 \
        --kubeconfig=kube-proxy.kubeconfig

    kubectl config set-credentials system:kube-proxy \
        --client-certificate=kube-proxy.crt \
        --client-key=kube-proxy.key \
        --embed-certs=true \
        --kubeconfig=kube-proxy.kubeconfig

    kubectl config set-context default \
        --cluster=kubernetes-the-hard-way \
        --user=system:kube-proxy \
        --kubeconfig=kube-proxy.kubeconfig
    
    kubectl config use-context default \
        --kubeconfig=kube-proxy.kubeconfig
}
