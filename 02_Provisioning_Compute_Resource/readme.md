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