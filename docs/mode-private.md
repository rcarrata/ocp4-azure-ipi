# IPI Installation - Mode Privated

## Fill the vars

* Edit the vars.yml and customize to fill your needs:

```
vim vars/vars-private.yml
```

## Execute the installation

```
ansible-playbook install-private.yml --vault-password-file .vault-file-password
```

NOTE: This will use the proxy by default as egress / OutboundType. For other egress modes check the egress-firewall, egress-lb and egress-nat documentation.

## Diagram Private Installation (with Proxy as OutboundType)

<img align="center" width="750" src="pics/egress_azure_proxy.png">

The following items are not required or created when you install a private cluster:

* A BaseDomainResourceGroup, since the cluster does not create public records
* Public IP addresses
* Public DNS records
* Public endpoints

## Egress modes [Alternatives]

* [Default with LoadBalancer](/docs/egress-default.md)
* [Azure Firewall](/docs/egress-firewall.md)
* [Nat Gateway](/docs/egress-nat.md)


