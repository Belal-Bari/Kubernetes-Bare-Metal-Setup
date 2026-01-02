# Kubernetes from scratch - Bare Metal Setup
This repo contains the documentation for the bare metal setup of Kubernetes control plane and worker nodes from scratch.

## Initial setup plan
The setup of the k8 cluster will be done in virtual machines and the planned spec for each virtual machine is given below: <br/>
| Name    | Description     | CPU | RAM   | Storage |
|---------|-----------------|-----|-------|---------|
| Jumpbox | Admin host      | 1   | 512MB | 5GB     |
| Server  | Kub server      | 1   | 1GB   | 5GB     |
| Node-0  | Worker Node-0   | 1   | 1.5GB | 5GB     |
| Node-1  | Worker Node-1   | 1   | 1.5GB | 5GB     |
