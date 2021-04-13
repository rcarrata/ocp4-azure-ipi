# Egress Default Private - Load Balancer

## 1 Install and Configure Azure with Load Balancer as Egress Outbound

```
ansible-playbook install-private.yml -e "egress=Loadbalancer" -e "azure_outboundtype=Loadbalancer" --vault-password-file .vault-file-password
```

## 1.2 Connect to the Private Cluster

* [Connect to the Private Cluster](/docs/connect-private-cluster.md)

## 2. Diagram Openshift Install using the Azure Load Balancer Outbound

<img align="center" width="750" src="pics/egress_azure_lb.png">

The following items are not required or created when you install a private cluster:

* A BaseDomainResourceGroup, since the cluster does not create public records
* Public IP addresses
* Public DNS records
* Public endpoints
