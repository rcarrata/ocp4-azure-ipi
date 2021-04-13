# Openshift 4 Egress in Disconnected Mode

 <img align="center" width="750" src="pics/egress_azure_disconnected.png">

The following items are not required or created when you install a private cluster:

* A BaseDomainResourceGroup, since the cluster does not create public records
* Public IP addresses
* Public DNS records
* Public endpoints
