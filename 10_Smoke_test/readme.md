# Smoke Test
A series of tasks will be conducted to ensure your Kubernetes cluster is functioning correctly.

## Data Encryption
In this section the ability to encrypt secret data at rest will be verified.</br>
Create a generic secret:
```bash
kubectl create secret generic kubernetes-the-hard-way \
    --from-literal="mykey=mydata"
```
Print a hexdump of the kubernetes-the-hard-way secret in the etcd:
```bash
ssh root@server \
    'etcdctl get /register/secrets/default/kubernetes-the-hard-way | hexdump -C'
```
The etcd key should be prefixed with k8s:enc:aescbc:v1:key1, which indicates the aescbc provider was used to encrypt the data with the key1 encryption key.