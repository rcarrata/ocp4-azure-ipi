# Egress Mode - Azure Firewall

## Outbound Type - User Defined Routing - Azure Firewall

The simplest solution to securing outbound addresses lies in use of a firewall device that can control outbound traffic based on domain names. Azure Firewall, for example, can restrict outbound HTTP and HTTPS traffic based on the FQDN of the destination. You can also configure your preferred firewall and security rules to allow these required ports and addresses.

IMPORTANT: Outbound type of UDR requires there is a route for 0.0.0.0/0 and next hop destination of NVA (Network Virtual Appliance) in the route table. The route table already has a default 0.0.0.0/0 to Internet, without a Public IP to SNAT just adding this route will not provide you egress.

When using an outbound type of UDR, a load balancer public IP address for inbound requests is not created unless a service of type loadbalancer is configured. A public IP address for outbound requests is never created by AKS if an outbound type of UDR is set.

## 1 Install and Configure Azure Firewall with Ansible (recommended)

### 1.1 Execute Simple Install

* Simple & Primary Option:

```
ansible-playbook install-private.yml -e "egress=firewall" --vault-password-file .vault-file-password
```

## 1.3 Checking the Azure Firewall

[Check Firewall](/docs/check_firewall)

## 2. Diagram Openshift Install using the Azure Firewall Outbound

<img align="center" width="750" src="/pics/egress_azure_fw.png">

## 3. Create Firewall With AZ Cli (manual mode - not recommended)

```
VNET=$(az network vnet list --query "[?contains(name, '${CLUSTER_NAME}')].{Name:name}" -o tsv)
RG=$(az network vnet list --query "[?contains(name, '${CLUSTER_NAME}')].{ResourceGroup:resourceGroup}" -o tsv)
```

## Create vnet for FW

```
az network vnet create -g ${RG} -n fw-vnet --address-prefix 10.1.0.0/16 --subnet-name AzureFirewallSubnet --subnet-prefix 10.1.1.0/24
```

## Peer vnets
### fw-net -> cluster-net
```
az network vnet peering create -g ${RG} -n fw2cluster --vnet-name fw-vnet --remote-vnet ${VNET} --allow-vnet-access
```

### cluster-net -> fw-net with forwarding
```
az network vnet peering create -g ${RG} -n cluster2fw --vnet-name ${VNET} --remote-vnet fw-vnet  --allow-forwarded-traffic --allow-vnet-access
```

## Create Firewall
```
export FW="myFirewall"
az extension add -n azure-firewall
az network firewall create -g ${RG} -n ${FW} --location eastus
```

NOTE: To leverage FQDN on network rules we need DNS proxy enabled, when enabled the firewall will listen on port 53 and will forward DNS requests to the DNS server specified above. This will allow the firewall to translate that FQDN automatically.

Testing the dns --enable-dns-proxy true

```
az network firewall create -g ${RG} -n ${FW} --location eastus --enable-dns-proxy true
```

## create static IP

```
az network public-ip create --name fw-pip --resource-group ${RG} \
  --location eastus \
  --allocation-method static \
  --sku standard
```

## Configure FW public ip
```
az network firewall ip-config create \
    --firewall-name ${FW} \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group ${RG} \
    --vnet-name fw-vnet
...
{
  "id": "/subscriptions/xxxx/resourceGroups/ocp4-rg/providers/Microsoft.Network/azureFirewalls/myFirewall/azureFirewallIpConfigurations/FW-config",
  "name": "FW-config",
  "privateIpAddress": "10.1.1.4",
  "provisioningState": "Succeeded",
  "publicIpAddress": {
    "id": "/subscriptions/xxxx/resourceGroups/ocp4-rg/providers/Microsoft.Network/publicIPAddresses/fw-pip",
    "resourceGroup": "ocp4-rg"
  },
  "resourceGroup": "ocp4-rg",
  "subnet": {
    "id": "/subscriptions/xxx/resourceGroups/ocp4-rg/providers/Microsoft.Network/virtualNetworks/fw-vnet/subnets/AzureFirewallSubnet",
    "resourceGroup": "ocp4-rg"
  },
  "type": "Microsoft.Network/azureFirewalls/azureFirewallIpConfigurations"
```

## Update config

```
az network firewall update \
    --name ${FW} \
    --resource-group ${RG}
```

# Get private ip of FW

```
fwprivaddr="$(az network firewall ip-config list -g ${RG} -f ${FW} --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

## Create new table route
```
az network route-table create \
    --name Firewall-rt-table \
    --resource-group ${RG} \
    --location eastus \
    --disable-bgp-route-propagation true
```

## Create default route

Azure automatically routes traffic between Azure subnets, virtual networks, and on-premises networks. If you want to change any of Azure's default routing, you do so by creating a route table.

Create an empty route table to be associated with a given subnet. The route table will define the next hop as the Azure Firewall created above. Each subnet can have zero or one route table associated to it.

```
az network route-table route create \
  --resource-group ${RG} \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

## Nat Rule

Put before the assignation of the route table to the bastion subnet for avoiding locking down

```
bastion_private_ip="$(az network nic show -n bastion01 -g ocp4-rg | jq -r .ipConfigurations[0].privateIpAddress)"
```

```
fw_ip="$(az network public-ip show -n fw-pip -g ocp4-rg | jq -r .ipAddress)"
```

```
az network firewall nat-rule create --collection-name access2bastion \
--destination-addresses $fw_pip --destination-ports 22 \
--firewall-name myFirewall --name inboundrule --protocols Any \
--resource-group ocp4-rg --source-addresses '*' \
--translated-port 22 --action Dnat --priority 100 --translated-address $bastion_private_ip
```

## Associate the route table to the cluster subnets
## get subnets from the used vnet
```
subnets=`az network vnet subnet list --vnet-name ${VNET} -g ${RG} -o tsv --query '[].{Name:name}'`
```
#
```
for subnet in ${subnets}
do
az network vnet subnet update \
    --resource-group ${RG} \
    --vnet-name ${VNET} \
    -n ${subnet} \
    --route-table Firewall-rt-table
done
```

NOTE: the bastion subnet needs the route to the azure firewall? Could be without routing through the azure firewall and only with by the regular routing. Check to leave the the bastion subnet outside the route table. If this so, the nat rule for that is not needed any more.

## Configure APP rules

### Get subnet prefixes
```
addressPrefix=`az network vnet subnet list --vnet-name ${VNET} -g ${RG} -o tsv --query '[].{AddressPrefix:addressPrefix}'`
```

### Allow google
```
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name ${FW} \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *google.com \
   --source-addresses ${addressPrefix} \
   --priority 200 \
   --action Allow
```

### Allow azure / microsoft / windows stuff
```
az network firewall application-rule create \
   --collection-name azure_ms \
   --firewall-name ${FW} \
   --name azure \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *azure.com *microsoft.com *microsoftonline.com *windows.net \
   --source-addresses ${addressPrefix} \
   --priority 300 \
   --action Allow
```

### Allow redhat / openshift / quay stuff
```
az network firewall application-rule create \
   --collection-name redhat \
   --firewall-name ${FW} \
   --name redhat \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *redhat.com *redhat.io *quay.io *openshift.com \
   --source-addresses ${addressPrefix} \
   --priority 400 \
   --action Allow
```

### Allow github
```
az network firewall application-rule create \
   --collection-name github \
   --firewall-name ${FW} \
   --name github \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *github.com \
   --source-addresses ${addressPrefix} \
   --priority 500 \
   --action Allow
```

### Allow docker.io
```
az network firewall application-rule create \
   --collection-name docker \
   --firewall-name ${FW} \
   --name docker \
   --protocols Http=80 Https=443 \
   --resource-group ${RG} \
   --target-fqdns *docker.io *docker.com \
   --source-addresses ${addressPrefix} \
   --priority 600 \
   --action Allow
```
