# Smoke Test
A series of tasks will be conducted to ensure your Kubernetes cluster is functioning correctly.

## Data Encryption
In this section the ability to encrypt secret data at rest will be verified.</br>
Create a generic secret:
```bash
kubectl create secret generic kubernetes-the-hard-way \
    --from-literal="mykey=mydata"
```