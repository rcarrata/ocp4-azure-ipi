# Openshift 4 Azure IPI installation

Ansible Repository for deploy Openshift 4 clusters with IPI installation in connected, private and disconnected mode

There are different approaches for each mode, but always an IPI installation is used.

 <img align="center" width="450" src="docs/pics/azure_ocp4_pic.png">

## 1. Prereqs

* [Prerequisites Doc](/docs/prereqs.md)

## 2. Installation Modes

* [Connected Installation](/docs/mode-connected.md)
* [Private Installation](/docs/mode-private.md)
* [Disconnected Installation](/docs/mode-disconnected.md)

## 3. Egress/Outbound modes

### 3.1 Connected/Public Openshift cluster Egress/Outbound mode

By default in the Connected/Public Clusters the Egress / Outbound connections to Internet is
performed using the LoadBalancer Egress mode:

* [Default with LoadBalancer](/docs/egress-default.md)

### 3.2 Private Openshift cluster Egress/Outbound mode

By default in the Private / Public Clusters the OutboundType is LoadBalancer, but other
possibilities can be used for the Egress/Outbound mode

* [Default with LoadBalancer](/docs/egress-default.md)
* [Proxy in Bastion Host](/docs/egress-proxy.md)
* [Azure Firewall](/docs/egress-firewall.md)
* [Nat Gateway](/docs/egress-nat.md)

### 3.3 Disconnected Openshift Cluster Egress/Outbound mode

By default NO egress/outbound is allowed to Internet because is a Disconnected environment

* [Nat Gateway](/docs/egress-disconnected.md)

## 4. Ingress Modes

* [Ingress in Public/Connected Mode](/docs/ingress-connected.md)
* [Ingress in Private Mode](/docs/ingress-private.md)
* [Ingress in Disconnected Mode](/docs/ingress-disconnected.md)
