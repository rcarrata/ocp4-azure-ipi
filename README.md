# Openshift 4 Azure IPI installation

Ansible Repository for deploy Openshift 4 clusters with IPI installation in connected, private and disconnected mode

There are different approaches for each mode, but always an IPI installation is used.

 <img align="center" width="450" src="docs/pics/azure_ocp4_pic.png">

## Prereqs

* [Prerequisites Doc](/docs/prereqs.md)

## Installation Modes

* [Connected Installation](/docs/mode-connected.md)
* [Private Installation](/docs/mode-private.md)
* [Disconnected Installation](/docs/mode-disconnected.md)

## Egress modes

* [Default with LoadBalancer](/docs/egress-default.md)
* [Proxy in Bastion Host](/docs/egress-proxy.md)
* [Azure Firewall](/docs/egress-firewall.md)
* [Nat Gateway](/docs/egress-nat.md)

## Ingress Modes

* [Ingress in Public/Connected Mode](/docs/ingress-connected.md)
* [Ingress in Private Mode](/docs/ingress-private.md)
