# Provisioning Compute Resources
To make sure the adaptors are within the same network check the following:<br/>
In jumpbox:
```bash
ip a
```
Check for ip 10.0.2.15 is 'up'.<br/>
In server:<br/>
Check for ip 10.0.0.11 is 'up'.<br/>
These ip’s were manually set, the instructions are as follows:<br/>
On Jumpbox (<enp0s8> check your adapter):
```bash
ip addr add 10.0.0.10/24 dev enp0s8
ip link set enp0s8 up
```
On Server (<enp0s3> check your adapter):
```bash
ip addr add 10.0.0.11/24 dev enp0s8
ip link set enp0s3 up
```
The above manual setup is temporary and gets un-done if the machine is rebooted, so for permanent static ip do the following:<br/>
Step 1 - Edit the interface file:<br/>
```bash
nano /etc/network/interfaces
```
You should see something as like:
```bash
sourve /etc/network/interfaces.d/*
auto lo
iface lo inet loopback
```
Add your internal network interface configuration below it. For example, if your Internal Network interface is `enp0s8`:
```bash
auto enp0s8
iface enp0s8 inet static
    address 10.0.0.10
    netmask 255.255.255.0
```
Step 2 - Restart Networking:<br/>
```bash
systemctl restart networking
```
*Note: always check that the machines are within same networking interface.*

## Generate and Distribute SSH Keys:

Generate a new SSH key:
```bash
ssh-keygen
```
Copy the SSH public key to each machine:
```bash
while read IP FQDN HOST SUBNET; do  echo "Copying SSH key to $IP...";   ssh-copy-id -i ~/.ssh/my-key-pair.pub root@${IP};  done < machines.txt
```
Test the SSH:
```bash
ssh -i ~/.ssh/my-key-pair root@10.0.0.11
```
## Hostnames
Set the hostnames on each machine listed in the `machines.txt` file:
```bash
while read IP FQDN HOST SUBNET; do
    CMD="sed -i 's/^127.0.1.1.*/127.0.1.1\t${FQDN} ${HOST}/' /etc/hosts"
    ssh -n root@${IP} "$CMD"
    ssh -n root@${IP} hostnamectl set-hostname ${HOST}
    ssh -n root@${IP} systemctl restart systemd-hostnamed
done < machines.txt
```
Verify the hostname is set on each machine:
```bash
while read IP FQDN HOST SUBNET; do
    ssh -n root@${IP} hostname --fqdn
done < machines.txt
```
The following should be displayed:
```bash
server.kubernetes.local
node-0.kubernetes.local
node-1.kubernetes.local
```
## Host Lookup Table
Create a new hosts file and add a header to identify the machines being added:
```bash
echo "" > hosts
echo "# Kubernetes The Hard Way" >> hosts
```
This will allow each machine to be reachable using a hostname such as server, node-0, node-1.
```bash
cd ~/kubernetes-the-hard-way
while read IP FQDN HOST SUBNET; do
    ENTRY="${IP} ${FQDN} ${HOST}"
    echo $ENTRY >> hosts
done < machines.txt
cat hosts
```
## Adding /etc/hosts Entries to a local machine
The DNS entries from the hosts file to the local /etc/hosts file will be appended on the jumpbox machine.
```bash
cd ~/kubernetes-the-hard-way
cat hosts >> /etc/hosts
```
```bash
for host in server node-0 node-1
    do ssh root@${host} hostname
done
```
## Adding /etc/hosts entries to the remote machines
Copy the hosts file to each machine and append the contents to /etc/hosts:
```bash
cd ~/kubernetes-the-hard-way
while read IP FQDN HOST SUBNET; do
    scp hosts root@${HOST}:~/
    ssh -n \
        root@${HOST} "cat hosts >> /etc/hosts"
done < machines.txt
```
## Provisioning a CA and Generating TLS Certificates