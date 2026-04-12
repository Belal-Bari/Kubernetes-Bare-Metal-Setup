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

## Deployments

In this section you will verify the ability to create and manage Deployments.</br>
Create a deployment for the nginx web server:
```bash
kubectl create deployment nginx \
    --image=nginx:latest
```
```bash
kubectl get deployment
```
List the pod created by the nginx deployment:
```bash
kubectl get pods -l app=nginx
```
## Port Forwarding
In this section you will verify the ability to access applications remotely using port forwarding.
```bash
kubectl port-forward nginx-54c98b4f84-vf86g 8080:80
```
```bash
curl --head http://127.0.0.1:8080
```

## Logs
In this secction you will verify the ability to retrieve container logs.</br>
Print the nginx pod logs:
```bash
kubectl logs nginx-54c98b4f84-vf86g
```

## Exec

In this section you will verify the ability to execute commands in a container.</br>