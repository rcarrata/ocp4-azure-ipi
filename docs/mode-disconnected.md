# IPI Installation - Mode Disconnected

## Fill the vars

* Edit the vars-private.yml and customize to fill your needs:

```
vim vars/vars-disconnected.yml
```

## Execute the installation

```
ansible-playbook install-disconnected.yml --vault-password-file .vault-file-password
```

## Diagram for Disconnected Mode

<img align="center" width="750" src="pics/egress_azure_proxy.png">

The following items are not required or created when you install a private cluster:

* A BaseDomainResourceGroup, since the cluster does not create public records
* Public IP addresses
* Public DNS records
* Public endpoints

## OutboundMode

OutboundMode is forced to be "User-Defined Outbound Routing"

```
UserDefinedRouting: Loadbalancer
```

Mode Disconnected won't allow the Outbound Connectivity to Internet.

## Ingress Mode

Public no allowed. Only by VPN (ExpressRoute) or Bastion host is allowed the connection to the API
or the Console.
