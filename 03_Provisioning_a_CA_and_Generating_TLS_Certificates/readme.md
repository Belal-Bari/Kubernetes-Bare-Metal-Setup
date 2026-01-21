# Provisioning a CA and Generating TLS Certificates
A PKI Infrastructure will be provisioned using openssl to bootstrap a Certificate Authority, and generate TLS certificates for the following components: kube-apiserver, kube-controller-manager, kube-scheduler, kubelet, and kube-proxy. The commands in this section will be run from the jumpbox.
## Certificate Authority
A Certificate Authority will be provisioned that can be used to generate additional TLS certificates for the other Kubernetes components. Setting up CA and generating certificates using openssl can be time-consuming, especially when doing it for the first time. To streamline this lab, I've used an openssl configuration file ca.conf, which defines all the details needed to generate certificates for each Kubernetes component.
```bash
{
    openssl genrsa -out ca.key 4096
    openssl req -x509 -new -sha512 -noenc \
        -key ca.key -days 3653 \
        -config ca.conf \
        -out ca.crt
}
```
Results:
```bash
ca.crt ca.key
```
## Create Client and Server Certificates
A client and server certificates will be generated for each Kubernetes component and a client certificate for the Kubernetes admin user.<br/>
Generate the certificates and private keys:
```bash
certs=(
    "admin" "node-0" "node-1"
    "kube-proxy" "kube-scheduler"
    "kube-controller-manager"
    "kube-api-server"
    "service-accounts"
)
```
```bash
for i in ${certs[*]}; do
    openssl genrsa -out "${i}.key" 4096

    openssl req -new -key "${i}.key" -sha256 \
        -config "ca.conf" -section ${i} \
        -out "${i}.csr"
    
    openssl x509 -req -days 3653 -in "${i}.csr" \
        -copy_extensions copyall \
        -sha256 -CA "ca.crt" \
        -CAkey "ca.key" \
        -CAcreateserial \
        -out "${i}.crt"
done
```
## Distribute the Client and Server Certificates
