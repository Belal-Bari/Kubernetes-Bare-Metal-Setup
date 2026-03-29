# Provisioning Pod Network Routes
Pods scheduled to a node receive an IP address from the node's Pod CIDR range. At this point pods can not communicate with other pods running on different nodes due to missing network routes.</br>
Here routes will be created for each worker node that maps the node's Pod CIDR range to the node's internal IP address.
## The Routing Table
In this section you will gather the information required to create routes in the kubernetes-the-hard-way VPC network.</br>
Print the internal IP address and Pod CIDR range for each worker instance:
```bash
{
    SERVER_IP=$(grep server machines.txt | cut -d " " -f 1)
    NODE_0_IP=$(grep node-0 machines.txt | cut -d " " -f 1)
    NODE_0_SUBNET=$(grep node-0 machines.txt | cut -d " " -f 4)
    NODE_1_IP=$(grep node-1 machines.txt | cut -d " " -f 1)
    NODE_1_SUBNET=$(grep node-1 machines.txt | cut -d " " -f 4)
}
```
```bash
ssh root@server <<EOF 
    ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
    ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF
```
```bash
ssh root@node-0 <<EOF
    ip route add ${NODE_1_SUBNET} via ${NODE_1_IP}
EOF
```
```bash
ssh root@node-1 <<EOF
    ip route add ${NODE_0_SUBNET} via ${NODE_0_IP}
EOF
```