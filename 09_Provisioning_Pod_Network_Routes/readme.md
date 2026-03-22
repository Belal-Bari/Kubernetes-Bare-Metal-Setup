# Provisioning Pod Network Routes
Pods scheduled to a node receive an IP address from the node's Pod CIDR range. At this point pods can not communicate with other pods running on different nodes due to missing network routes.</br>
Here routes will be created for each worker node that maps the node's Pod CIDR range to the node's internal IP address.
## The Routing Table
In this section you will gather the information required to create routes in the kubernetes-the-hard-way VPC network.</br>
Print the internal IP address and Pod CIDR range for each worker instance:
```bash
