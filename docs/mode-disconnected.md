# IPI Installation - Mode Disconnected (Under Reconstruction)

AirGapped in IPI is NOT Supported, because of lack of [Virtual Service Endpoints for Azure Resource Manager]()

## Fill the vars

* Edit the vars-private.yml and customize to fill your needs:

```
vim vars/vars-disconnected.yml
```

## Execute the installation

```
ansible-playbook install-disconnected.yml --vault-password-file .vault-file-password
```

## Diagram for Disconnected Mode (Ongoing)

```
To be Modified.
```

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
